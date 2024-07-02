### 页面加载

```python
# 等待页面加载完毕再执行代码
window.onload = function(){
  // js代码
}


"""jQuery中等待页面加载完毕"""
# 第一种
$(document).ready(function(){
  // js代码
})
# 第二种
$(function(){
  // js代码
})
# 第三种
"""直接写在body内部最下方"""
```

### 动画效果

```python
$('#d1').hide(5000)
w.fn.init [div#d1]
$('#d1').show(5000)
w.fn.init [div#d1]
$('#d1').slideUp(5000)
w.fn.init [div#d1]
$('#d1').slideDown(5000)
w.fn.init [div#d1]
$('#d1').fadeOut(5000)
w.fn.init [div#d1]
$('#d1').fadeIn(5000)
w.fn.init [div#d1]
$('#d1').fadeTo(5000,0.4)
w.fn.init [div#d1]      
```

### 补充

```python
# each()
# 第一种方式
$('div')
w.fn.init(10) [div, div, div, div, div, div, div, div, div, div, prevObject: w.fn.init(1)]
$('div').each(function(index){console.log(index)})
VM181:1 0
VM181:1 1
VM181:1 2
VM181:1 3
VM181:1 4
VM181:1 5
VM181:1 6
VM181:1 7
VM181:1 8
VM181:1 9

$('div').each(function(index,obj){console.log(index,obj)})
VM243:1 0 <div>​1​</div>​
VM243:1 1 <div>​2​</div>​
VM243:1 2 <div>​3​</div>​
VM243:1 3 <div>​4​</div>​
VM243:1 4 <div>​5​</div>​
VM243:1 5 <div>​6​</div>​
VM243:1 6 <div>​7​</div>​
VM243:1 7 <div>​8​</div>​
VM243:1 8 <div>​9​</div>​
VM243:1 9 <div>​10​</div>​

# 第二种方式
$.each([111,222,333],function(index,obj){console.log(index,obj)})
VM484:1 0 111
VM484:1 1 222
VM484:1 2 333
(3) [111, 222, 333]
"""
有了each之后 就无需自己写for循环了 用它更加的方便
"""
# data()
"""
能够让标签帮我们存储数据 并且用户肉眼看不见
"""
$('div').data('info','回来吧，我原谅你了!')
w.fn.init(10) [div#d1, div, div, div, div, div, div, div, div, div, prevObject: w.fn.init(1)]
               
$('div').first().data('info')
"回来吧，我原谅你了!"
$('div').last().data('info')
"回来吧，我原谅你了!"
               
$('div').first().data('xxx')
undefined
$('div').first().removeData('info')
w.fn.init [div#d1, prevObject: w.fn.init(10)]
           
$('div').first().data('info')
undefined
$('div').last().data('info')
"回来吧，我原谅你了!"
```

