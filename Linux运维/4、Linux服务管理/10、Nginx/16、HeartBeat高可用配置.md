**通过Heartbeat实现负载均衡服务器的高可用**

> **环境准备：**
> **server04   LB   	192.168.17.103**
> **server05   backup LB   192.168.17.104**
> **    VIP：192.168.17.202**


# 1、安装

## 1.1 rpm包安装

```
#安装wget
shell > yum install -y wget
#配置epel源
shell > wget -O /etc/yum.repos.d/epel.repo 
#安装heartbeat
shell > yum install -y heartbeat
```

## 1.2 源码包安装

```
Heartbeat 3.0.6    
Cluster Glue 1.0.12 
Resource Agents 3.9.6 
```

```
/********安装开始*************/
安装所需依赖：
yum install gcc gcc-c++ autoconf automake libtool glib2-devel libxml2-develbzip2-devel e2fsprogs-devel libxslt-devel libtool-ltdl-devel make wget docbook-dtds docbook-style-xsl bzip2-devel asciidoc libuuid-devel
新建用户与组
groupadd haclient
useradd -g haclient hacluster -M -s /sbin/nologin
下载上述三个组件解压并进入相应目录进行安装
安装cluster-glue
./autogen.sh
./configure --prefix=/usr/local/heartbeat --sysconfdir=/etc/heartbeat libdir=/usr/local/heartbeat/lib64
make
make install
安装Resource Agents
./autogen.sh
./configure --prefix=/usr/local/heartbeat --sysconfdir=/etc/heartbeat libdir=/usr/local/heartbeat/lib64 CFLAGS=-I/usr/local/heartbeat/include LDFLAGS=-L/usr/local/heartbeat/lib64
ln -s /usr/local/heartbeat/lib64/* /lib64/
make
make install
安装Heartbeat
./bootstrap
./configure --prefix=/usr/local/heartbeat --sysconfdir=/etc/heartbeat CFLAGS=-I/usr/local/heartbeat/include  LDFLAGS=-L/usr/local/heartbeat/lib64
vi /usr/local/heartbeat/include/heartbeat/glue_config.h  /* 错误：“HA_HBCONF_DIR”重定义,删除最后一行，make clean */
make
make install
cp doc/ha.cf doc/authkeys doc/haresources /etc/heartbeat/ha.d/
chmod 600 /etc/heartbeat/ha.d/authkeys
chkconfig --add heartbeat
chkconfig heartbeat on
chkconfig --list heartbeat
/********安装结束*************/
```

# 2、配置

**①两台服务器都需要操作**

在两台服务器的/etc/hosts文件解析

```
192.168.17.103 server04
192.168.17.104 server05
```

/etc/heartbeat/ha.d/authkeys文件去掉对应注释

```
#到文件中，去掉以下几行的注释
auth 1
1 crc
2 sha1 HI!
3 md5 Hello!
```

**②主服务器配置**

/etc/heartbeat/haresources文件

```
#node-name resource1 resource2 ... resourceN
#添加本主机名称  VIP  启动的服务名称
server04 192.168.17.202  nginx  #添加的服务器名称  需要在/etc/init.d里有server管理服务的脚本
    server04    #本机名称
    192.168.17.202    #VIP
    nginx    #需要监控的启动脚本
```

/etc/ha.cf文件

```
auto_failback off        #非抢占模式
#找到ucast项 去掉注释并修改
ucast eth0 192.168.17.104     #给备服务器发送的地址  这里写备服务器的IP,使用单播模式
#找到node项，并去掉注释添加
node server04
node server05
```

**③备服务器配置**

/etc/haresources文件

```
#node-name resource1 resource2 ... resourceN
#添加本主机名称  VIP  启动的服务名称
server05 192.168.17.202 nginx
```

/etc/ha.cf文件

```
#找到ucast项 去掉注释并修改
#给备服务器发送的地址  这里写备服务器的IP
ucast eth0 192.168.17.103
#找到node项，并去掉注释添加
node server04
node server05
```