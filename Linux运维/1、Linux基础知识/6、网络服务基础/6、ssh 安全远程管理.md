# **1、什么是ssh**

ssh 是 Secure Shell 的缩写，是一个建立在应用层上的安全远程管理协议。ssh 是目前较为可靠的传输协议，专为远程登录会话和其他网络服务提供安全性。利用 ssh 协议可以有效防止远程管理过程中的信息泄露问题。ssh可用于大多数UNIX和类UNIX操作系统中，能够实现字符界面的远程登录管理，它默认使用22端口，采用密文的形式在网络中传输数据，相对于通过明文传输的Telnet协议，具有更高的安全性。

# **2、ssh 的登录验证模式**

ssh 提供了基于账户密码（口令）和密钥对两种登录验证方式，这两者都是通过密文传输数据的

## **1）账户密码验证**

![](images/WEBRESOURCEfa1db007bb474309539888066e6f82d6截图.png)

  账户密码登录认证过程中传输的是用户的账户名和密码，密码具有足够的复杂度才能具有更高的安全性。


Linux主机之间的远程管理工具是ssh命令，所以我们直接使用ssh进行远程登录


```javascript
格式：
ssh 用户名@IP地址
ssh root@192.168.88.20
```

windows远程登录Linux主机一般使用第三方工具，比如Xshell等工具

## **2）秘钥对验证**

![](images/WEBRESOURCEf2c8b3951b565578cdd5e01b73372416截图.png)

1. 首先需要在 Client 上创建一对密钥，并且需要把公钥放在需要访问的 Server 上


2. 当 Client 需要连接 Server 时，Client 端的软件就会向 Server 端发出登录请求，请求使用密钥对中的的公钥进行安全验证


3. Server 收到请求之后，会在该用户的家目录下查询公钥文件，拿 Client 发送过来的公钥和自己家目录下的公钥进行比较


4. 如果两个公钥一致，Server 就用公钥加密“challenge（质疑）”，并把它发送给 Client 软件。Client 收到加密内容之后，使用本地的私钥进行解密，再把解密结果发送给 Server 端，Server 端验证成功后，允许登录


注意：若第3个步骤对比结果失败，则 Server 端会通知 Client 端此公钥未在本机注册，无法验证登录

![](images/WEBRESOURCE45ea76270d0e8730b8797b777c3dfa23截图.png)

# **3、密钥对登录试验****（配置ssh服务）**

## **1）环境准备**

准备好两台Linux操作系统的主机，配置好相关网络参数，实现可以正常通信，

**临时关闭防护功能**

```javascript
iptables -F #清空防火墙规则
setenforce 0 #临时关闭SELinux
```

- **查看防火墙状态：iptables -L / service iptables status**

```javascript
[root@localhost ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
ACCEPT     all  --  anywhere             anywhere            state RELATED,ESTABLISHED 
ACCEPT     icmp --  anywhere             anywhere            
ACCEPT     all  --  anywhere             anywhere            
ACCEPT     tcp  --  anywhere             anywhere            state NEW tcp dpt:ssh 
REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited 
Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         
REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited 
Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         
[root@localhost ~]# ^C
```

- **清空防火墙规则：iptables -F**

```javascript
[root@localhost ~]# iptables -F
[root@localhost ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         
Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination    
[root@localhost ~]# service iptables status
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         
Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination   
```

**永久关闭防护功能：**

```javascript
chkconfig iptables off #设置防火墙开机不自启动
sed -i ‘7s/enforcing/disabled/’ /etc/selinux/config #永久关闭SELinux
```

注意：以上两条命令执行后，需要重启服务器才能生效，切记

- **查看是否自启：chkconfig  --list iptables**

```javascript
chkconfig --list iptables
iptables       	0:关闭	1:关闭	2:启用	3:启用	4:启用	5:启用	6:关闭
```

- 关闭防火墙自启：chkconfig iptables off

- 配置配置文件：vi   /etc/selinux/config(SELINUX=disabled)

```javascript
[root@localhost ~]# vi /etc/selinux/config
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing                 #更改此处
# SELINUXTYPE= can take one of these two values:
#     targeted - Targeted processes are protected,
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

## **2）密钥对验证**

**Linux 主机之间的密钥 对 登录验证**

### **1. 客户端生成密钥对文件：shh-keygen -t rsa -b 2048
**

```javascript
ssh-keygen -t rsa -b 2048
    -t 指定加密类型（rsa/dsa等）
    -b 指定密钥对加密长度
