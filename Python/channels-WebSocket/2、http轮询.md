**基于http轮询的聊天系统**

# HTML

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.js"></script>
    <style>
        .message{
            height: 600px;
            border: 1px solid #dddddd ;
            width: 100%;
        }
    </style>
</head>
<body>
    <div class="message">
    </div>
    <input type="text" placeholder="请输入" id="txt">
    <input type="button" value="发送" onclick="sendMessage();">
</body>
<script>
    function sendMessage() {
        var text=$("#txt").val();
        $.ajax({
            url:"{% url 'msg_get_post' %}",
            type:"post",
            data:{
                text:text
            },
            success:function(res){
                console.log("发送请求成功",res);
            }
        })
    }
    max_index=0;
    setInterval(function(){            #定时器，每两秒询问一次服务器
        //发送请求获取数据
        $.ajax({
            url:"{%  url 'msg_get_post' %}",
            type:"get",
            data:{
                index:max_index
            },
            success:function (res) {
                {#var dataDict=JSON.parse(res);#}
                console.log(res);
                max_index=res.max_index;
                $.each(res.data,function (index, item) {
                    console.log(index,item);
                    var tag=$("<div>");
                    tag.text(item);
                    $(".message").append(tag);
                })
            }
        })
    },2000)
</script>
</html>
```

# url

```
urlpatterns = [
    path('admin/', admin.site.urls),
    path("home/",views.home),
    path("send/message",views.MsgView.as_view(),name="msg_get_post")
]
```

# views

```
from django.shortcuts import render,HttpResponse
from django.http import  JsonResponse
from django.views import  View
import json
# Create your views here.
DB=["hello","heihei","hai"]
def home(request):
    return  render(request,"home.html")
class MsgView(View):
    def get(self,request):
        index=request.GET.get("index")
        index=int(index)
        content={
            "data":DB[index:],
            "max_index":len(DB),
        }
        return  JsonResponse(content)
    def post(self,request):
        text=request.POST.get("text")
        DB.append(text)
        return HttpResponse("ok")
```