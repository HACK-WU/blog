**配置文件介绍**

自定义目录：/myredis/redis.conf

配置大小单位,开头定义了一些基本的度量单位，只支持

大小写不敏感

![](images/WEBRESOURCE01316554247767916ba939df7c8e6d75截图.png)

![](images/WEBRESOURCE7d19a90268f034e8b13d9944f500aaaa截图.png)

类似jsp中的

默认情况bind=127.0.0.1只能接受本机的访问请求

不写的情况下，无限制接受任何ip地址的访问

生产环境肯定要写你应用服务器的地址；

如果开启了protected-mode，那么在没有设定

![](images/WEBRESOURCEe5e68f8405f9cc0740bf37829d21a9e5截图.png)

保存配置，停止服务，重启启动查看进程，不再是本机访问了。

![](images/WEBRESOURCE9b74482849c7d6581fa736ee1bdc17e1截图.png)

将本机访问保护模式设置no

![](images/WEBRESOURCEfd8474c45e02771287a638f267b41320截图.png)

端口号，默认 6379

![](images/WEBRESOURCEfef1827fa31344e245003e71235242af截图.png)

设置tcp的

在高并发环境下你需要一个高backlog值来避免慢客户端连接问题。

注意

![](images/WEBRESOURCEaf08421da1148406ad032dad77fe0d3d截图.png)

一个空闲的客户端维持多少秒会关闭，0表示关闭该功能。即

![](images/WEBRESOURCEeeac03230e1d86148aeb3d3db1e64050截图.png)

对访问客户端的一种

单位为秒，如果设置为0，则不会进行

![](images/WEBRESOURCE233178cc7c4a36028321fdbe2dbc4465截图.png)

是否为后台进程，设置为yes

守护进程，后台启动

![](images/WEBRESOURCEf2a5677ab23d0d5d5ed864b253ec6841截图.png)

存放pid文件的位置，每个实例会产生一个不同的

![](images/WEBRESOURCEbdbc58c0b04b91948000b519b93557b6截图.png)

指定日志记录级别，Redis总共支持四个级别：

四个级别根据使用阶段来选择，生产环境选择notice 或者

![](images/WEBRESOURCE21614557ab46677fee46188d8a9d42b0截图.png)

日志文件名称

![](images/WEBRESOURCEe9fa8915781f079c8a69357ea7fbdd95截图.png)

设定库的数量 默认16，

![](images/WEBRESOURCEf54ddee801e3807dd75ec998ab296048截图.png)

![](images/WEBRESOURCE330842458277f5a84dda619ca5666b6b截图.png)

访问密码的查看、设置和取消

在命令中设置密码，只是临时的。重启

永久设置，需要再配置文件中进行设置。

![](images/WEBRESOURCEb63f267b5be11c5a579b4eb0127d0325截图.png)

Ø

Ø

Ø

number of clients reached”以作回应。

![](images/WEBRESOURCEb37882c948a9feee8d8c61f8475a17e0截图.png)

Ø

Ø

Ø

Ø

![](images/WEBRESOURCE6f19a2e820e1c3636a91868d31dbbfe8截图.png)

Ø

Ø

Ø

Ø

Ø

Ø

![](images/WEBRESOURCE944269b680a7d9457611454277c7017b截图.png)

Ø

Ø

![](images/WEBRESOURCE1af7735faa0fb862302126034d143da9截图.png)