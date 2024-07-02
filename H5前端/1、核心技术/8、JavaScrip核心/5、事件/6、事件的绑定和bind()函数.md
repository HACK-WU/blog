

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        window.onload=function (ev) {
            /*
             *点击按钮以后弹出一个内容
             */
            //获取按钮对象
            var btn01=document.getElementById("btn01");
            /*
             *使用 对象.事件=函数()的形式绑定函数，
             *  它只能同时为一个元素的一个事件绑定一个函数，
             *  不能绑定多个，如果绑定多个，则会被覆盖
             */
            //为btn01绑定一个单机响应函数
            /*
             * btn01.onclick=function (ev1) {
                alert("1");
            }
             */
           /*
            *addEventListener()
            *   -通过这个方法也可以为元素绑定响应函数
            *   -参数：
            *       1、事件的字符串，不要on(相当于监视的类型）
            *       2、回调函数，当事件触发时该函数会被调用
            *       3、是否在捕获截断触发事件，需要一个布尔值，一般都传FALSE；
            *
            * 使用addEventListener(),可以同时为一个元素的一个事件绑定多个响应函数，
            *   这样当事件被触发时，响应函数将会按照函数的绑定顺序执行
            *
            * 这个方法在IE8及以下的浏览器
            */
           /*
            *  btn01.addEventListener("click",function (ev1) {
               alert("1");
           },false);
            btn01.addEventListener("click",function (ev1) {
                alert("2");
            },false);
            */

           /*
            *attachEvent()
            *   -在IE8中可以使用attachEvent()来绑定事件
            *   -参数：
            *       1、事件的字符串，不要on
            *       2、回调函数
            *
            *   -这个方法也可以同时为一个事件绑定多个处理函数，
            *       不同的是它是后绑定先执行，执行顺序和addEventListener()相反
            *
            *     btn01.attachEvent("onclick",function () {
                alert("1");
            });
            btn01.attachEvent("onclick",function () {
                alert("2");
            });
            */
            //定义一个函数，用来为指定元素绑定响应函数
            /*
             *addEventListener()中的this,是绑定事件的对象
             *attachEvent()中的this,是window
             *  需要统一两个方法的this
             */
            /*
             *参数：
             *  obj:要绑定事件的对象
             *  eventStr：  事件字符串(不要on）
             *  callback:   回调函数
             */
            function bind(obj,eventStr,callback) {

                if(obj.addEventListener){
                    //大部分浏览器兼容的方式
                    obj.addEventListener(eventStr,callback,false);
                }else{
                    /*
                     *this是由调用方式决定的
                     * callback.call(obj)
                     */
                    //IE8及以下
                    obj.attachEvent("on"+eventStr,function () {
                        //在匿名函数中调用callback
                        callback.call(obj);
                    });
                }
            }

            bind(btn01,"click",function () {
                alert(1)
            })
        }

    </script>
</head>
<body>
    <button id="btn01">点我一下</button>

</body>
</html>
```

