# 1、发行版本

1、Nginx社区免费版

[https://nginx.org/](https://nginx.org/)

2、NGINX+商业版

[https://www.nginx.com/](https://www.nginx.com/)

3、淘宝的tengine

[http://tengine.taobao.org/](http://tengine.taobao.org/)2

tengine是alibaba公司，在nginx的基础上，开发定制，更加服务自己业务的服务器软件。后来进行了开源。

# 2、tengine编译安装

```
#解压编译安装
shell > tar xvf tengine-2.3.0.tar.gz
shell > cd tengine-2.3.0
shell > ./configure --prefix=/usr/local/tengine --add-module=/root/tengine-2.3.0/ngx_http_footer_filter_module
shell > make && make install
shell > /usr/local/tengine/sbin/nginx -V    #查看默认加载的模块和参数信息
shell > /usr/local/tengine/sbin/nginx -m    #tengine 默认提供-m参数 查看已经编译加载的模块
```

案例: 通过tengine内置模块自动添加页脚信息标识服务器信息

使用ngx_http_footer_filter_module包含的语法

①修改配置文件并重载服务

```
shell > vim /usr/local/tengine/conf/nginx.conf
```

- 语法规则

```
#可定义在http、server、location中，建议在server中
#定义需要插入footer的响应类型（Response Content-Type) .
footer_types " text/plain" "text/css" "application/x-javascript";
出本 ONHTITPI向应Q正文结导市Tformat内容form中令有 会神大的有
footer "Shost_comment";
```

示例配置：

```
server{
    listen 80;
    server_name hackwu.cn;
    root html/tp5shop/public;
    #开启加载注释页脚信息
    footer_types " text/plain" "text/css" "application/x-javascript";
    footer "Shost_comment";
}
```

# 3、OpenResty安装

openresty 在nginx的基础上，结合lua脚本实现高并发等web平台。作者章亦春，

官方网址: [http://openresty.org/cn/](http://openresty.org/cn/)

- 编译安装步骤

```
#解压编译并安装
shell > tar xvf openresty-1.15.8.1.tar.gz
shell > cd openresty-1.15.8.1
shell > ./configure --prefix=/usr/local/openresty
shell > make && make install
shell > /usr/local/openresty/sbin/openresty -v  #查看默认编译参数及其模块
```

案例:使用lua模块语法

①修改配置文件

```
shell > vim /usr/local/openresty/nginx/conf/nginx.conf
```

- 示例配置

```
location / {
    root html;
    index index.html index.htm;
    #默认返回类型
    default_type text/html;
    #调用lua模块的语法
    content_by_lua 'ngx.say( "hello openresty" )';
}
```