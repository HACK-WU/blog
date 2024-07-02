**聊天应用 简易版**

# HTML

```python
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
   var ws=new WebSocket("ws://localhost:8080/ws/");
    ws.onopen=function (ev) {           //建立连接时触发
        console.log("成功建立了连接");
    };
    ws.onmessage=function (ev) {        //收到消息时触发
        {#console.log("接收到了消息",ev);     //ev 是一个对象，其中的“data”属性，封装了接收到的字符数据。#}
        {#console.log(ev.data);#}
        var tag=$("<div>").text(ev.data);
        $(".message").append(tag);
    };
   ws.onerror=function (ev) {
       console.log("连接出错了");
       ws.close()       //主动关闭连接
   } ;
   ws.onclose=function (ev) {
        console.log("连接关闭");
   };
   function sendMessage() {
       var data=$("#txt").val();
       ws.send(data);
       $("#txt").val("");
   }
</script>
</html>
```

# consumer

```python
# _*_ coding : utf-8 _*_
# @Time : 2023/5/11 22:43
# @Author : HackWu
# @File : consumers
# @Project : websocket
from  channels.generic.websocket import WebsocketConsumer
from channels.exceptions import StopConsumer
websocket_list=[]
class ChatConsumer(WebsocketConsumer):
    def websocket_connect(self, message):
        #有客户端来向后端发送WebSocket连接的请求时，自动触发
        #self，表示的是连接对象本身。每连接一个websocket，就会产生一个self对象，每次都是不一样的，
        # 就像django中的request对象一样。
        print("有连接来了")
        self.accept()   #表示允许与客户端连接
        websocket_list.append(self)
    def websocket_receive(self, message):
        #浏览器基于WebSocket向后端发送数据，自动触接收消息
        print(message)          #message是一个字典。包含了“type,text” 两个key.
            #type  表示连接的类型
            #text   就是接收到的字符数据。
        for item in websocket_list:
            item.send(message.get("text"))
        # self.close()              #表示服务端断开连接
    def websocket_disconnect(self, message):
        #客户端与服务端断开连接时，自动触发
        #客户端与服务端断开连接时，自动触发，除非之前已经有对象提前抛出了“StopConsumer” 异常，那么就不会再次执行此操作。
        print("客户端出错，即将断开连接")
        websocket_list.remove(self)
        raise  StopConsumer()
```