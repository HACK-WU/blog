**Nginx负载均衡**

# 1、介绍

负载均衡（Server Load Balancer）是将访问流量根据转发策略分发到后端多台 ECS 的流量分发控制服

务。负载均衡可 以通过流量分发扩展应用系统对外的服务能力，通过消除单点故障提升应用系统的可用

性。 负载均衡主要有如下几个功能点：

- 负载均衡服务通过设置虚拟服务地址（IP），将位于同一地域（Region）的多台云服务器（Elastic

Compute Service，简称ECS）资源虚拟成一个高性能、高可用的应用服务池；根据应用指定的方

式，将来自客户端的网络请 求分发到云服务器池中。

- 负载均衡服务会检查云服务器池中ECS的健康状态，自动隔离异常状态的ECS，从而解决了单台ECS

的单点问题，同 时提高了应用的整体服务能力。在标准的负载均衡功能之外，负载均衡服务还具备

TCP与HTTP抗DDoS攻击的特 性，增强了应用服务器的防护能力。

- 负载均衡服务是ECS面向多机方案的一个配套服务，需要同ECS结合使用。

# 2、ngx_http_upstream_module模块

相关参数：

```
upstream：        定义后端服务器组，会引入一个新的上下文
server：        在upstream上下文中server成员，以及相关的参数
least_conn：    最少连接调度算法，当server拥有不同的权重时其为wlc
ip_hash：        源地址hash调度方法
hash ：          基于指定的key的hash表来实现对请求的调度，此处的key可以直接文本、变量或二者的组合
keepalive connections;：为每个worker进程保留的空闲的长连接数量；
weight=number     # 权重，默认为1；
max_fails=number     # 失败尝试最大次数；超出此处指定的次数时，server将被标记为不可用；
fail_timeout=time     # 设置将服务器标记为不可用状态的超时时长；
max_conns         # 当前的服务器的最大并发连接数；
backup         # 将服务器标记为“备用”，即所有服务器均不可用时此服务器才启用；
down             # 标记为“不可用”
```

# 3、七层负载均衡实验

```
node1作为反向代理服务器并配置七层代理
node2和node3作为web站点
[root@node1 ~]# cat /etc/nginx/conf.d/proxy.conf
upstream websers{
server 192.168.10.20;
server 192.168.10.30;
}
server{
listen 8080;
server_name 172.16.0.10:8080;
location / {
proxy_pass http://websers;
}
}
测试：
1.并发访问htt://172.16.0.10:8080
```

# 4、四层负载均衡实验

```
[root@node1 ~]# cat /etc/nginx/nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
include /usr/share/nginx/modules/*.conf;
events {
worker_connections 1024;
}
stream {
upstream sshsers{
server 192.168.10.20:22;
server 192.168.10.30:22;
least_conn;
}
server{
listen 172.16.0.10:22222;
proxy_pass sshsers;
}
}
测试：
1. 通过ssh root@172.16.0.10 22222测试
# 官网示例
stream {
 upstream sshservers {
 server 192.168.80.20:22;
 server 192.168.80.30:22;
 } 
 upstream mysql_server {
 server 192.168.80.30:3306;
 } 
 server {
   listen 22222;
   proxy_connect_timeout 1s;
   proxy_timeout 3s;
   proxy_pass sshservers;
 } 
 server {
   listen 33306;
   proxy_timeout 20s;
   proxy_pass mysql_server;
 } 
}
```

# 5、负载均衡算法

- 轮询(默认)： 每个请求按时间顺序逐一分配到不同的后端服务，如果后端某台服务器死机，自动剔

除故障系统，使 用户访问不受影响。

- weight（轮询权值）：weight的值越大分配到的访问概率越高，主要用于后端每台服务器性能不均

衡的情况下。或 者仅仅为在主从的情况下设置不同的权值，达到合理有效的地利用主机资源

- ip_hash：每个请求按访问IP的哈希结果分配，使来自同一个IP的访客固定访问一台后端服务器，

并且可以有效解决 动态网页存在的session共享问题。

- fair：比 weight、ip_hash更加智能的负载均衡算法，fair算法可以根据页面大小和加载时间长短智

能地进行负载均 衡，也就是根据后端服务器的响应时间 来分配请求，响应时间短的优先分配。

Nginx本身不支持fair，如果需要这 种调度算法，则必须安装upstream_fair模块。

- url_hash：按访问的URL的哈希结果来分配请求，使每个URL定向到一台后端服务器，可以进一步

提高后端缓存服 务器的效率。Nginx本身不支持url_hash，如果需要这种调度算法，则必须安装

Nginx的hash软件包。