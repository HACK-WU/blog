# iptables nat表应用案例

- nat表作用

- 导流

- nat表作用位置

- KVM或OpenStack中虚拟机或云主机与外部通信

- Docker管理的容器与外部通信

- nat表规则动作所对应的链

- SNAT			源地址转换			应用于出口POSTROUTING

- MASQUERADE	源地址转换			应用于出口POSTROUTING

- DNAT			目标地址转换			应用于进☐PREROUTING

- REDIRECT		端口重定向			应用于进口PREROUTING

- **开启路由转发**

```
方法一：(推荐使用)
root@server1 ~# vim /etc/sysctl.conf 
。。。。。。

net.ipv4.ip_forward=1           
root@server1 ~# sysctl  -p         //刷新系统配置
net.ipv4.ip_forward = 1
root@server1 ~# sysctl  -a | grep "ip_forward"    //查看系统配置
net.ipv4.ip_forward = 1
方法二:    (网络重启后，可能会失效)
[root@localhost~]#echo 1 /proc/sys/net/ipv4/ip_forward   //这个文件不能用vim编辑器打开，只能cat或者echo
[root@localhost~]#cat /proc/sys/net/ipv4/ip_forward   //这个文件不能用vim编辑器打开，只能cat或者echo
    1            //为开启
[root@localhost ~]echo 0 /proc/sys/net/ipv4/ip_forward   //关闭路由转发
[root@localhost ~]cat /proc/sys/net/ipv4/ip_forward
0
```