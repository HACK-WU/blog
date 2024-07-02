# myForms.py

```python
# _*_ coding : utf-8 _*_
# @Time : 2022/5/4 14:30
# @Author : HackWu
# @File : myforms
# @Project : BBS
from django import forms
from app01 import models
from django.core.exceptions import ValidationError
class MyForm(forms.Form):
    username=forms.CharField(label="用户名",min_length=3,max_length=8,
                             error_messages={
                                 "required":"用户名不能为空",
                                 "min_length":"用户名最少3位",
                                 "max_length":"用户名最多8位"
                             },
                             #还需要让标签有bootstrap样式
                             widget=forms.widgets.TextInput(attrs={"class":"form-control"})
                             )
    password=forms.CharField(label="密码",min_length=3,
                             error_messages={
                                 "required":"密码不能为空",
                                 "min_length":"密码最少3位",
                             },
                             #还需要让标签有bootstrap样式
                             widget=forms.widgets.PasswordInput(attrs={"class":"form-control"}))
    confirm_password=forms.CharField(label="确认密码",min_length=3,
                                     error_messages={
                                         "required":"密码不能为空",
                                         "min_length":"密码最少3位",
                                     },
                                     #还需要让标签有bootstrap样式
                                     widget=forms.widgets.PasswordInput(attrs={"class":"form-control"}))
    email=forms.EmailField(label="邮箱",
                           error_messages={
                             "required":"邮箱不能为空",
                               "invalid":"邮箱格式不正确",
                           },
                           widget=forms.widgets.EmailInput(attrs={"class":"form-control"})
                            )
    def clean_username(self):
        username=self.cleaned_data.get("username")
        #去数据库中校验
        is_exists=models.UserInfo.objects.filter(username=username)
        if is_exists:
            #提示信息
           raise ValidationError("用户名已存在")
        return username
    #全局钩子
    def clean(self):
        password=self.cleaned_data.get("password")
        confirm_password=self.cleaned_data.get("confirm_password")
        if not password==confirm_password:
            raise ValidationError("false")
        return self.cleaned_data
```

# register.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
</head>
<body>
<div class="container-fluid">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <h1 class="text-center">用户注册</h1>
            
<!--#############################################表单组件核心部分##########################################-->
            <form id="myform">  <!--这里我们不使用form表单提交数据，只是单纯使用一下form标签而已-->
               {% csrf_token %}
                 {% for form in form_obj %}
                    <div class="form-group ">
                        <label for="{{ form.auto_id }}">{{ form.label }}</label>
                    {{ form }}
                    <span style="color:red" class="pull-right">{{ form.errors.0 }}</span>   <!--报错信息-->
                    </div>
                {% endfor %}
            <input type="button"  value="注册" class="btn-primary pull-right" id="id_commit">
            </form>
 <!--end #############################################表单组件部分##########################################-->
        </div>        
    </div>    
