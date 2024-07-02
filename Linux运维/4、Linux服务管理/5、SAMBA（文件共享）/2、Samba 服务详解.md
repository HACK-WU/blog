

# **1、Samba 软件相关信息**

```javascript
1. 协议：	SMB/CIFS
2. 服务：	smb 实现资源共享、权限验证 TCP 139 445
3. 配置文件（/etc/samba/）
    smb.conf 主配置文件
    smbusers 别名配置文件
```

# **2、登录验证模式 （ 安全级别） ）**

- ****

```javascript
◆ share 匿名验证
```

- 

```javascript
◆ user 本地用户验证（Samba服务器默认的安全级别，用户在访问共享资源之前必须提供用户名和密码进行验证）
拓展 ：tdbsam：该方式是使用一个数据库文件来验证。数据库文件叫passdb.tdb。可以通过pdbedit -a 向数据库中添
加新用户，不过要建立的Samba用户必须先是系统用户。也可以理解为我们使用pdbedit -a 将系统用户转化为了samba
用户。pdbedit命令的参数很多，列出几个主要的。
pdbedit -a username：    新建Samba账户（将系统用户转化为samba用户，并设置密码）
pdbedit -x username：    删除Samba账户
pdbedit -L：            列出Samba用户列表，读取passdb.tdb数据库文件。
```

- 

```javascript
◆ 别名用户访问（虚拟用户）
```

# **3、常见 配置 参数解释**

## **[global]	****用于定义Samba服务器的总体特性,其配置项对所有共享资源生效**

- workgroup = WORKGROUP

#设定 Samba Server 所要加入的工作组或者域。

- server string = Samba Server Version %v

#设定 Samba Server 的注释，可以是任何字符串，也可以不填。宏%v表示显示Samba的版号。

- interfaces = lo eth0 192.168.12.2/24

#设置Samba Server监听哪些网卡，可以写网卡名，也可以写该网卡的IP地址。

- hosts allow = 127. 192.168.1. 192.168.10.1

#表示允许连接到Samba Server的客户端，多个参数以空格隔开。可以用一个IP表示，也可以用一个网段表示

- hosts deny 与hosts allow 刚好相反（二选一）。

例如：

```javascript
hosts allow=172.17.2. EXCEPT172.17.2.50
表示容许来自172.17.2.*.*的主机连接，但排除172.17.2.50
hosts allow=172.17.2.0/255.255.0.0
表示容许来自172.17.2.0/255.255.0.0子网中的所有主机连接
```



- log file = /var/log/samba/log.%m

#设置Samba Server日志文件的存储位置以及日志文件名称。在文件名后加个宏%m（主机名），表示对每台访问Samba Server的机器都单独记录一个日志文件。

- max log size = 50	

#设置Samba Server日志文件的最大容量，单位为kB，0代表不限制

- security = user	

#设置用户访问Samba Server的验证方式。

#可选值：

- share：	匿名访问

- user：    	自定义用户名密码 

- server： 	外部服务器用户密码

- passdb backend = tdbsam

#密码格式

- load printers = yes/no

#设置是否在启动Samba时就加载打印机

- cpu options=raw

#打印机选项



## **[homes] ****    用于设置用户宿主目录的共享属性（特殊共享）**

```
[homes] 			#共享名（特殊共享，泛指每个用户对应的家目录）
comment = Home Directories 	#共享描述
browseable = no 		#共享是否可被查看
writable = yes			 #共享是否可写
valid users = %S 		#允许访问该共享的用户
  例如：valid users = bob，@bob（多个用户或者组中间用逗号隔开，如果要加入一个组就用“@组名”表示。）
```



## **[printers]用于设置打印机共享资源的属性（特殊共享，共享打印设备，现在基本不用）**

```
[printers]                  #共享名
comment = All Printers      #共享描述
path = /var/spool/samba     #共享路径
browseable = no             #共享是否可被查看
guest ok = no               #是否可以匿名访问，类似于public=no,为no，则表名不可以匿名访问
writable = no               #是否可写
printable = yes             #是否可以打印

```

## **[****自定义]****  自定义共享区域**

```
[自定义]                         #共享名
comment = the share is xbz       #共享描述
path = /share/zdy                #共享路径
public = yes                     #是否可以匿名访问，类似于guest ok
browseable = yes                 #共享是否可被查看
writable = yes                   #是否可写(同时设置目录的W)
```

## **配置文件检查工具**

testparm ：若显示"Loaded services file OK."信息表示配置文件的语法是正确的

-v：显示samba所支持的所有选项

## **访问控制**

写入权限的控制方式（类似于vsftp的限制方式）：

◆ 配置文件开启，文件系统严格控制（尽量采用这种）

writable = yes

 

setfacl 或 chmod 777

◆ 文件系统开启，配置文件严格控制

```
chmod 777 /dir
read only = yes
write list = 用户，@组
```

服务启动管理

◆ 启动、停止、重新启动和重新加载Samba服务

```
service smb start|stop|restart|reload
```

◆ 开机自动启动samba服务

```
chkconfig --level 2345 smb on|off
```