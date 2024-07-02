# 1、反向代理

实现隐藏真实服务的操作，起到一定安全作用

# 2、隐藏版本号

Nginx对外提供服务，为了避免被针对某个版本的漏洞进行攻击。经常做法是隐藏掉软件的版本信息。提供一定的安全性。

```
#将以下配置加入到http段配置中
server_tokens off
```

# 3、Https和CA

。。。。

# 4、重写pathinfo路径

Tip: Nginx支持pathinfo路径 重写方式

需求

```
```

重写成为

```
```

语法规则示例：

```
location / {
    if (!-e $request_filename) {
     # 捕获到所有路径信息重写为index.php的s参数，last需要匹配之后的location规则
        rewrite ^( .*)$  /index.php?s=$1 last;
    }
}
```