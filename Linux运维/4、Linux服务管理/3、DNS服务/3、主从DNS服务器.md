**实验目的：**

减轻主服务器的压力

**先关闭服务器和客户机上的防火墙和 SELinux**

![](images/WEBRESOURCE84b10b6c7203014225a4509e97367b9a截图.png)

| 编号 | 主机名称 | IP地址 | 备注信息 | 
| -- | -- | -- | -- |
| 1 | slave | 192.168.23.13 | dns slave 从服务器 | 


**主从部署的核心思路：**

- 1.master和slavel的系统时间保持一致

- 2.s1ave服务器上安装相应的软件（系统版本、软件版本保持一致)

- 3.根据需求修改相应的配置文件(master和slave都应该去修改)

- 4.主从同步的核心是slave同步master.上的区域文件(zone文件)

**实验 准备： **

一台主服务器、一台从服务器、一台测试机

**搭建过程：**

# **1. 搭建主服务器 搭建主服务器 步骤 **

- a. 安装 bind 软件 	:yum install -y bind

- b. 主配置文件的修改:vi /etc/named.conf  **(监听网卡改成服务器本机IP)**

```javascript
options {
        listen-on port 53 { 192.168.22.128; };         
        listen-on-v6 port 53 { any; };
        ......
        allow-transfer { 192.168.23.13 };    #添加此行，表示允许192.168.23.13主机可以作为本机的DNS slave 服务器
}                                        

```

- c. 区域配置文件的修改：**allow-upate  改成从服务器ip,并且只留下正向解析部分**

```javascript
[root@localhost ~]# vi /etc/named.rfc1912.zones 
zone "atguigu.com" IN {
        type master;
        file "atguigu.localhost";
        allow-update { none; };          #改成从服务器ip地址
};

```

- d. 配置数据文件

正向数据文件

```javascript
[root@localhost named]# vi atguigu.localhost 

$TTL 1D
@       IN SOA  atguigu.com.  rname.invalid. (
                                        20210820        ; serial       #增加版本号
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      dns.atguigu.com.
dns     A       192.168.22.128
www     A       192.168.22.10
：
```

反向数据文件（可选做）

e. 启动 named 服务

**注意：主 DNS 的区域配置文件中 allow-updata 参数添加从服务器 IP 地址。**

# **2. 搭建从服务器 步骤：**

a. 安装 bind 软件



b. 修改主配置文件/etc/named.conf

```javascript
root@localhost ~]# vi /etc/named.conf 

options {
        listen-on port 53 { 192.168.22.129; };        #监听网卡，填写本机ip
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { any; };                     #允许访问的客户端：any
        recursion yes;    
```

c. 配置区域文件（/etc/named.rfc1912.zones）

```javascript
[root@localhost ~]# vi /etc/named.rfc1912.zones 
zone "atguigu.com" IN {
        type slave;		    # 类型 ：slave
        masters { 192.168.22.128; };    #大括号两端有空格，分号结尾；
        file "slaves/atguigu.localhost";      #正向文件，需要指定路在./slaves/下
        allow-update { none; };
};


```

**注意：从配置文件的类型需要修改为 slave，并且需要填写主服务器的地址，如下**

**type slave；**

masters { 192.168.0.10; }； #大括号两侧留有空格

文件保存位置修改为 file “slaves/atguigu.localhost”;

d. 重启服务

e. 在测试机上填写从服务器的 IP，并使用 nslookup 测试