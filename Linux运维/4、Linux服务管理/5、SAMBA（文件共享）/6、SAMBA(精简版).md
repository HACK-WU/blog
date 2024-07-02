**SAMBA**

# **1、简介**

### 1）历史：

1987年，微软公司和英特尔公司共同制定了SMB（Server Messages Block，服务器消息块）协议，旨在解 决局域网内的文件或打印机等资源的共享问题，这也使得在多个主机之间共享文件变得越来越简单。到了1991年， 当时还在读大学的Tridgwell为了解决Linux系统与Windows系统之间的文件共享问题，基于SMB协议开发出了 SMBServer服务程序。这是一款开源的文件共享软件，经过简单配置就能够实现Linux系统与Windows系统之间的 文件共享工作。当时，Tridgwell想把这款软件的名字SMBServer注册成为商标，但却被商标局以SMB是没有意义的 字符而拒绝了申请。后来Tridgwell不断翻看词典，突然看到一个拉丁舞蹈的名字—Samba，而且这个热情洋溢的 舞蹈名字中又恰好包含了“SMB”，于是Samba服务程序的名字由此诞生。Samba服务程序现在已经成为在Linux系 统与Windows系统之间共享文件的最佳选择

## 2）SMB协议：

- SMB（服务信息模块）协议是一个高层协议、它提供了在网络上的不同计算机之间共享文件，打印机和不同通 信资料的手段

- SMB使用NetBIOS API实现面向连接的协议，该协议为Window是客户程序和服务提供了一个通过虚链路按照 请求——响应方式进行通信的机制

- SMB的工作原理就是让NetBIOS与SMB协议运行在TCP/IP上，并且使用NetBIOS的名字解释器让Linux机器可 以在Windows的网络邻居中被看到，从而在Windows的网络邻居中被看到，从而和Windows9x/NT/200X进 行相互沟通，共享文件和打印机

## 3）CIFS协议：

- 通用网际文件系统是微软服务器消息块协议（SMB）的增强版

- 提供计算机用户在企业内部网和因特网上共享文件的标准方法

- CIFS在TCP/IP运行，利用英特网上的全球域名服务系统（DNS）增强其可扩展性，同时为因

特网上普遍存在 的慢速拨号连接优化

## 4）应用环境

- 文件和打印机共享：文件和打印机共享是samba的主要功能，SMB进程实现资源共享，将文件和打印机发布到网络之中，以供用户可以访问

- 身份验证和权限设置：samba服务支持user mode和domain mode 等身份验证和权限设置模式，通过加密方式可以保护共享的文件和打印机

- 名称解析：samba通过NMB服务可以搭建NBNS服务器，提供域名解析，将计算机的NetBIOS名称解析为ip地址

- 浏览服务，局域网中，samba可以成为本地主浏览服务器，保存可用资源列表，当使用客户端访问windows网上邻居时，会提供浏览列表，显示共享目录、打印机资源等

## 5）端口号：139和445

- 在早期，SMB运行于NBT协议上，使用udp协议的137和138以及TCP协议的139端口

```
cat /etc/services
     netbios-ns    137/tcp             # NETBIOS Name Service
     netbios-ns    137/udp
     netbios-dgm   138/tcp             # NETBIOS Datagram
Service
     netbios-dgm   138/udp
     netbios-ssn   139/tcp             # NETBIOS session service
     netbios-ssn   139/udp
```

## 6）NetBIOS协议

- NetBIOS是Network Basic Input/Output System的简称，**网络基本输入输出协议**。协议，一般指能用于局域网通信的一套API，是由IBM公司开发的。主要作用，**通过NetBIOS协议获得计算机名称，然后把计算机名称解析为对应的IP地址**

## **7）基于C/S模式**

## 8）安装：[root@server1 ~]# yum install samba -y

## 9）相关配置

```
etc/sysconfig/samba：     用于设置守护进程的启动参数。
/etc/samba/smb.conf：     主配置文件。
/etc/samba/smbusers：     用于映射Linux用户和Windows用户。
/etc/samba/lmhosts：      用于设置NetBIOS名字与IP地址的对应关系表。
/etc/pam.d/samba：        Samba的PAM配置文件
/etc/rc.d/init.d/smb：    Samba的INIT启动脚本
```

## 10）相关工具

