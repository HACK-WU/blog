# 路由的params参数

1. 配置路由，声明接收params参数

```javascript
{
    path:'/home',
    component:Home,
    children:[
        {
            path:'news',
            component:News
        },
        {
            component:Message,
            children:[
                {
                    name:'xiangqing',
                    path:'detail/:id/:title', //使用占位符声明接收params参数,表示id和title是参数
                    component:Detail
                }
            ]
        }
    ]
}
```

1. 传递参数

```javascript
<!-- 跳转并携带params参数，to的字符串写法 -->
<router-link to="/home/message/detail/666/你好">跳转</router-link>
<!--使用js模板字符串的方式,sayhello是一个变量-->
<router-link :to="`/home/message/detail/${id}/${sayhello}`">跳转</router-link>
                
<!-- 跳转并携带params参数，to的对象写法 -->
<router-link 
    :to="{
        name:'xiangqing',
        params:{
           id:666,
            title:'你好'
        }
    }"
>跳转</router-link>
```

> **特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，****必须使用name配置****！**


1. 接收参数：

```javascript
$route.params.id
$route.params.title
```