</div>
<script>
//*******************************点击注册，提交用户信息*********************************************
    $("#id_commit").click(function () {
        //我们发送的数据，既包括普通键值对，又包括文件
        let formDataObj=new FormData();
        /*
         *当需要向后端提交的数据，包含文件时，不能使用字典的形式，因为字典知识存储简单的字符串信息。
         *  -文件，一般都是以二进制的形式进行存储。
         *  -这个时候可以将文件信息，存储在FormData()对象中。然后然后通过传递formData()对象，将信息发送给后端。
         *  -FormData()对象类似于字典，也是键值对的形式存储信息，
         *      -但是FormData()的键值可以存放二进制信息。而字典，一般只能存储简单的字符信息。
         *
         */
        {            #console.log($("#myform").serializeArray()) //将form表单内一组键值对封装成一个字典，然后每个字段又封装在一个数组中#}
        $.each($("#myform").serializeArray(),function(index,obj) {  //index,就是数组的索引；obj,对应索引的元素
            formDataObj.append(obj.name, obj.value)  //将表单中的数据，以键值对的方式存放在FormData()对象中
        });
        formDataObj.append('avatar',$('#myfile')[0].files[0]);
        $.ajax({
            url:"",
            type:"post",
            data:formDataObj,
            contentType:false,
            processData:false,
            /*
             *  http协议，默认的请求头一般是application/x-www-form-urlencoded，只能传输简单字符信息。
             *  当传送的信息，包含有文件时，需要更改请求头contentType，这里改为False。
             *      - contentType:false,
             *      - processData:false,
             */
            success:function(args){   //args就是后端传来的信息，是个JsonResponse类型，类似于字典
              if (args.code==1000){    //判断状态码，code=1000表示验证通过，这是后端自己定义的。
                  //跳转到登录页面
                  window.location.href=args.url
              }else{
                //如何将对应的错误提示展示到对应的input框下面
                //forms组件渲染的标签的id值都是 id_字段名
                  $.each(args.msg,function(index,obj){  //args.msg,报错信息是一个序列。
                      {#console.log(index,obj)#}
                      //index就是对应的forms类中各个数据字段的名称。
                      //obj 存放的就是报错信息。
                      if (obj[0]=="两次密码不一致"){
                          $("#id_confirm_password").next().text("两次密码不一致").parent().addClass("has-error")
                      }
                      let targetId ="#id_"+index;
                      $(targetId).next().text(obj[0]).parent().addClass("has-error")    //addClass()表示增加class属性值的内容
                      /*
                       *$("target"),next()   表示这个标签的下一个标签，所以指的就是<span>标签。也就是报错信息
                       * $("span").text(obj[0])表示更改这个标签里的内容，改为报错信息。
                       * $("span").parent()表示这个标签的父标签，也就是<div>
                       * $("div").addClass("has-error") #表示增加这个标签class属性的内容。
                       */
                  })
              }
            }
        })
    });
//****************************给input标签，绑定焦点事件**********************************
    $("input").focus(function () {
        //将input下面的span标签和外面的div标签修改内容及属性
        $(this).next().text("").parent().removeClass("has-error")   //removeClass()表示移除class属性的某个内容。
    })
</script>
</body>
</html>
```

# views.py

```python
def fun(request):
    form_obj=MyRegForm()    #这是用于渲染前端标签以及检验数据的forms对象，
    if request.method=="POST":
        back_dic={"code":1000,"msg":""}
        '''
            back_dic字典是用于返回给前端的响应信息。初始内容为：
                code：1000   状态码，1000表示格式正确，意味告诉前端，数据校验是否通过
                msg:" "     报错信息，初始为空。
        '''
        form_obj=MyRegForm(request.POST)    #将数据传入forms对象。
        '''
            request.POST中存放的知识普通的键值对字符信息。所以不包含文件数据
            request.FILES中存放的就是二进制的文件信息，也是以键值对的形式
        '''
        #判断数据是否合法
        if form_obj.is_valid():
            '''
            froms.is_valid()  返回值是boolean,判断数据校验是否全都通过
                -全部通过返回True,否则返回False
                
                校验通过的数据，会被存放在forms.cleaned_data  对象中，这个对象是一个字典
                校验失败的数据，报错信息会被存放在forms.errors 对象中，这个对象是一个序列
            '''
    
            # {'username': 'hack', 'password': '123', 'confirm_password': '123', 'email': '123@qq.com'}
            cleaned_data=form_obj.cleaned_data  #将校验通过的数据字典赋值给一个变量
            #将字典里面的confirm_password键值对删除
            cleaned_data.pop("confirm_password")
            # {'username': 'hack', 'password': '123', 'email': '123@qq.com'}
            #用户头像
            file_obj=request.FILES.get("avatar")
            '''
            针对用户头像，一定要判断是否传值，不能直接添加到字典里面
            '''
            if file_obj:
                print("获取到头像！！")
                cleaned_data["avatar"]=file_obj
            #直接操作数据库，保存数据
            models.UserInfo.objects.create_user(**cleaned_data)
            back_dic["url"]="/login/"   #数据校验通过，告诉前端通过了之后，页面跳转的地址。
            print(form_obj.cleaned_data)
            return JsonResponse(back_dic)   #返回一个josn数据类型给前端
        else:
            back_dic["code"]=2000   #code：2000，表示校验失败的状态码
            back_dic["msg"]=form_obj.errors     #全局报错信息，里面可以封装所有的字段的报错信息。
            return JsonResponse(back_dic)
    return render(request,"register.html",locals())
```