live-server

两种安装方式：

```shell
全局安装 npm i live-server -g
本地安装  npm i live-server --save-dev
```

### 直接使用live-server

首先在项目下npm初始化:

然后可以选择全局安装或者本地安装live-server，然后在package.json的scripts属性中添加如下代码：

```
"scripts": {
    "server": "live-server ./ --port=8181 --host=localhost --proxy=/api:
}
```

中包括了代理设置

然后