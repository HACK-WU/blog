# 路由的query参数

1. 传递参数

```javascript
<!-- 跳转并携带query参数，to的字符串写法 -->
<router-link to="/home/message/detail?id=666&title=你好">跳转</router-link>
<!--使用js模板字符串的方式-->
<router-link :to="`/home/message/detail?id=${id}&title=${sayhello}`">跳转</router-link> 
                                
<!-- 跳转并携带query参数，to的对象写法 -->
<router-link 
    :to="{
        path:'/home/message/detail',
        query:{
           id:666,
            title:'你好'
        }
    }"
>跳转</router-link>
```

1. 接收参数：

```javascript
this.$route.query.id
this.$route.query.title
```