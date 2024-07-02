```
from django.utils.deprecation import MiddlewareMixin
class LoginCheckMiddleware(MiddlewareMixin):
    def process_request(self,request):
        url=[
            "/login/",            #如果访问这个路径，就不需要检测是否登录
        ]
        if not request.user.is_authenticated and (request.path_info not in url):
            return redirect("/login/")
            
```