# jQuery

```python
"""
jQuery内部封装了原生的js代码(还额外添加了很多的功能)
能够让你通过书写更少的代码 完成js操作 
类似于python里面的模块  在前端模块不叫模块  叫 “类库”

兼容多个浏览器的 你在使用jQuery的时候就不需要考虑浏览器兼容问题

jQuery的宗旨
	write less do more
	让你用更少的代码完成更多的事情

复习
	python导入模块发生了哪些事？
		导入模块其实需要消耗资源
	jQuery在使用的时候也需要导入
		但是它的文件非常的小(几十KB) 基本不影响网络速度

选择器
筛选器
样式操作
文本操作
属性操作
文档处理
事件
动画效果
插件
each、data、Ajax(重点 django部分学)

版本介绍

jQuery文件下载
	压缩  		容量更小
	未压缩
"""
# jQuery在使用之前 一定要确保已经导入了
```

### 针对导入问题

```python
# 1 文件下载到了本地 如何解决多个文件反复书写引入语句的代码
	可以借助于pycharm自动初始化代码功能完成自动添加
  	配置
    	编辑
      	file and code template
  """我不想下载jQuery文件 能不能使用呢？"""
  
# 2 直接引入jQuery提供的CDN服务(基于网络直接请求加载)
	CDN:内容分发网络
  	CDN有免费的也有收费的
    前端免费的cdn网站:
      	bootcdn
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
  """你的计算机必须要有网络"""
  
  
# jQuery基本语法
	jQuery(选择器).action()
  秉持着jQuery的宗旨 jQuery简写	$
  jQuery()  === $()

# jQuery与js代码对比
	eg:将p标签内部的文本颜色改为红色
   	// 原生js代码操作标签
		let pEle = document.getElementById('d1')
		pEle.style.color = 'red'

		// jQuery操作标签
		$('p').css('color','blue')
```

# 先学如何查找标签

### 基本选择器

```python
// id选择器
$('#d1')
w.fn.init [div#d1]0: div#d1length: 1__proto__: Object(0)
// class选择器
$('.c1')
w.fn.init [p.c1, prevObject: w.fn.init(1)]0: p.c1length: 1prevObject: w.fn.init [document]__proto__: Object(0)
// 标签选择器
$('span')
w.fn.init(3) [span, span, span, prevObject: w.fn.init(1)]

"""一定要区分开(重点)"""
// jQuery对象如何变成标签对象
undefined
$('#d1')[0]
<div id=​"d1">​…​</div>​
document.getElementById('d1')
<div id=​"d1">​…​</div>​
// 标签对象如何转jQuery对象
undefined
$(document.getElementById('d1'))
w.fn.init [div#d1]
```

### 组合选择器/分组与嵌套

```python
$('div')
w.fn.init(2) [div#d1, div.c1, prevObject: w.fn.init(1)]
$('div.c1')
w.fn.init [div.c1, prevObject: w.fn.init(1)]0: div.c1length: 1prevObject: w.fn.init [document]__proto__: Object(0)
$('div#d1')
w.fn.init [div#d1, prevObject: w.fn.init(1)]
$('*')
w.fn.init(19) [html, head, meta, title, meta, link, script, script, body, span, span, div#d1, span, p#d2, span, span, div.c1, span, span, prevObject: w.fn.init(1)]
               
$('#d1,.c1,p')  # 并列+混用
w.fn.init(3) [div#d1, p#d2, div.c1, prevObject: w.fn.init(1)]
              
$('div span')  # 后代
w.fn.init(3) [span, span, span, prevObject: w.fn.init(1)]
$('div>span')  # 儿子
w.fn.init(2) [span, span, prevObject: w.fn.init(1)]
$('div+span')  # 毗邻
w.fn.init [span, prevObject: w.fn.init(1)]
$('div~span')  # 弟弟
w.fn.init(2) [span, span, prevObject: w.fn.init(1)]
```

### 基本筛选器

