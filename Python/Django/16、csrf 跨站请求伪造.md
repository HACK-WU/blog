### csrf跨站请求伪造

```python
"""
钓鱼网站
	我搭建一个跟正规网站一模一样的界面(中国银行)
	用户不小心进入到了我们的网站，用户给某个人打钱
	打钱的操作确确实实是提交给了中国银行的系统，用户的钱也确确实实减少了
	但是唯一不同的时候打钱的账户不适用户想要打的账户变成了一个莫名其妙的账户

大学英语四六级
	考之前需要学生自己网站登陆缴费

内部本质
	我们在钓鱼网站的页面 针对对方账户 只给用户提供一个没有name属性的普通input框
	然后我们在内部隐藏一个已经写好name和value的input框

如何规避上述问题
	csrf跨站请求伪造校验
		网站在给用户返回一个具有提交数据功能页面的时候会给这个页面加一个唯一标识
		当这个页面朝后端发送post请求的时候 我的后端会先校验唯一标识，如果唯一标识不对直接拒绝(403 forbbiden)如果成功则正常执行	
"""
```

### 如何符合校验

```python
# form表单如何符合校验
<form action="" method="post">
    {% csrf_token %}
    <p>username:<input type="text" name="username"></p>
    <p>target_user:<input type="text" name="target_user"></p>
    <p>money:<input type="text" name="money"></p>
    <input type="submit">
</form>

# ajax如何符合校验
// 第一种 利用标签查找获取页面上的随机字符串
{#data:{"username":'jason','csrfmiddlewaretoken':$('[name=csrfmiddlewaretoken]').val()},#}
// 第二种 利用模版语法提供的快捷书写
{#data:{"username":'jason','csrfmiddlewaretoken':'{{ csrf_token }}'},#}
// 第三种 通用方式直接拷贝js代码并应用到自己的html页面上即可
data:{"username":'jason'}
```

```js
function getCookie(name) {
    var cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        var cookies = document.cookie.split(';');
        for (var i = 0; i < cookies.length; i++) {
            var cookie = jQuery.trim(cookies[i]);
            // Does this cookie string begin with the name we want?
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}
var csrftoken = getCookie('csrftoken');


function csrfSafeMethod(method) {
  // these HTTP methods do not require CSRF protection
  return (/^(GET|HEAD|OPTIONS|TRACE)$/.test(method));
}

$.ajaxSetup({
  beforeSend: function (xhr, settings) {
    if (!csrfSafeMethod(settings.type) && !this.crossDomain) {
      xhr.setRequestHeader("X-CSRFToken", csrftoken);
    }
  }
});
```

### csrf相关装饰器

```python
"""
1.网站整体都不校验csrf，就单单几个视图函数需要校验
2.网站整体都校验csrf，就单单几个视图函数不校验
"""
from django.views.decorators.csrf import csrf_protect,csrf_exempt
from django.utils.decorators import method_decorator
"""
csrf_protect  需要校验
    针对csrf_protect符合我们之前所学的装饰器的三种玩法
csrf_exempt   忽视校验
    针对csrf_exempt只能给dispatch方法加才有效
"""
# @csrf_exempt
# @csrf_protect
def transfer(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        target_user = request.POST.get('target_user')
        money = request.POST.get('money')
        print('%s给%s转了%s元'%(username,target_user,money))
    return render(request,'transfer.html')



from django.views import View

# @method_decorator(csrf_protect,name='post')  # 针对csrf_protect 第二种方式可以
# @method_decorator(csrf_exempt,name='post')  # 针对csrf_exempt 第二种方式不可以
@method_decorator(csrf_exempt,name='dispatch')
class MyCsrfToken(View):
    # @method_decorator(csrf_protect)  # 针对csrf_protect 第三种方式可以
    # @method_decorator(csrf_exempt)  # 针对csrf_exempt 第三种方式可以
    def dispatch(self, request, *args, **kwargs):
        return super(MyCsrfToken, self).dispatch(request,*args,**kwargs)

    def get(self,request):
        return HttpResponse('get')

    # @method_decorator(csrf_protect)  # 针对csrf_protect 第一种方式可以
    # @method_decorator(csrf_exempt)  # 针对csrf_exempt 第一种方式不可以
    def post(self,request):
        return HttpResponse('post')

```

### 补充知识点

```python
# 模块:importlib
import importlib
res = 'myfile.b'
ret = importlib.import_module(res)  # from myfile import b
# 该方法最小只能到py文件名
print(ret)
```

### 重要思想

```python
import settings
import importlib


def send_all(content):
    for path_str in settings.NOTIFY_LIST:  #'notify.email.Email'
        module_path,class_name = path_str.rsplit('.',maxsplit=1)
        # module_path = 'notify.email'  class_name = 'Email'
        # 1 利用字符串导入模块
        module = importlib.import_module(module_path)  # from notify import email
        # 2 利用反射获取类名
        cls = getattr(module,class_name)  # Email、QQ、Wechat
        # 3 生成类的对象
        obj = cls()
        # 4 利用鸭子类型直接调用send方法
        obj.send(content)
```

