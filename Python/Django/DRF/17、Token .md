# 最佳实践

## Views.py

```python
from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework.authtoken.models import Token
from django.contrib.auth.models import User
@api_view(['POST'])
def register(request):
    username = request.data.get('username')
    password = request.data.get('password')
    if username and password:
        try:
            user = User.objects.create_user(username=username, password=password)
            token = Token.objects.create(user=user)
            return Response({'token': token.key})
        except:
            return Response({'error': 'Unable to register.'})
    else:
        return Response({'error': 'Missing credentials.'})
@api_view(['POST'])
def login(request):
    username = request.data.get('username')
    password = request.data.get('password')
    if username and password:
        user = authenticate(username=username, password=password)
        if user:
            token, created = Token.objects.get_or_create(user=user)
            return Response({'token': token.key})
        else:
            return Response({'error': 'Unable to log in with provided credentials.'})
    else:
        return Response({'error': 'Missing credentials.'})
```

## urls.py

```python
from django.urls import path
from .views import register, login
urlpatterns = [
    path('register/', register, name='register'),
    path('login/', login, name='login'),
]
```

# 错误信息：“Unable to log in with provided credentials.”

在使用auth_token模块进行用户身份验证时，有时会遇到一个常见的错误信息：“Unable to log in with provided credentials.” 这就意味着提供的凭据不正确，无法进行用户登录。

有几个可能的原因会导致这个错误：

1. 用户名或密码不正确：首先，请确保提供的用户名和密码是正确的，并且与数据库中的用户凭据匹配。密码是区分大小写的，因此在输入密码时要小心。

1. 用户不存在：如果提供的用户名在数据库中不存在，那么登录将失败。请确保您提供的用户名是正确的，并且已经注册过。

1. 身份验证机制配置错误：有时，身份验证机制的配置可能会出错，导致无法进行用户登录。请检查

settings.py

1. Token已过期：DRF的auth_token模块使用Token进行身份验证，这些Token是有有效期的。如果您的Token已经过期，那么登录将失败并显示错误信息。在这种情况下，您需要为用户生成一个新的Token。

解决这个错误的方法是仔细检查提供的凭证，并确保它们与数据库中的用户凭证匹配。您还可以尝试重新生成一个新的Token，以确保登录过程顺利进行。