


# 1、获取客户端的 mac 地址


```javascript
ifconfig      #查看客户机网卡的mac地址
```

- 查看客户端网卡MAC地址：ifconfig

```javascript
[root@localhost ~]# ifconfig 
eth0      Link encap:Ethernet  HWaddr 00:0C:29:91:A0:B1    #MAC 地址

```

# 2、修改DHCP服务器 /etc/dhcp/dhcpd.conf 文件


```javascript
host fantasia｛
hardware ethernet mac地址； #客户机的mac地址
fixed-address IP地址； #固定分配给客户机的ip地址（可以使用地址池以外的IP）
｝
```

- 

```javascript
[root@bogon ~]# vi  /etc/dhcp/dhcpd.conf

host fantasia {
  hardware ethernet  00:0C:29:91:A0:B1;
  fixed-address 192.168.22.120;        #将这个IP固定分配
}


```

# 3、重启DHCP 服务


```javascript
service dhcpd restart
```

# 4、 重启客户机网卡验证 IP 获取是否成功


```javascript
ifdown eth0；ifup eth0
```

- 查看ip：

```javascript
[root@localhost ~]# ip a
1。。。。。
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 00:0c:29:91:a0:b1 brd ff:ff:ff:ff:ff:ff
    inet 192.168.22.120/24 brd 192.168.22.255 scope global eth0    #获取成功
    inet6 fe80::20c:29ff:fe91:a0b1/64 scope link 
       valid_lft forever preferred_lft forever

```

