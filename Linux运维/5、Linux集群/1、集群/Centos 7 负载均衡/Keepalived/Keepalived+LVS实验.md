**Keepalived+LVS实验**

# 1、环境准备

```
node1（DS1）：192.168.10.10
node2（DS2）：192.168.10.20
node3（RS1）：192.168.10.30
node4（RS2）：192.168.10.40
VIP：192.168.10.99（单主模型）
```

# 2、RS部署

```
在node3和node4执行下面的脚本：
#!/bin/bash
yum install net-tools httpd -y
systemctl stop firewalld
setenforce 0
vip="192.168.10.99"
mask="255.255.255.255"
ifconfig lo:0 $vip broadcast $vip netmask $mask up
route add -host $vip lo:0
echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
echo "<h1>This is RS1</h1>" > /var/www/html/index.html # 修改不同的主页以便测
试！
systemctl start httpd
```

# 2、DR部署

```
[root@node1 ~]# yum install keepalived ipvsadm -y
[root@node1 ~]# systemctl stop firewalld
[root@node1 ~]# setenforce 0
[root@node1 keepalived]# mv keepalived.conf{,.bak}
[root@node1 keepalived]# cat keepalived.conf
! Configuration File for keepalived
global_defs {
 router_id node1 # 设置lvs的id，一个网络中应该唯一
}
vrrp_instance VI_1 {
 state MASTER # 指定Keepalived的角色
 interface ens33 # 网卡
 virtual_router_id 10 # 虚拟路由器ID，主备需要一样
 priority 100 # 优先级越大越优，backup路由器需要设置比这小！
 advert_int 1 # 检查间隔1s
 authentication {
 auth_type PASS
 auth_pass 1111
 }
 virtual_ipaddress {
  192.168.10.99 # 定义虚拟IP地址，可以定义多个
 }
}
# 定义虚拟主机，对外服务的IP和port
virtual_server 192.168.10.99 80 {
 delay_loop 6 # 设置健康检查时间，单位是秒
 lb_algo wrr # 负责调度算法
 lb_kind DR # LVS负载均衡机制
 persistence_timeout 0
 protocol TCP
# 指定RS主机IP和port
 real_server 192.168.10.30 80 {
 weight 2
# 定义TCP健康检查
TCP_CHECK {
 connect_timeout 10
 nb_get_retry 3
 delay_before_retry 3
 connect_port 80
 }
}
real_server 192.168.10.40 80 {
 weight 1
 TCP_CHECK {
 connect_timeout 10
 nb_get_retry 3
 delay_before_retry 3
 connect_port 80
 }
 }
}
```