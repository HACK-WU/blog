## vue脚手架配置代理

# 方法一

	在vue.config.js中添加如下配置：

```javascript
devServer:{
  proxy:"http://localhost:5000"
}
```

说明：

1. 优点：配置简单，请求资源时直接发给前端（8080）即可。

1. 缺点：不能配置多个代理，不能灵活的控制请求是否走代理。

1. 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求会转发给服务器 （优先匹配前端资源）

# 方法二

	编写vue.config.js配置具体代理规则：

```javascript
module.exports = {
    devServer: {
      '/api1': {// 匹配所有以 '/api1'开头的请求路径
        target: 'http://localhost:5000',// 代理目标的基础路径
        changeOrigin: true,             //默认值为true
        pathRewrite: {'^/api1': ''}     //路径重写，将url中，开头的apil路劲替换为空
        //ws:true 用于支持websocket
      },
      '/api2': {// 匹配所有以 '/api2'开头的请求路径
        target: 'http://localhost:5001',// 代理目标的基础路径
        changeOrigin: true,
        pathRewrite: {'^/api2': ''}
      }
    }
  }
}
/*
   changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000     #和请求的服务器地址相对
   changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:8080    #真实的自己代理服务器的地址
   changeOrigin默认值为true
*/
```

说明：

1. 优点：可以配置多个代理，且可以灵活的控制请求是否走代理。

1. 缺点：配置略微繁琐，请求资源时必须加前缀。