```
服务端工具：
/usr/bin/smbpasswd：    用于设置Samba用户账号及口令
/usr/bin/testparm：     用于检测配置文件的正确性
/usr/bin/smbstatus：    用于查找网络中的Samba服务器
客户端工具：
/usr/bin/findsmb：    用于查找网络中的Samba服务器
/usr/bin/smbclient：    Linux下的Samba客户端
/usr/bin/smbget：    基于SMB/CIFS的类似于wget的下载工具
/usr/bin/smbtar：    类似于tar的归档工具，用于将SMB/CIFS的共享打包备份到Linux主机
```

## 11）主配置文件

```
[root@server1 ~]# cat /etc/samba/smb.conf
# 默认主配置文件：
[global] #全局参数。
 workgroup = MYGROUP     #工作组名称
 server string = Samba Server Version %v     #服务器介绍信息，参数%v为显示SMB版本号
 log file = /var/log/samba/log.%m     #定义日志文件的存放位置与名称，参数%m为来访的主机名
 max log size = 50     #定义日志文件的最大容量为50KB
 security = user     #安全验证的方式，总共有4种
          #share：来访主机无需验证口令；比较方便，但安全性很差
          #user：需验证来访主机提供的口令后才可以访问；提升了安全性
          #server：使用独立的远程主机验证来访主机提供的口令（集中管理账户）
          #domain：使用域控制器进行身份验证
 passdb backend = tdbsam #定义用户后台的类型，共有3种
      #smbpasswd：使用smbpasswd命令为系统用户设置Samba服务程序的密码
      #tdbsam：创建数据库文件并使用pdbedit命令建立Samba服务程序的用户
      #ldapsam：基于LDAP服务进行账户验证
 load printers = yes #设置在Samba服务启动时是否共享打印机设备
 cups options = raw #打印机的选项
[homes]                     #共享参数
 comment = Home Directories     #描述信息
 browseable = no         #指定共享信息是否在“网上邻居”中可见
 writable = yes         #定义是否可以执行写入操作，与“read only”相反
[printers]                     #打印机共享参数
 comment = All Printers
 path = /var/spool/samba     #共享文件的实际路径(重要)。
 browseable = no
 guest ok = no             #是否所有人可见，等同于"public"参数。
 writable = no
 printable = yes
```

## 12）安全等级

```
Samba安全等级：
User：由本地Samba服务器负责账户验证，使用smbpasswd 设置账号（默认的安全等级）
Domain：账户验证账户及口令的工作由其他的Windows 或Samba域控制器负责，需要使用“password server”指令指定验证服务器
Ads：验证账户及口令的工作由支持Kerberos验证的Windows活动目录服务器负责。需要使用“realm”指令指定Kerberos领域
Share：匿名共享
Samba账户及口令数据库
1.Samba使用的账户文件/数据库是与系统账户文件分离的
2.当设置了user的安全等级后（默认），将由本地系统对问Samba共享资源的用户进行认证
3.用.tdb格式的口令数据库，初始情况下口令数据库文件并不存在
4.为了创建Samba的口令数据库文件，管理员可以在添加Samba账户的同时创建它
5.管理员可以使用smbpasswd命令配置Samba账号并设置其口令
```

# 2、配置一个共享资源(具体步骤)

1. 设置共享名称

1. 共享资源发布后，必须为每个共享目录或打印机设置不同的共享名称，给网络用户访问时使用，并且共享名可以与原目录名不同。例如，samba服务器上有个目录为/share，需要发布该目录为共享目录，定义共享名为public

1. 共享资源描述

1. 格式： comment = 备注信息

1. 备注信息通常是用来进行解释说明的

1. 共享路径

1. 共享资源的原始完整路径

1. 格式： path = 绝对路径

1. 设置匿名访问

1. 共享资源如果对匿名访问进行设置，可以更改public字段

1. 格式： public = yes | no （yes代表允许匿名访问，no代表不允许）

1. 设置访问用户

1. 如果共享资源存在重要数据的话，需要访问用户审核，可以使用valid users字段进行设置

1. 格式：

1. valid users = 用户

1. valid users = @组名

1. 设置目录只读

1. 共享目录如果限制用户的读写操作，可以通过readonly实现

1. 格式： readonly = yes | no

1. yes代表只读

1. no代表读写

1. 设置目录可写

1. 如果目录允许用户写操作，可以使用writable或write list两个字段进行设置

1. 格式：

1. writable = yes 读写

1. writable = no 只读

1. write list = 用户名

1. write list = @ 组名

# 3、案例，通过用户名共享文件

共享销售部

## 1)修改主配置文件安全相关设置

