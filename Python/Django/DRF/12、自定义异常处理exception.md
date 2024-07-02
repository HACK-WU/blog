# 四 异常处理 Exceptions

REST framework提供了异常处理，我们可以自定义异常处理函数。

## 4.1 使用方式

```python
from rest_framework.views import exception_handler
from rest_framework.response import Response
from rest_framework import status
def custom_exception_handler(exc, context):
    # context是上下文信息，里面包含了是哪一个CBV发生了错误，可以通过context['view']的方式获取到
    #log.error('view是：%s ，错误是%s' % (context['view'].__class__.__name__, str(exc)))
    
    # 先调用REST framework默认的异常处理方法获得标准错误响应对象
    response = exception_handler(exc, context)
    # 在此处补充自定义的异常处理
    if response is None:
        return Response(data={"status":999,'msg':str(exc)},status=status.HTTP_400_BAD_REQUEST)
    else:
        status_code=response.status_code
        return Response(data={"status":200,'msg':response.data.get("detail")},status=status_code)
```

在配置文件中声明自定义的异常处理

```bash
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'my_project.my_app.utils.custom_exception_handler'
}
```

如果未声明，会采用默认的方式，如下

rest_frame/settings.py

```bash
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'rest_framework.views.exception_handler'
}
```

## 4.2 案例

补充上处理关于数据库的异常

```python
from rest_framework.views import exception_handler
from rest_framework.response import Response
from rest_framework.views import exception_handler as drf_exception_handler
from rest_framework import status
from django.db import DatabaseError
def exception_handler(exc, context):
    response = drf_exception_handler(exc, context)
    if response is None:
        view = context['view']
        print('[%s]: %s' % (view, exc))
        if isinstance(exc, DatabaseError):
            response = Response({'detail': '服务器内部错误'}, status=status.HTTP_507_INSUFFICIENT_STORAGE)
        else:
            response = Response({'detail': '未知错误'}, status=status.HTTP_500_INTERNAL_SERVER_ERROR)
    return response
  
# 在setting.py中配置
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'app01.ser.exception_handler'
}
```

## 4.3 REST framework定义的异常

- APIException 所有异常的父类

- ParseError 解析错误

- AuthenticationFailed 认证失败

- NotAuthenticated 尚未认证

- PermissionDenied 权限决绝

- NotFound 未找到

- MethodNotAllowed 请求方式不支持

- NotAcceptable 要获取的数据格式不支持

- Throttled 超过限流次数

- ValidationError 校验失败

也就是说，很多的没有在上面列出来的异常，就需要我们在自定义异常中自己处理了。