**请求和响应**

# 1、request 请求

```python
# 请求对象
# from rest_framework.request import Request
    def __init__(self, request, parsers=None, authenticators=None,                 negotiator=None, parser_context=None):
        # 二次封装request，将原生request作为drf request对象的 _request 属性
        self._request = request
    def __getattr__（self，item）：
     return getattr(self._request,item)
# 请求对象.data:		前端以三种编码方式传入的数据，都可以取出来
# 请求对象.query_params: 与Django标准的request.GET相同，只是更换了更正确的名称而已。
        
```

# 2、Response 响应

```python
#from rest_framework.response import Response
 def __init__(self, data=None, status=None,                 
 template_name=None, headers=None,                 
 exception=False, content_type=None):
        
#data：你要返回的数据，字典
#status：返回的状态码，默认是200，
from rest_framework import status   #在这个路径下，它把所有使用到的状态码都定义成了常量
#template_name 渲染的模板名字（自定制模板），不需要了解
#headers:响应头，可以往响应头放东西，就是一个字典
#content_type：响应的编码格式，application/json和text/html;
# 浏览器响应成浏览器的格式，postman响应成json格式，通过配置实现的（默认配置）
#不管是postman还是浏览器，都返回json格式数据
# drf有默认的配置文件---》先从项目的setting中找，找不到，采用默认的
# drf的配置信息，先从自己类中找--》项目的setting中找---》默认的找
 -局部使用:对某个视图类有效
        -在视图类中写如下
        from rest_framework.renderers import JSONRenderer
        renderer_classes=[JSONRenderer,]
    -全局使用：全局的视图类，所有请求，都有效
     -在setting.py中加入如下
        REST_FRAMEWORK = {
            'DEFAULT_RENDERER_CLASSES': (  # 默认响应渲染类
                'rest_framework.renderers.JSONRenderer',  # json渲染器
                'rest_framework.renderers.BrowsableAPIRenderer',  # 浏览API渲染器
            )
        }
                                
                                                                                                                                                                                                                                
#status 状态码
from  rest_framework import  status
HTTP_100_CONTINUE = 100
HTTP_101_SWITCHING_PROTOCOLS = 101
HTTP_200_OK = 200
HTTP_201_CREATED = 201
HTTP_202_ACCEPTED = 202
HTTP_203_NON_AUTHORITATIVE_INFORMATION = 203
HTTP_204_NO_CONTENT = 204
HTTP_205_RESET_CONTENT = 205
HTTP_206_PARTIAL_CONTENT = 206
HTTP_207_MULTI_STATUS = 207
HTTP_208_ALREADY_REPORTED = 208
HTTP_226_IM_USED = 226
HTTP_300_MULTIPLE_CHOICES = 300
HTTP_301_MOVED_PERMANENTLY = 301
HTTP_302_FOUND = 302
HTTP_303_SEE_OTHER = 303
HTTP_304_NOT_MODIFIED = 304
HTTP_305_USE_PROXY = 305
HTTP_306_RESERVED = 306
HTTP_307_TEMPORARY_REDIRECT = 307
HTTP_308_PERMANENT_REDIRECT = 308
HTTP_400_BAD_REQUEST = 400
HTTP_401_UNAUTHORIZED = 401
HTTP_402_PAYMENT_REQUIRED = 402
HTTP_403_FORBIDDEN = 403
HTTP_404_NOT_FOUND = 404
HTTP_405_METHOD_NOT_ALLOWED = 405
HTTP_406_NOT_ACCEPTABLE = 406
HTTP_407_PROXY_AUTHENTICATION_REQUIRED = 407
HTTP_408_REQUEST_TIMEOUT = 408
HTTP_409_CONFLICT = 409
HTTP_410_GONE = 410
HTTP_411_LENGTH_REQUIRED = 411
HTTP_412_PRECONDITION_FAILED = 412
HTTP_413_REQUEST_ENTITY_TOO_LARGE = 413
HTTP_414_REQUEST_URI_TOO_LONG = 414
HTTP_415_UNSUPPORTED_MEDIA_TYPE = 415
HTTP_416_REQUESTED_RANGE_NOT_SATISFIABLE = 416
HTTP_417_EXPECTATION_FAILED = 417
HTTP_418_IM_A_TEAPOT = 418
HTTP_422_UNPROCESSABLE_ENTITY = 422
HTTP_423_LOCKED = 423
HTTP_424_FAILED_DEPENDENCY = 424
HTTP_426_UPGRADE_REQUIRED = 426
HTTP_428_PRECONDITION_REQUIRED = 428
HTTP_429_TOO_MANY_REQUESTS = 429
HTTP_431_REQUEST_HEADER_FIELDS_TOO_LARGE = 431
HTTP_451_UNAVAILABLE_FOR_LEGAL_REASONS = 451
HTTP_500_INTERNAL_SERVER_ERROR = 500
HTTP_501_NOT_IMPLEMENTED = 501
HTTP_502_BAD_GATEWAY = 502
HTTP_503_SERVICE_UNAVAILABLE = 503
HTTP_504_GATEWAY_TIMEOUT = 504
HTTP_505_HTTP_VERSION_NOT_SUPPORTED = 505
HTTP_506_VARIANT_ALSO_NEGOTIATES = 506
HTTP_507_INSUFFICIENT_STORAGE = 507
HTTP_508_LOOP_DETECTED = 508
HTTP_509_BANDWIDTH_LIMIT_EXCEEDED = 509
HTTP_510_NOT_EXTENDED = 510
HTTP_511_NETWORK_AUTHENTICATION_REQUIRED = 511
```