```
[root@server1 ~]# vim /etc/samba/smb.conf
[global]
   workgroup = SAMBA
   security = user
#    passdb backend = tdbsam
   passdb backend = smbpasswd
   smb passwd file = /etc/samba/smbpasswd
   printing = cups
   printcap name = cups
   load printers = yes
   cups options = raw
# 重启smb服务之后，会自动生成/etc/samba/smbpasswd该文件
```

## 2）添加销售部用户和组

```
[root@server1 ~]# groupadd xsb
[root@server1 ~]# useradd -g xsb -M -s /sbin/nologin xsb01
[root@server1 ~]# useradd -g xsb -M -s /sbin/nologin xsb02
[root@server1 ~]# useradd jsb01
```

## 3）添加相应的samba账户

```
[root@server1 ~]# smbpasswd -a xsb01
New SMB password:
Retype new SMB password:
Added user xsb01.
[root@server1 ~]# smbpasswd -a xsb02
New SMB password:
Retype new SMB password:
Added user xsb02.
```

## 4）指定共享目录

```
[root@server1 ~]# mkdir /xsb
[root@server1 ~]# cp /etc/hosts /xsb
[root@server1 ~]# vim /etc/samba/smb.conf
[xsb]
   comment = Xsb Data
   path = /xsb
   valid user = xsb01,xsb02
```

## 5）重启服务

```
[root@server1 ~]# systemctl restart smb.service
[root@server1 ~]# systemctl restart nmb.service
```

## 6）检查139和445端口号

```
[root@server1 ~]# ss -tanl
State    Recv-Q Send-Q Local Address:Port         Peer
Address:Port       
LISTEN   0   50         *:139               *:*
        
LISTEN   0   50         *:445               *:*
        
LISTEN   0   50         :::139              :::*
        
LISTEN   0   50         :::445              :::*
```

## 7）客户端验证

```
# linux上验证
[root@server2 ~]# yum install samba-client -y
[root@server2 ~]# smbclient -L //192.168.80.151/xsb -U xsb01
Enter SAMBA\xsb02's password:
Sharename    Type   Comment
---------    ----    -------
print$     Disk   Printer Drivers
xsb       Disk   Xsb Data
IPC$      IPC    IPC Service (Samba 4.10.16)
xsb02      Disk   Home Directories
Reconnecting with SMB1 for workgroup listing.
Server        Comment
---------       -------
Workgroup      Master
---------       -------
SAMBA        SERVER1
# 在windows上进行验证
 windows验证：
 在Window运行输入地址：\\192.168.10.10
 用户名：******
 密码：******
 可以在DOS窗口中使用命令net use * /delete 清空用户缓存信息
```

## 8）在Linux上进行挂载

```
[root@server2 ~]# mkdir /xsbdata
[root@server2 ~]# yum install cifs-utils -y
[root@server2 ~]# vim auth.smb
username=xsb01
password=1
[root@server2 ~]# vim /etc/fstab
//192.168.80.151/xsb /xsbdata  cifs  defaults,credentials=/root/auth.smb
  0 0
[root@server2 ~]# df
文件系统          1K-块  已用   可用 已用% 挂载点
/dev/mapper/centos-root 17811456 1099604 16711852   7% /
devtmpfs          922468    0  922468   0% /dev
tmpfs           933524    0  933524   0% /dev/shm
tmpfs           933524   8852  924672   1% /run
tmpfs           933524    0  933524   0% /sys/fs/cgroup
/dev/sda1         1038336  145756  892580  15% /boot
tmpfs           186708    0  186708   0% /run/user/0
//192.168.80.151/xsb   17811456 1108900 16702556   7% /xsbdata
[root@server2 ~]# ls /xsbdata/
hosts
```

```
扩展
隐藏目录
    隐藏共享目录，访问时必须输入绝对的URL进行访问
    格式：browseable = no
控制访问源
     使用hosts allow 和 hosts deny进行控制访问源
    hosts allow = 192.168.1.           允许192.168.1.0/24网段进行访问
    hosts deny = 192.168.1.3           禁止192.168.1.3主机进行访问
    当allow和deny同时设置时allow优先，但上述情况无法拒绝192.168.1.3，这种情况可以使用
except字段
    hosts allow = 192.168.1. EXCEPT  192.168.1.3
写入控制
     writable = yes
     write list =xsb01                   开启samba服务的写入权限
     chmod 777 /xsb                      给共享目录赋予写入
权限
用户账号映射（为了防止系统账号被黑客破解）
    username map = /etc/samba/smbusers 全局添加username map字段
    useradd rm
    passwd rm
     vim /etc/samba/smbusers
      rm = testuser
     重启smb服务
```