```python
$('ul li')
w.fn.init(10) [li, li, li, li, li, li, li.c1, li, li#d1, li, prevObject: w.fn.init(1)]
               
$('ul li:first')  # 大儿子 
w.fn.init [li, prevObject: w.fn.init(1)]0: lilength: 1prevObject: w.fn.init [document]__proto__: Object(0)
               
$('ul li:last')  # 小儿子
w.fn.init [li, prevObject: w.fn.init(1)]0: lilength: 1prevObject: w.fn.init [document]__proto__: Object(0)
               
$('ul li:eq(2)')		# 放索引
w.fn.init [li, prevObject: w.fn.init(1)]0: lilength: 1prevObject: w.fn.init [document]__proto__: Object(0)
               
$('ul li:even')  # 偶数索引 0包含在内
w.fn.init(5) [li, li, li, li.c1, li#d1, prevObject: w.fn.init(1)]0: li1: li2: li3: li.c14: li#d1length: 5prevObject: w.fn.init [document]__proto__: Object(0)
              
$('ul li:odd')  # 奇数索引
w.fn.init(5) [li, li, li, li, li, prevObject: w.fn.init(1)]0: li1: li2: li3: li4: lilength: 5prevObject: w.fn.init [document]__proto__: Object(0)
              
$('ul li:gt(2)')  # 大于索引
w.fn.init(7) [li, li, li, li.c1, li, li#d1, li, prevObject: w.fn.init(1)]0: li1: li2: li3: li.c14: li5: li#d16: lilength: 7prevObject: w.fn.init [document]__proto__: Object(0)
              
$('ul li:lt(2)')  # 小于索引
w.fn.init(2) [li, li, prevObject: w.fn.init(1)]0: li1: lilength: 2prevObject: w.fn.init [document]__proto__: Object(0)
              
$('ul li:not("#d1")')  # 移除满足条件的标签
w.fn.init(9) [li, li, li, li, li, li, li.c1, li, li, prevObject: w.fn.init(1)]
         
$('div')
w.fn.init(2) [div, div, prevObject: w.fn.init(1)]
$('div:has("p")')  # 选取出包含一个或多个标签在内的标签
w.fn.init [div, prevObject: w.fn.init(1)]
```

### 属性选择器

```python
$('[username]')
w.fn.init(3) [input, input, p, prevObject: w.fn.init(1)]
$('[username="jason"]')
w.fn.init [input, prevObject: w.fn.init(1)]
$('p[username="egon"]')
w.fn.init [p, prevObject: w.fn.init(1)]

$('[type]')
w.fn.init(2) [input, input, prevObject: w.fn.init(1)]
$('[type="text"]')
w.fn.init(2) [input, input, prevObject: w.fn.init(1)]
```

### 表单筛选器

```python
$('input[type="text"]')
w.fn.init [input, prevObject: w.fn.init(1)]0: inputlength: 1prevObject: w.fn.init [document]__proto__: Object(0)
$('input[type="password"]')
w.fn.init [input, prevObject: w.fn.init(1)]

$(':text')  # 等价于上面第一个
w.fn.init [input, prevObject: w.fn.init(1)]0: inputlength: 1prevObject: w.fn.init [document]__proto__: Object(0)
$(':password')  # 等价于上面第二个
w.fn.init [input, prevObject: w.fn.init(1)]


:text
:password
:file
:radio
:checkbox
:submit
:reset
:button
...

表单对象属性
:enabled
:disabled
:checked
:selected
"""特殊情况"""
$(':checked')  # 它会将checked和selected都拿到
w.fn.init(2) [input, option, prevObject: w.fn.init(1)]0: input1: optionlength: 2prevObject: w.fn.init [document]__proto__: Object(0)
$(':selected')  # 它不会 只拿selected
w.fn.init [option, prevObject: w.fn.init(1)]
$('input:checked')  # 自己加一个限制条件
w.fn.init [input, prevObject: w.fn.init(1)]
```

