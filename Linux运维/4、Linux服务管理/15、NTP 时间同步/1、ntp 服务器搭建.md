**基础班ntp回顾：**

```
ntpdate cn.ntp.org.cn
```

虽然以上方式，可以实现对时间的校对！但是由于需要连接外网，其同步需要消耗一定的时间，那有没有更好的解决办法呢？

# **第一步：准备一台NTP时间服务器**

| 编号 | 主机名称 | IP地址 | 备注信息 | 
| -- | -- | -- | -- |
| 1 | ntp.hackwu.cn | 192.168.23.13 | NTP 内网时间服务器 | 


# 第二步：安装ntp服务软件

```
yum install -y ntp
```

# 第三步：修改配置文件

```
vim /etc/ntp.conf
restrict 192.168.23.0 mask 255.255.255.0    #添加此行，
    #表示可以连接本机ntp服务的网段
```

# 第四步：启动ntp服务

```
ntpdate cn.ntp.org.cn        #先同步本机时间
systemctl start ntpd        #启动ntp时间服务
```

到此NTP时间服务器配置完毕，以后所有的计算机都可以直接使用nfpdate同步此NTP服务器