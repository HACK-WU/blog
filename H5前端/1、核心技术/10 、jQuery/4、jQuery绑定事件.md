### 事件

```python
// 第一种
    $('#d1').click(function () {
            alert('别说话 吻我')
    });
  
// 第二种(功能更加强大 还支持事件委托)
    $('#d2').on('click',function () {
            alert('借我钱买草莓 后面还你')
    })
```

*   克隆事件

    ```html
    <button id="d1">屠龙宝刀，点击就送</button>

    <script>
        $('#d1').on('click',function () {
            // console.log(this)  // this指代是当前被操作的标签对象
            // $(this).clone().insertAfter($('body'))  // clone默认情况下只克隆html和css 不克隆事件
            $(this).clone(true).insertAfter($('body'))  // 括号内加true即可克隆事件

        })
    </script>
    ```

*   自定义模态框

    ```python
    """
    模态框内部本质就是给标签移除或者添加上hide属性
    """
    ```

*   左侧菜单

    ```html
    <script>
        $('.title').click(function () {
            // 先给所有的items加hide
            $('.items').addClass('hide')
            // 然后将被点击标签内部的hide移除
            $(this).children().removeClass('hide')
        })
    </script>
    <!--尝试用一行代码搞定上面的操作-->
    ```

*   返回顶部

    ```python
    <script>
        $(window).scroll(function () {
            if($(window).scrollTop() > 300){
                $('#d1').removeClass('hide')
            }else{
                $('#d1').addClass('hide')
            }
        })
    </script>
    ```

*   自定义登陆校验

    ```python
    # 在获取用户的用户名和密码的时候 用户如果没有填写 应该给用户展示提示信息
    <p>username:
        <input type="text" id="username">
        <span style="color: red"></span>
    </p>
    <p>password:
        <input type="text" id="password">
        <span style="color: red"></span>
    </p>
    <button id="d1">提交</button>

    <script>
        let $userName = $('#username')
        let $passWord = $('#password')
        $('#d1').click(function () {
            // 获取用户输入的用户名和密码 做校验
            let userName = $userName.val()
            let passWord = $passWord.val()
            if (!userName){
                $userName.next().text("用户名不能为空")
            }
            if (!passWord){
                $passWord.next().text('密码不能为空')
            }
        })
        $('input').focus(function () {
            $(this).next().text('')
        })
    </script>
    ```

*   input实时监控

    ```python
    <input type="text" id="d1">

    <script>
        $('#d1').on('input',function () {
            console.log(this.value)  
        })
    </script>
    ```

*   hover事件

    ```python
    <script>
        // $("#d1").hover(function () {  // 鼠标悬浮 + 鼠标移开
        //     alert(123)
        // })

        $('#d1').hover(
            function () {
                alert('我来了')  // 悬浮
        },
            function () {
                alert('我溜了')  // 移开
            }
        )
    </script>
    ```

*   键盘按键按下事件

    ```html
    <script>
        $(window).keydown(function (event) {
            console.log(event.keyCode)
            if (event.keyCode === 16){
                alert('你按了shift键')
            }
        })
    </script>
    ```

# 今日作业

```python
"""
今日作业
必做
1.利用jQuery链式操作一句话完成菜单显隐展示
2.整理日考题目
3.整理今日内容完成博客书写
选做
1.预习前端框架Bootstrap
"""
```

