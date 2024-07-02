**HAproxy负载均衡**

# 1、介绍

HAProxy（高性能-代理）目前是一款使用广泛的软负载均衡器，它可以实现TCP/HTTP的负载均衡。

- 特性：

- 免费开源

- 最大并发量能达到5w

- 支持多种负载均衡算法，同时支持session保持

- 支持虚拟主机

- 拥有服务监控页面，可以了解系统的实时运行状态

- 通常不做正向代理，有更好的选择（Squid）

- 通常不做缓存代理，有更好的选择（Varnish）

- 不会改变请求和响应报文

- 不用于Web服务器

- 不是基于数据包的负载均衡器，看不到ip数据包

# 2、安装部署

## 1）安装

```
[root@node1 ~]# yum install haproxy -y
/etc/haproxy/haproxy.cfg # 主配置文件
/usr/lib/systemd/system/haproxy.service # 守护进程服务
默认配置：
[root@node1 ~]# cat /etc/haproxy/haproxy.cfg
```

## 2）全局配置

```
log 定义全局的syslog服务器
chroot 定义工作目录
pidfile 指定PID
maxconn 定义最大并发数
user 用户名
group 组名
daemon 以守护进程方式工作于后台
```

## 3）默认配置：用于为所有其它配置段提供默认参数，这配置默认配置参数可由下一个“defaults”所重新设定。

```
mode 运行模式或协议
log 定义每个实例启用事件和流量日志
option httplog
option dontlognull
option http-server-close
option forwardfor except 127.0.0.0/8
option redispatch
retries 3
timeout http-request 10s
timeout queue 1m
timeout connect 10s
timeout client 1m
timeout server 1m
timeout http-keep-alive 10s
timeout check 10s
maxconn 定义最大并发数
```

## 4）前端配置：定义一系列监听的套接字，这些套接字可接受客户端请求并与之建立连接

```
frontend main *:5000
 acl url_static path_beg -i /static /images /javascript /stylesheets # 测试
请
求的URL是否以指定的模式开头
 acl url_static path_end -i .jpg .gif .png .css .js # 测试请求的URL是否以
<string>指定的模式结尾
 use_backend static if url_static
 default_backend app # 指定后端的名称（当use_backend 没能匹配）
```

## 5）后端配置：定义一系列“后端”服务器，代理将会将对应客户端的请求转发至这些服务器

```
backend static
 balance roundrobin
 server static 127.0.0.1:4331 check
backend app
 balance roundrobin
 server app1 127.0.0.1:5001 check
 server app2 127.0.0.1:5002 check
 server app3 127.0.0.1:5003 check
 server app4 127.0.0.1:5004 check
```

# 3、简单实验

```
[root@nginx1 ~]# cat /etc/haproxy/haproxy.cfg 
global
 log     127.0.0.1 local2
  chroot   /var/lib/haproxy
 pidfile   /var/run/haproxy.pid
 maxconn   4000
 user    haproxy
 group    haproxy
 daemon
 stats socket /var/lib/haproxy/stats
defaults
 mode          http
 log           global
 option         httplog
 option         dontlognull
 option http-server-close
 option forwardfor    except 127.0.0.0/8
 option         redispatch
 retries         3
 timeout http-request  10s
 timeout queue      1m
 timeout connect     10s
 timeout client     1m
 timeout server     1m
 timeout http-keep-alive 10s
 timeout check      10s
 maxconn         3000
frontend main *:5000
 acl url_static    path_beg    -i /static /images /javascript
/stylesheets
 acl url_static    path_end    -i .jpg .gif .png .css .js .txt
 use_backend static      if url_static
 default_backend       app
backend static
 balance   roundrobin
 server   static 192.168.80.30:80 check
backend app
 balance   roundrobin
 server app1 192.168.80.20:80 check
listen mysql_proxy
 bind 0.0.0.0:3306
 mode tcp
 balance source
 server mysqldb1 192.168.80.30:3306 check
listen ssh_proxy
 bind 0.0.0.0:22222
 mode tcp
 balance roundrobin
 server sshserver1 192.168.80.20:22 check
 server sshserver2 192.168.80.30:22 check
```