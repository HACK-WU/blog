**nginx相关模块**

# 1、ngx_http_access_module

- 实现基于ip的访问控制功能

- 代码实例

```
server {
 ...
 deny  192.168.1.1;
 allow 192.168.1.0/24;
 allow 10.1.1.0/16;
 allow 2001:0db8::/32;
 deny all;    #禁止所有人访问
}
```

**allow写在deny的上面，否则allow失效。**

# 2、ngx_http_auth_basic_module模块

- 使用基于用户的访问控制，使用basic机制进行用户认证

- 代码示例

```
location / {
 auth_basic      "closed site";
 auth_basic_user_file conf/htpasswd;
}
htpasswd -bc /etc/nginx/conf/htpasswd admin 123456
将密码文件权限改为600观察会发生什么现象？
```

**yum install -y httpd-tools**

```
htpasswd工具介绍
    -c：创建一个密码文件
    -n：不会更新文件，显示文件内容信息
    -b：免交互式输入用户密码信息
    -i：读取密码采用标准输入方式，并不做检查
    -m：使用md5的加密算法
    -B：使用bcrypt对密码进行加密
    -C：使用bcrypt algorithm对密码进行加密
    -d：密码加密方式
    -s：加密方式
    -p：不进行加密
    -D：删除指定用户
```

# 3、ngx_http_stub_status_module模块

- 用于输出nginx的基本访问状态信息

- 代码示例

```
location = /basic_status {
 stub_status;
}
Active connections
The current number of active client connections including Waiting
connections.
accepts
The total number of accepted client connections.
handled
The total number of handled connections. Generally, the parameter value is
the same as accepts unless some resource limits have been reached (for
example, the worker_connections limit).
requests
The total number of client requests.
Reading
The current number of connections where nginx is reading the request header.
Writing
The current number of connections where nginx is writing the response back
to the client.
Waiting
The current number of idle client connections waiting for a request.
```

# 4、ngx_http_log_module模块

- 相关日志的设置

- 代码示例

```
log_format compression '$remote_addr - $remote_user [$time_local] '
           '"$request" $status $bytes_sent '
           '"$http_referer" "$http_user_agent" "$gzip_ratio"';
access_log /spool/logs/nginx-access.log compression buffer=32k;
----
$remote_addr与$http_x_forwarded_for: 用以记录客户端的ip地址；
$remote_user：用来记录客户端用户名称；
$time_local：用来记录访问时间与时区；
$request：用来记录请求的url与http协议；
$status：用来记录请求状态；成功是200
$body_bytes_s ent：记录发送给客户端文件主体内容大小；
$http_referer：用来记录从那个页面链接访问过来的；
$http_user_agent：记录客户端浏览器的相关信息
```

# 5、ngx_http_gzip_module模块

- 使用gzip方式压缩响应的过滤器

- 代码示例

```
gzip      on;
gzip_min_length 1000;
gzip_proxied  expired no-cache no-store private auth;
gzip_types   text/plain application/xml;
```

# 6、ngx_http_ssl_module模块

## 1）定义https的相关配置

- 代码示例

```
http {
 ...
 server {
   listen        443 ssl;
   keepalive_timeout  70;
   ssl_protocols    TLSv1 TLSv1.1 TLSv1.2;
   ssl_ciphers     AES128-SHA:AES256-SHA:RC4-SHA:DES-CBC3-SHA:RC4-
MD5;
   ssl_certificate   /usr/local/nginx/conf/cert.pem;
   ssl_certificate_key /usr/local/nginx/conf/cert.key;
   ssl_session_cache  shared:SSL:10m;
   ssl_session_timeout 10m;
   ...
 }
```

## 2）自签名证书

- 生成ca证书

```
cd /apps/nginx
mkdir certs && cd certs
openssl req -newkey rsa:4096 -nodes -sha256 -keyout ca.key -x509 -days 3650 -
out ca.crt
```

- 生成证书请求文件

```
openssl req -newkey rsa:4096 -nodes -sha256 -keyout iproute.cn.key -out
iproute.cn.csr
```

- 签发证书

```
openssl x509 -req -days 36500 -in iproute.cn.csr -CA ca.crt -CAkey ca.key -
CAcreateserial -out iproute.cn.crt
cat iproute.cn.crt ca.crt > iproute.crt
```

- 验证证书内容

```
openssl x509 -in iproute.cn.crt -noout -text 
```

## 3）Nginx证书配置

```
server {
listen 80;
listen 443 ssl;
ssl_certificate /apps/nginx/certs/iproute.crt;
ssl_certificate_key /apps/nginx/certs/iproute.cn.key;
ssl_session_cache shared:sslcache:20m;
ssl_session_timeout 10m;
root /data/nginx/html;
}
```

# 7、ngx_http_rewrite_module模块

- 将用户请求的URI基于regex所描述的模式进行检查，而后完成替换

# 8、ngx_http_referer_module模块

- 定义referer首部的合法可用值