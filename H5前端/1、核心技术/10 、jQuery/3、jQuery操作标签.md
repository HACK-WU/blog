# 内容详细

* ### 操作标签

  ```python
  # 操作类
  """
  js版本														jQuery版本
  classList.add()										addClass()
  classList.remove()								removeClass()
  classList.contains()							hasClass()
  classList.toggle()								toggleClass()
  """
  
  # css操作
  <p>111</p>
  <p>222</p>
  """一行代码将第一个p标签变成红色第二个p标签变成绿色"""
  $('p').first().css('color','red').next().css('color','green')
  # jQuery的链式操作 使用jQuery可以做到一行代码操作很多标签
  # jQuery对象调用jQuery方法之后返回的还是当前jQuery对象 也就可以继续调用其他方法
  class MyClass(object):
      def func1(self):
          print('func1')
          return self
  
      def func2(self):
          print('func2')
          return self
  obj = MyClass()
  obj.func1().func2()
  
  # 位置操作
  offset()  # 相对于浏览器窗口
  position()  # 相对于父标签
  
  scrollTop()		# 需要了解
  	$(window).scrollTop()
    0
    $(window).scrollTop()
    969
    $(window).scrollTop()  # 括号内不加参数就是获取
    1733
    $(window).scrollTop(0)  # 加了参数就是设置
    n.fn.init [Window]
    $(window).scrollTop(500)
    n.fn.init [Window]
  scrollLeft()
  
  # 尺寸
  $('p').height()  # 文本
  20
  $('p').width()
  1670
  $('p').innerHeight()  # 文本+padding
  26
  $('p').innerWidth()
  1674
  $('p').outerHeight()  # 文本+padding+border
  26
  $('p').outerWidth()
  1674
  
  
  # 文本操作
  """
  操作标签内部文本
  js											jQuery
  innerText								text()  括号内不加参数就是获取加了就是设置
  innerHTML								html()
  
  $('div').text()
  "
      
          有些话听听就过去了，不要在意，都是成年人!
      
  "
  $('div').html()
  "
      <p>
          有些话听听就过去了，不要在意，都是成年人!
      </p>
  "
  $('div').text('你们都是我的大宝贝')
  w.fn.init [div, prevObject: w.fn.init(1)]
  $('div').html('你个臭妹妹')
  w.fn.init [div, prevObject: w.fn.init(1)]
  $('div').text('<h1>你们都是我的大宝贝</h1>')
  w.fn.init [div, prevObject: w.fn.init(1)]
  $('div').html('<h1>你个臭妹妹</h1>')
  w.fn.init [div, prevObject: w.fn.init(1)]
  """
  # 获取值操作
  """
  js													jQuery
  .value											.val()
  """
  $('#d1').val()
  "sasdasdsadsadad"
  $('#d1').val('520快乐')  # 括号内不加参数就是获取加了就是设置
  
  w.fn.init [input#d1]
  $('#d2').val()
  "C:\fakepath\01_测试路由.png"
  $('#d2')[0].files[0]  # 牢记两个对象之间的转换
  File {name: "01_测试路由.png", lastModified: 1557043083000, lastModifiedDate: Sun May 05 2019 15:58:03 GMT+0800 (中国标准时间), webkitRelativePath: "", size: 28733, …}
             
             
  # 属性操作
  """
  js中																jQuery
  setAttribute()											attr(name,value)
  getAttribute()											attr(name)
  removeAttribute()										removeAttr(name)
  
  在用变量存储对象的时候 js中推荐使用	
  	XXXEle			标签对象
  jQuery中推荐使用
  	$XXXEle			jQuery对象
  """
  let $pEle = $('p')
  undefined
  $pEle.attr('id')
  "d1"
  $pEle.attr('class')
  undefined
  $pEle.attr('class','c1')
  w.fn.init [p#d1.c1, prevObject: w.fn.init(1)]
  $pEle.attr('id','id666')
  w.fn.init [p#id666.c1, prevObject: w.fn.init(1)]
  $pEle.attr('password','jason123')
  w.fn.init [p#id666.c1, prevObject: w.fn.init(1)]
  $pEle.removeAttr('password')
  w.fn.init [p#id666.c1, prevObject: w.fn.init(1)]       
        
             
  """
  对于标签上有的能够看到的属性和自定义属性用attr
  对于返回布尔值比如checkbox radio option是否被选中用prop
  """
             
  $('#d3').attr('checked')
  "checked"
  $('#d2').attr('checked')
  undefined
  $('#d2').attr('checked')
  undefined
  $('#d4').attr('checked')
  undefined
  $('#d3').attr('checked')
  "checked"
  $('#d3').attr('checked','checked')  # 无效
  w.fn.init [input#d3]
             
             
  $('#d2').prop('checked')
  false
  $('#d2').prop('checked')
  true
  $('#d3').prop('checked',true)
  w.fn.init [input#d3]
  $('#d3').prop('checked',false)
  w.fn.init [input#d3]
             
             
  # 文档处理
  """
  js																		jQuery
  createElement('p')										$('<p>')
  appendChild()													append()
  
  """
  let $pEle = $('<p>')
  $pEle.text('你好啊 草莓要不要来几个?')
  $pEle.attr('id','d1')          
  $('#d1').append($pEle)  # 内部尾部追加
  $pEle.appendTo($('#d1')) 
             
  $('#d1').prepend($pEle)  # 内部头部追加
  w.fn.init [div#d1]
  $pEle.prependTo($('#d1'))
  w.fn.init [p#d1, prevObject: w.fn.init(1)]
           
  $('#d2').after($pEle)  # 放在某个标签后面
  w.fn.init [p#d2]
  $pEle.insertAfter($('#d1'))
          
  $('#d1').before($pEle)
  w.fn.init [div#d1]
  $pEle.insertBefore($('#d2'))
  
  $('#d1').remove()  # 将标签从DOM树中删除
  w.fn.init [div#d1]
             
  $('#d1').empty()  # 清空标签内部所有的内容
  w.fn.init [div#d1]
  ```

















