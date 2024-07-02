

```javascript
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Untitled Document</title>
    <link rel="stylesheet" type="text/css" href="style/css.css" />
    <script type="text/javascript">

        window.onload=function (ev) {
            function myClick(str,fun) {
                var btn=document.getElementById(str);
                btn.onclick=fun;
            }
            //创建一个“广州”节点，添加到#city下

               myClick("btn01",function () {
                   //创建广州节点<li>广州</li>
                   //创建li元素节点
                   /*
                     *document.createElement()
                     *  可以用于创建一个元素节点对象
                     *  它需要返回一个标签名作为参数，将会根据该标签名创建元素节点对象
                     *  并将创建好的对象作为返回值返回
                     */
                   var li = document.createElement("li");
                   //alert(li);
                   //创建文本节点
                    /*
                     *document.createTextNode()
                     * 可以用来创建一个文本节点对象
                     * 需要一个个文本内容作为参数，将会根据内容穿件文本节点，并将新的节点返回
                     */
                    //var gzText=document.createTextNode("广州");
                    //alert(gzText);
                    //将gzText设置为li的子节点
                   /*
                    *appendChild()
                    *   -向父节点中添加一个新的子节点
                    *   -用法：父元素.appendChild(子节点)
                    */
                   //li.appendChild(gzText);
                   //将广州添加到city下
                   var city=document.getElementById("city");
                    city.appendChild(li);
                    li.innerHTML="广州";
                   //city.innerHTML+="<li>广州</li>"

               })

            //将广州节点添加到#bj前面
            myClick("btn02",function () {
                //创建一个广州
                var li =document.createElement("li");
                var gtText=document.createTextNode("广州");
                li.appendChild(gtText);
                var bj=document.getElementById("bj");
                var city=document.getElementById("city");
                /*
                 *insertBefore()
                 *  -可以在指定的子节点前插入新的节点
                 *  -语法：
                 *      父节点.insertBefore(新节点，旧节点)；
                 */
                city.insertBefore(li,bj);
            })

            //使用“广州”节点替换bj节点
            myClick("btn03",function () {
                var li=document.createElement("li");
                var gtText=document.createTextNode("广州");
                li.appendChild(gtText);
                var bj=document.getElementById("bj");
                var city=document.getElementById("city");
                /*
                 *replaceChild()
                 *  -可以使用指定的子节点替换已有的子节点
                 *  -语法：父节点.replaceChild(新节点，旧节点）
                 */
                city.replaceChild(li,bj);
            })

            //删除#bj节点
            myClick("btn04",function () {
                var bj=document.getElementById("bj");
               // var city=document.getElementById("city");

                /*
                 *removeChild()
                 *  -可以删除一个子节点
                 *  -语法：父节点.removeChild(子节点)
                 */
                //city.removeChild(bj);
                bj.parentNode.removeChild(bj);
            })

            //读取#city内的HTML代码
            myClick("btn05",function () {
                var city=document.getElementById("city");
                alert(city.innerHTML);
            })
            //设置#bj内的HTML代码
            myClick("btn06",function () {
                //获取bj
                var bj=document.getElementById("bj");
                bj.innerHTML="昌平"
            })
        }

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

    </div>
</div>
<div id="btnList">
    <div><button id="btn01">创建一个"广州"节点,添加到#city下</button></div>
    <div><button id="btn02">将"广州"节点插入到#bj前面</button></div>
    <div><button id="btn03">使用"广州"节点替换#bj节点</button></div>
    <div><button id="btn04">删除#bj节点</button></div>
    <div><button id="btn05">读取#city内的HTML代码</button></div>
    <div><button id="btn06">设置#bj内的HTML代码</button></div>
    <div><button id="btn07">创建一个"广州"节点,添加到#city下</button></div>
</div>
</body>
</html>

```

