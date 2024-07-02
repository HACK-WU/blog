# 1、LVS/NAT实践

## 1）环境准备

```
DIP:192.168.10.10/24
VIP:172.16.0.10/16
RIP1:192.168.10.20 GATEWAY:192.168.10.10
RIP2:192.168.10.30 GATEWAY:192.168.10.10
```

## 2）DS部署（负载均衡器）

```
1.安装ipvsadm工具
[root@node1 ~]# yum install ipvsadm -y
2.打开转发，配置nat
[root@node1 ~]# echo 1 >> /proc/sys/net/ipv4/ip_forward
[root@node1 ~]# iptables -t nat -F
[root@node1 ~]# iptables -t nat -A POSTROUTING -s 192.168.10.0/24 -o ens37 -j MASQUERADE
3.设置wrr策略和添加RS主机
[root@node1 ~]# ipvsadm -A -t 172.16.0.10:80 -s wrr
[root@node1 ~]# ipvsadm -a -t 172.16.0.10:80 -r 192.168.10.20:80 -m -w 1
[root@node1 ~]# ipvsadm -a -t 172.16.0.10:80 -r 192.168.10.30:80 -m -w 1
4.查看策略信息
[root@node1 ~]# ipvsadm -ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
-> RemoteAddress:Port Forward Weight ActiveConn InActConn
TCP 172.16.0.10:80 wrr
-> 192.168.10.20:80 Masq 1 0 0
-> 192.168.10.30:80 Masq 1 0 0
```

## 3）RS部署（真实服务器）

```
# RS的网关必须指向DS
[root@node2 ~]# echo "RS2" > /usr/share/nginx/html/index.html
[root@node3 ~]# echo "RS3" > /usr/share/nginx/html/index.html
[root@node1 ~]# curl 172.16.0.10
RS1
[root@node1 ~]# curl 172.16.0.10
RS2
注意：selinux策略和防火墙！
```

## 4）ipvsadm命令

```
ipvsadm：
管理集群服务
 添加：-A -t|u|f service-address [-s scheduler]
 -t: TCP协议的集群
 -u: UDP协议的集群
  service-address: IP:PORT
 -f: FWM: 防火墙标记
  service-address: Mark Number
修改：-E
删除：-D -t|u|f service-address
管理集群服务中的RS
添加：-a -t|u|f service-address -r server-address [-g|i|m] [-w weight]
 -t|u|f service-address：事先定义好的某集群服务
 -r server-address: 某RS的地址，在NAT模型中，可使用IP：PORT实现端口映射；
 [-g|i|m]: LVS类型
 -g: DR模型
 -i: TUN模型
 -m: NAT模型
 [-w weight]: 定义服务器权重
修改：-e
删除：-d -t|u|f service-address -r server-address
查看
 -L|l
 -n: 数字格式显示主机地址和端口
 --stats：统计数据
 --rate: 速率
 --timeout: 显示tcp、tcpfin和udp的会话超时时长
 -c: 显示当前的ipvs连接状况
 删除所有集群服务
 -C：清空ipvs规则
保存规则
 -S
  # ipvsadm -S > /path/to/somefile
 载入此前的规则：
 -R
  # ipvsadm -R < /path/form/somefile
```

# 2、LVS/DR实践

## 1）环境准备

```
DIP:192.168.80.10/24
RIP1:192.168.80.20
RIP2:192.168.80.30
VIP:192.168.80.100
```

## 2）DS部署

```
[root@node1 ~]# ifconfig ens33:0 192.168.80.100 broadcast 192.168.80.255
netmask 255.255.255.0 up
[root@node1 ~]# route add -host 192.168.80.100 dev ens33:0
[root@node1 ~]# ipvsadm -A -t 192.168.80.100:80 -s wrr
[root@node1 ~]# ipvsadm -a -t 192.168.80.100:80 -r 192.168.80.20:80 -g -w 2
[root@node1 ~]# ipvsadm -a -t 192.168.80.100:80 -r 192.168.10.30:80 -g -w 1
```

## 3)RS部署

```
[root@node2 ~]# cat rs.sh
#!/bin/bash
vip=192.168.80.100
ifconfig lo:0 $vip broadcast 192.168.80.255 netmask 255.255.255.255 up
route add -host $vip lo:0
echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
在所有RS上部署！
```

## 4）测试

不要在DS上直接进行测试，可能会因为网络的问题导致丢包