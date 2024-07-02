# 1、css.css

```javascript
@CHARSET "UTF-8";

body {
   width: 800px;
   margin-left: auto;
   margin-right: auto;
}

button {
   width: 300px;
   margin-bottom: 10px;
}

#btnList {
   float:left;
}

#total{
   width: 450px;
   float:left;
}

ul{
   list-style-type: none;
   margin: 0px;
   padding: 0px;
}

.inner li{
   border-style: solid;
   border-width: 1px;
   padding: 5px;
   margin: 5px;
   background-color: #99ff99;
   float:left;
}

.inner{
   width:400px;
   border-style: solid;
   border-width: 1px;
   margin-bottom: 10px;
   padding: 10px;
   float: left;
}

```



---

## 2、xxx.html

```javascript
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Untitled Document</title>
    <link rel="stylesheet" type="text/css" href="style/css.css" />
    <script type="text/javascript">

        /*
         * 定义一个函数，专门用来为指定元素绑定单击响应函数
         *     参数：
         *        idStr 要绑定单击响应函数的对象的id属性值
         *        fun 事件的回调函数，当单击元素时，该函数将会被触发
         */
        function myClick(idStr , fun){
            var btn = document.getElementById(idStr);
            btn.onclick = fun;
        }

        window.onload = function(){

            //为id为btn01的按钮绑定一个单击响应函数
            var btn01 = document.getElementById("btn01");
            btn01.onclick = function(){
                //查找#bj节点
                var bj = document.getElementById("bj");
                //打印bj
                //innerHTML 通过这个属性可以获取到元素内部的html代码
                alert(bj.innerHTML);
            };

            var btn02=document.getElementById("btn02");
            btn02.onclick=function (ev) {
                //查找所有li节点
                //getElementsByTagName()可以根据标签名获取一组元素节点对象
                //这个方法会返回一个类数组对象，所查询到的元素都封装在对象中
                var lis=document.getElementsByTagName("li");
                //打印lis
                //alert(lis.length)
                //遍历lis
                for(var i=0;i<lis.length;i++){
                    alert(lis[i].innerHTML)
                }
            }

            var btn03=document.getElementById("btn03");
            btn03.onclick=function (ev) {
                //查询name=gender的所有节点
                var inputs=document.getElementsByName("gender");
                //alert(inputs.length)
                /*
                 *如果需要获取元素的属性，只需要“元素.属性名”，即可
                 *  注意：class属性不能采用这种方式
                 *      读取class属性是，需要使用“元素.className”
                 */
                for(var i=0;i<inputs.length;i++){
                    alert(inputs[i].value);
                }
            }

            var btn04=document.getElementById("btn04");
            btn04.onclick=function (ev) {
                //获取id为city的元素
                var city=document.getElementById("city");
                //获取city下所有li节点
                var lis=city.getElementsByTagName("li");
                for(var i=0;i<lis.length;i++){
                    alert(lis[i].innerHTML);
                }
            }

            var btn05=document.getElementById("btn05");
            btn05.onclick=function (ev) {
                var city=document.getElementById("city");
                var cns=city.childNodes;
                //会获取包括文本节点在内的所有节点
                //alert(cns.length)

                var cns2=city.children;
                alert(cns2.length)

            }

            var btn06=document.getElementById("btn06");
            btn06.onclick=function (ev) {
                var phone=document.getElementById("phone");
                var fir=phone.firstChild;
                alert(fir);
            }
            
            /*
             *定义一个函数，专门用来为指定元素绑定单机响应函数
             *  参数：
             *      idStr，要绑定单机响应函数的对象id属性值
             *      fun,事件的回调函数
             */

            function myClick(idStr,fun) {
                var btn=document.getElementById(idStr);
                btn.onclick=fun;
            }

            myClick("btn07",function () {
                //获取id为bj的节点
                var bj=document.getElementById("bj");
                //返回#bj的父节点
                var pn=bj.parentNode;
                alert(pn.innerText)
            })

            myClick("btn08",function () {
                //返回#id为android的前一个兄弟节点
                var and=document.getElementById("android");
                var ps=and.previousSibling;
                alert(ps.innerHTML)
            })

            myClick("btn09",function () {
                var um=document.getElementById("username");
                alert(um.value)
            })
            myClick("btn10",function () {
                var um=document.getElementById("username");
                um.value="今天天气不错"
            })
            myClick("btn11",function () {
                var bj=document.getElementById("bj");
               alert(bj.innerText)
            })
        };


    </script>
</head>
<body>
<div id="total">
    <div class="inner">
        <p>
            你喜欢哪个城市?
        </p>

        <ul id="city">
            <li id="bj">北京</li>
            <li>上海</li>
            <li>东京</li>
            <li>首尔</li>
        </ul>

        <br>
        <br>

        <p>
            你喜欢哪款单机游戏?
        </p>

        <ul id="game">
            <li id="rl">红警</li>
            <li>实况</li>
            <li>极品飞车</li>
            <li>魔兽</li>
        </ul>

        <br />
        <br />

        <p>
            你手机的操作系统是?
        </p>

        <ul id="phone"><li>IOS</li><li id="android">Android</li><li>Windows Phone</li></ul>
    </div>

    <div class="inner">
        gender:
        <input class="hello" type="radio" name="gender" value="male"/>
        Male
        <input class="hello" type="radio" name="gender" value="female"/>
        Female
        <br>
        <br>
        name:
        <input type="text" name="name" id="username" value="abcde"/>
    </div>
</div>
<div id="btnList">
    <div><button id="btn01">查找#bj节点</button></div>
    <div><button id="btn02">查找所有li节点</button></div>
    <div><button id="btn03">查找name=gender的所有节点</button></div>
    <div><button id="btn04">查找#city下所有li节点</button></div>
    <div><button id="btn05">返回#city的所有子节点</button></div>
    <div><button id="btn06">返回#phone的第一个子节点</button></div>
    <div><button id="btn07">返回#bj的父节点</button></div>
    <div><button id="btn08">返回#android的前一个兄弟节点</button></div>
    <div><button id="btn09">返回#username的value属性值</button></div>
    <div><button id="btn10">设置#username的value属性值</button></div>
    <div><button id="btn11">返回#bj的文本值</button></div>
</div>
</body>
</html>

```

