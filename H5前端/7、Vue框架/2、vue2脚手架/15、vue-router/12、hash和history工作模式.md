### 路由器的两种工作模式

1. 对于一个url来说，什么是hash值？—— #及其后面的内容就是hash值。

1. hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。

1. hash模式：

1. 地址中永远带着#号，不美观 。

1. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。

1. 兼容性较好。

1. history模式：

1. 地址干净，美观 。

1. 兼容性和hash模式相比略差。

1. 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。

 

```javascript
const router =  new VueRouter({
    mode:'history',         //表示开始history模式，默认是hash模式
}   
```

# history模式下，使用nginx，判断前后端路由

在使用 Nginx 部署前端项目时，可以通过配置 Nginx 来判断前端项目发出的 URL 是前端路由还是后端路由。以下是一种常见的配置方法：

1. 首先，将前端项目的静态文件（HTML、CSS、JS等）放置在一个目录下，例如 

/var/www/html

1. 在 Nginx 的配置文件中，找到 server 块，通常位于 /etc/nginx/nginx.conf 或 /etc/nginx/conf.d/default.conf。在该 server 块中添加如下配置：

```nginx
location / {
    try_files $uri $uri/ /index.html;
}
location /api {
    proxy_pass 
}
```