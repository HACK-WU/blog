**Apache+openssl 实现 https（重点）**

HTTPS（全称：Hypertext Transfer Protocol Secure，超文本传输安全协议），是以安全为目标的 HTTP

通道，简单讲是 HTTP 的安全版。即 HTTP 下加入 SSL 层，用于安全的 HTTP 数据传输。这个系统被

内置于浏览器中，提供了身份验证与加密通讯方法。现在它被广泛用于万维网上安全敏感的通讯，例

如交易支付方面。



# **1. 准备工作：**

**1）检查 Apache 是否支持 SSL，检查相应模块是否安装，若安装则将模块启用**

- 打开主配置文件: prefix/apache2/etc/httpd.conf   搜索ssl

```javascript
</IfModule>                                 #若显示有此代码，则已经安装相应的模块

# Secure (SSL/TLS) connections
#Include etc--with-included-apr/extra/httpd-ssl.conf
#
# Note: The following must must be present to support
#       starting without SSL on platforms with no /dev/random equivalent
#       but a statically compiled-in mod_ssl.
#
<IfModule ssl_module>
```



- 启用模块，

```javascript
LoadModule ssl_module modules/mod_ssl.so            #去掉注释，大约在120行左右
```

**2）模块存放目录：/usr/local/apache2/modules**

```javascript
cd  /usr/local/apache2/modules
[root@www modules]# ls mod_ssl*
mod_ssl.so                      #存在，检查完毕
```



**3）重启：apachectl  restart**

**4）检查模块 是否 启用：apachectl -M**

```javascript
[root@www modules]# apachectl  -M   #若有如下表示已重启
Loaded Modules:  

 ssl_module (shared)
 unixd_module (shared)
 status_module (shared)
 autoindex_module (shared)
 dir_module (shared)
 alias_module (shared)
 rewrite_module (shared)
 php7_module (shared)
```



# **2. CA 证书申请：**

- ****

```javascript
openssl genrsa -out ca.key 1024
```

#建立服务器私钥，生成 RSA 密钥

- 

```javascript
openssl req -new -key ca.key -out atguigu.csr
```

#需要依次输入国家，地区，城市，组织，组织单位，Email 等信息。最重要的是有一个 common

name，可以写你的名字或者域名。如果为了 https 申请，这个必须和域名吻合，否则会引发浏览

器警报。生成的 csr 文件交给 CA 签名后形成服务端自己的证书

- 

```javascript
openssl x509 -req -days 365 -sha256 -in atguigu.csr -signkey ca.key -out atguigu.crt
```

#使用 CA 服务器签发证书，设置证书的有效期等信息

**注意 1：生成完秘钥和证书文件后，将文件存放在 Apache 的安装目录下的 cert 目录下**

**注意 2：在生产环境中必须要在 https 证书厂商注册（否则浏览器不识别）**



# **3. 配置 文件 修改： ：**

## **a. 调用 ssl 模块用 ，并启用 ssl 独立配置文件**

```javascript
# vim /usr/local/apache2/etc/httpd.conf
LoadModule ssl_module modules/mod_ssl.so 	#取消注释
Include etc/extra/httpd-ssl.conf	        #取消注释
```

## **b. 修改 conf/extra/httpd-ssl.conf 配置文件 ，调用证书等文件**

```javascript
#添加 SSL 协议支持协议，去掉不安全的协议
SSLProtocol all -SSLv2 -SSLv3
#修改加密套件如下
SSLCipherSuite HIGH:!RC4:!MD5:!aNULL:!eNULL:!NULL:!DH:!EDH:!EXP:+MEDIUM    #将原文的进行注释，换成这个
SSLHonorCipherOrder on
#证书公钥配置（签字的）
SSLCertificateFile cert/atguigu.crt		#将原文的注释掉，换成这个
#证书私钥配置
SSLCertificateKeyFile cert/ca.key                   #将原文的注释掉，换成这个
```

## **c. 修改主配置文件 ，添加虚拟主机**

```javascript
<VirtualHost *:443>
# DocumentRoot 目录位置要和 httpd.conf 里面的一致
DocumentRoot "/usr/local/apache2/htdocs"
ServerName localhost:443
SSLCertificateFile cert/atguigu.crt
SSLCertificateKeyFile cert/ca.key
SSLCertificateChainFile cert/atguigu.crt
</VirtualHost>
```

# **4. 结果验证： ：**

```javascript
# apachectl -t #检查配置文件语法
# apachectl restart #重启 apache，并测试是否可以使用 https 访问
```

**报错提示**

SSLSessionCache: 'shmcb' session cache not supported (known names: ). Maybe you need to load the

appropriate socache module (mod_socache_shmcb?).

**解决方案：**



# **5. 强制跳转 https ：**

有些时候为了安全 ，用 网站不允许使用 http 访问用 ，仅允许使用 https 访问 ，目的是为了更加安全

在 http 部分的目录权限标签中添加一下内容

```javascript
DocumentRoot  "/usr/local/apache2/htdocs"              
<Directory "/usr/local/apache2/htdocs">		 #在主文件中找到这个位置，增加如下信息
……
RewriteEngine on			 #开启转发规则
RewriteCond %{SERVER_PORT} !^443$	 #检查访问端口只要目标不是443的
RewriteRule ^(.*)?$ https://%{SERVER_NAME}/$1 [R=301,L]	 #全都使用https重新访问
</Directory>
```



**在做 后面 实验时为了更加方便理解把 ，我们可以先把 https 关闭掉**

需要关闭：跳转&虚拟主机&ssl 配置文件调用