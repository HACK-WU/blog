Apache 配置静态缓存


所说的静态文件指的是图片、js、css 等文件，用户访问一个站点，其实大多数元素都是图片、js、


css 等，这些静态文件其实是会被客户端的浏览器缓存到本地电脑上的，目的就是为了下次再请求时


不再去服务器上下载，这样就加快了速度，提高了用户体验。但这些静态文件总不能一直缓存，它总


有一些时效性，那么就得设置这个过期时间。


# 1 、配置静态缓存


```javascript
# vim /usr/local/apache2/etc/httpd.conf
<IfModule mod_expires.c> #此模块默认未启用，请手动启用
ExpiresActive on
ExpiresByType image/gif "access plus 1 days"
ExpiresByType image/jpeg "access plus 24 hours"
ExpiresByType image/png "access plus 24 hours"
ExpiresByType text/css "now plus 2 hours"
ExpiresByType application/x-javascript "now plus 2 hours"
ExpiresByType application/javascript "now plus 2 hours"
ExpiresByType application/x-shockwave-flash "now plus 2 hours"
ExpiresDefault "now plus 0 min"
</IfModule>
```

或者使用 mod_headers 模块实现：该模块默认启用


```javascript
<IfModule mod_headers.c>
# htm,html,txt 类的文件缓存一个小时
<filesmatch "\.(html|htm|txt)$">

header set cache-control "max-age=3600"
</filesmatch>
# css, js, swf 类的文件缓存一个星期
<filesmatch "\.(css|js|swf)$">
header set cache-control "max-age=604800"
</filesmatch>
# jpg,gif,jpeg,png,ico,flv,pdf 等文件缓存一年
<filesmatch "\.(ico|gif|jpg|jpeg|png|flv|pdf)$">
header set cache-control "max-age=29030400"
</filesmatch>
</IfModule>
```

说明：这里的时间单位可以 days、 hours 甚至是 min，两种不同的方法，上面使用的是 mod_expires，


而下面用的是 mod_headers，要想使用这些模块，必须要事先已经支持。如何查看是否支持，使用命


令：


```javascript
# /usr/local/apache2/bin/apachectl -M
```



# 2 、重启服务器并验证


```javascript
/usr/local/apache2/bin/apachectl -t
/usr/local/apache2/bin/apachectl stop
/usr/local/apache2/bin/apachectl start
```

验证：


```javascript
# curl -x127.0.0.1:80 'http://www.sohu.com/image/a.jpg' -I
HTTP/1.1 200 OK
Date: Wed, 26 Oct 2016 03:51:26 GMT
Server: Apache/2.2.31 (Unix) PHP/5.5.38
Last-Modified: Tue, 31 May 2016 03:08:36 GMT
ETag: "46891b-16b-5341ab0597500"
Accept-Ranges: bytes
Content-Length: 363
Cache-Control: max-age=86400
Expires: Thu, 27 Oct 2016 03:51:26 GMT
Content-Type: image/jpg
```