询问1：执行过程中会询问保存位置，一般默认保存在当前用户家目录下的.ssh/目录下
询问2：是否对密钥文件进行加密
加密：若加密，则在调用密钥文件时需要先验证密钥的密码，密码正确才能使用密钥文件
不加密：若不加密，则密钥文件可以直接被调用，整个登录验证过程无需输入任何密码，即为免密登录
```

- **生成秘钥：ssh-keygen (key-generate:秘钥生成）**

```javascript
[root@client ~]# ssh-keygen -t rsa -b 2048
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):            #生成的秘钥的保存位置/root/.shh/id_rsa,是否保存在这；
Enter passphrase (empty for no passphrase):                         #是否要对秘钥文件加密，如果要输入密码，不要直接回车即可
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.            #私钥的保存位置；
Your public key has been saved in /root/.ssh/id_rsa.pub.			 #公钥的保存位置
The key fingerprint is:
37:0d:a2:d6:96:7c:d3:0d:76:ce:9a:c7:f5:17:a2:3b root@client
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|        . . o .  |
|       + o = *   |
|      o S = o.+..|
|     . . o o.+..o|
|           .o o o|
|           E..  .|
|           ..    |
+-----------------+
```

- **查看秘钥文件：**

- **查看私钥：vi  /root/.ssh/id_rsa**

```javascript
[root@client ~]# vi  /root/.ssh/id_rsa
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,1BEC060AC5DF04D9
R8ZEnaBpVK8mHFb9egcFjBCxdjh8pRqyJUBKmEmE4e8hq4PM4aUM2p7G2xr0zFMP
yt+zkPGhC9rOBd0UAAzjuIpRrPOZHfb11uLD2LMOdZlWfjjsCNHOXOwpuUwvQUKh
te3fhvbx6DzH+dv+81t9B7RxQPuv8AyJojgD9uJ0ISwV+Gx8nhf+DA496q0QjKNa
Rr5HA9OPjhVLg/wrdrUyZS5oYuAkjEd6GYt5teRiFL8I6hjaBPTDyxrMxoCYEv47
6QM2RbntawHLqnOL4KpIWonuOSQGrQ/BnXdfp2F3HFw4yJWGlZiFV8D3QUcxzd8c
ROrV2cVh18c3yeiSwZo5+bDbRMMsP+sqDzC4VX6PFFDr/eWb9m/URV6AyZNyNwbc
350TB1TZIhCxtOqu9dO/oiWEjdWQXLi43zqsiREvOoaYyrZ491WXy5pi9JindA1E
i+apKfOI9dBj4FaBl3h/1NgJW3z/FG1Jqik39/oVY7jqhg5w1Ame9kdSZKlu3NW4
GTFL4o9E67Hl5qYXNjlz5xs9PZU4uVkm/9vVwtKWwt/tgwfdoKIZz0yoaRYN32wC
hRyYeC+QBoXO2HEY9m1icTlz6a3YhETIZgUhqBS+0nzWPFaih88i011OhGBw48ui
E4K6imv2pEG3nOgebGeGpc9dGNwk8raZY/r1TgWcg3FalYM8wN3prT2WmauDIBLh
rrhuibKQwk45891kZKJPjagnbm6ZVibuL4qDuUqrz77r1OLAQ4YqP4Qxs24/LC7X
GWOXUgVXyblNxaSkkpYT4ZDkDi0qjm9ZmhZ+teZQ4Y+T1+eOvPhhd5Grd60i9485
d209m82saTgmZdTPDIz1Pv5hmpq/097a/yWLfrM7tYoAQ+qxaw5VGTeBbaeOyIPp
ZLFtQxhPgICG+LB3QkT+FuDQp5TcmFW1y30rwDgCeovJa36hjx/1zuW/Ni4a4M1b
ekCbnNNodPxOce26qDVSoJvXnITbMJlpH7sBMgNhSqOQePdwNKq1TPFykxiSnWU3
dEdBWBP2/ZGnSyt9TXdtaxcIHmliJFaQE8bGUGZqjcIIjb6VFRxgXIJvk/K07sac
2UC68I3y8ZK46F2BvnYYFKgiu62yqhjEKPTTzb0+UIMTrrSN3kvQ8bSwfE6in7IJ
"~/.ssh/id_rsa" 30L, 1743C                                   
```

- **查看公钥：**

```javascript
[root@client ~]# vi /root/.ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAwM4x3LhUhP+0i3XmpPXrZKv94Yg8i
wKd6nKQ5TyY3xq/DpapksT5TSlfGYPduozie5JJTXeaZqzaVtKPSxt6uXYrs0CPpa
ua+J2KWjrbNBv5Dw5xWrBPBFV8eGd1qnV5GvlssYPbOjY1lRSDpi0j7iQEsEznRC/
WoaomSrUsboPfUUWCpWWQA6MG9l23gZpl85U6bpF95UkScU+87OYqjFbcHZ3SZAmE
Zpbr0Zfqz2ePqkks2BJvCGdcCp8OVzP0Os8Rb1eafDCppAQhVyHDuODPYXxHAWfip
A6mS0W9NBnKG3kRwrB3IyEpeHpZfUF7vZYAOHwg1Kkzqb5/c9sPfw== root@client
~                  
```

### **2. 将公钥文件上传至服务器端:ssh-copy-id root@192.168.1.104(服务器IP）
**

```javascript
ssh-copy-id 用户名@服务器IP地址
#该用户名和要用来登录服务器的用户名一致
```

- **上传公钥文件：ssh-copy-id root@192.168.1.104 （成功后，服务器端公钥文件将会被保存在/root/.ssh/authorized_keys  中）**

```javascript
[root@client ~]# ssh-copy-id root@192.168.1.104
root@192.168.1.104's password:                                #输入服务器登录密码
Now try logging into the machine, with "ssh 'root@192.168.1.104'", and check in:
  .ssh/authorized_keys
to make sure we haven't added extra keys that you weren't expecting.         
#上传成功
```

- **查看服务器端保存的公钥文件：vi /root/.ssh/authorized_keys**

```javascript
[root@server ~]# vi /root/.ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAwM4x3LhUhP+0i3XmpPXrZKv94Yg8iwKd6nKQ5TyY3xq/
DpapksT5TSlfGYPduozie5JJTXeaZqzaVtKPSxt6uXYrs0CPpaua+J2KWjrbNBv5Dw5xWrBPBFV8eGd1
qnV5GvlssYPbOjY1lRSDpi0j7iQEsEznRC/WoaomSrUsboPfUUWCpWWQA6MG9l23gZpl85U6bpF95UkS
cU+87OYqjFbcHZ3SZAmEZpbr0Zfqz2ePqkks2BJvCGdcCp8OVzP0Os8Rb1eafDCppAQhVyHDuODPYXxH
AWfipA6mS0W9NBnKG3kRwrB3IyEpeHpZfUF7vZYAOHwg1Kkzqb5/c9sPfw== root@client
```

### **3. 客户端尝试登录服务器
**

```javascript
ssh 用户名@服务器IP地址
#密钥对验证优先级大于账户密码验证
```

- **登录服务器：**

```javascript
[root@client ~]# ssh root@192.168.1.104
Enter passphrase for key '/root/.ssh/id_rsa':              #输入秘钥加密的密码
Last login: Sun Aug 15 22:04:04 2021 from 192.168.1.101    # 登录成功
[root@server ~]# 
```

- **如果删除或者修改服务器端的公钥文件，将登录失败；**

**Windows 使用 密钥 对 登录Linux**

**1. 使用Xshell自带的密钥对生成向导生成密钥对**

![](images/WEBRESOURCE7cc8d8673659abb18f9a5bdb1e4d4090截图.png)

**2. 将公钥导入Linux主机的指定用户下的指定公钥配置文件内
**

**后面用哪个用户登录就放在谁家里，这里我们先用root用户做实验
**

**在root家目录下，找到 .ssh 目录，然后在里面创建 authorized_keys 文件，并且将公钥写入进去**

![](images/WEBRESOURCEbaf222aadf9a8aa051ecd5159c1644fe截图.png)

![](images/WEBRESOURCEb881b2286e58c916ed3768e90d417b90截图.png)

![](images/WEBRESOURCE271a17df56d47b484af80b4c934f36c8截图.png)

**
**

![](images/WEBRESOURCEbe79b779b6a60232302ca6a49a12b4a7截图.png)

**3. 使用windows尝试登录指定用**

```shell
[C:\~]$ ssh root@192.168.1.103
Connecting to 192.168.1.103:22...
Connection established.
To escape to local shell, press 'Ctrl+Alt+]'.
```

- **选择使用public key 登录：**

![](images/WEBRESOURCE4b325e6753c0b86abf7959ba635ea9fa截图.png)

- **确定登录成功：**

```javascript
Last login: Sun Aug 15 22:25:35 2021 from 192.168.1.103
[root@server ~]# 
```

# **4、禁止使用密码登录**

当我们学会了使用密钥对进行验证后，建议生产环境下将账户密码登录功能关掉


```javascript
配置文件：/etc/ssh/sshd_config
选项：
PasswordAuthentication no
```

- **修改配置文件，关闭账号密码登录：vi /etc/ssh/sshd_config****(需要重启sshd服务）**

```javascript
[root@server ~]# vi /etc/ssh/sshd_config 
#PasswordAuthentication yes
#PermitEmptyPasswords no
PasswordAuthentication yes              #改为 no              
# Change to no to disable s/key passwords
#ChallengeResponseAuthentication yes
ChallengeResponseAuthentication no
```

- **重启sshd服务：service sshd re****start**

```javascript
[root@server ~]# service sshd restart
停止 sshd：                                                [确定]
正在启动 sshd：                                            [确定]
```

- **登录测试**

- 

![](images/WEBRESOURCE5f3ba00ce37ad2d234f7ac50040178d8截图.png)

# **5、禁止root用户远程登录****(需要重启sshd服务：service sshd  restart）**

  root 在系统中是一个可以为所欲为的角色，我们可以在平时的操作中用普通用户操作，在有需要修改一些系统设置的


时候再从普通用户切换到 root 用户，这样可以最大限度的避免因为误操作而对系统造成破坏，同时也可以避免黑客在


暴力破解后直接使用 root 用户登录系统，一般在远程登录管理上我们会禁止直接使用 root 用户登录


```javascript
配置文件：/etc/ssh/sshd_config
选项：
PermitRootLogin no
```

- **修改配置文件禁止root远程登录 ：vi  /etc/ssh/sshd_config**

```javascript
[root@server ~]# vi /etc/ssh/sshd_config 
#PermitRootLogin yes 
PermitRootLogin yes      #  改为 no
#StrictModes yes
```

**（虽然限制了root用户登录，但是可以用普通用户登录后使用 su root  切换，这样不受限制）**

**设置了之后，就算使用秘钥登录，也无法登录！！**

# **6、修改默认端口，限制ssh监听****（需要重启sshd服务）**

- **修改默认端口 ****：**ssh 作为一个用来远程管理服务器的工具，需要特别的安全，默认情况下使用TCP的22端口，若不进行


修改，很容易被利用遭到攻击，所以我们一般都会修改端口，尽量修改一个高位端口（范围1-65535）


```javascript
配置文件：/etc/ssh/sshd_config
选项：
Port 59527
ssh -p 端口 用户名@服务器IP
```

```javascript
[root@server ~]# vi /etc/ssh/sshd_config 
# default value.
#Port 22
Port 9257        #修改此处
#AddressFamily any
[C:\~]$ ssh root@192.168.1.104 9257
Connecting to 192.168.1.104:9257...   #端口为9257
Connection established.
To escape to local shell, press 'Ctrl+Alt+]'.
Last login: Sun Aug 15 23:35:40 2021 from 192.168.1.101      #登录成功
```

- **限制 ssh 监听I IP P ：**有些服务器则安全级别更高一些，不允许使用外网直接登录，只有通过局域网才能登录，我们可以在


机房里设置其中一台能够被外网远程连接，其他的主机都通过这个机器进行远程连接即可

（我们可以为同一个服务器设置两个IP，利用那个IP进行登录：ifconfig eht0:0 192.168.1.102(新IP）


```javascript
配置文件：/etc/ssh/sshd_config
选项：
ListenAddress 192.168.88.100
```

- 设置第二个IP：ifconfig eth0:0 192.168.1.102

```javascript
[root@server ~]# ifconfig eth0:0 192.168.1.102
[root@server ~]# ip a
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 00:0c:29:47:15:df brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.104/24 brd 192.168.1.255 scope global eth0
    inet 192.168.1.102/24 brd 192.168.1.255 scope global secondary eth0:0    #第二个IP生效
    inet6 fe80::20c:29ff:fe47:15df/64 scope link 
       valid_lft forever preferred_lft forever
```

- 设置只能以第二个IP登录，修改配置文件（设置监听IP）

```javascript
[root@server ~]# vi /etc/ssh/sshd_config 
#ListenAddress 0.0.0.0
#ListenAddress ::
ListenAddress 192.168.1.102                #设置监听IP
# Disable legacy (protocol version 1) support in the server for new
```