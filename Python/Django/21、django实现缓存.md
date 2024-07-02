## 1.缓存的简介

在动态网站中,用户所有的请求,服务器都会去数据库中进行相应的增,删,查,改,渲染模板,执行业务逻辑,最后生成用户看到的页面.

当一个网站的用户访问量很大的时候,每一次的的后台操作,都会消耗很多的服务端资源,所以必须使用缓存来减轻后端服务器的压力.

缓存是将一些常用的数据保存内存或者memcache中,在一定的时间内有人来访问这些数据时,则不再去执行数据库及渲染等操作,而是直接从内存或memcache的缓存中去取得数据,然后返回给用户.

## 2.Django提供了6种缓存方式

- 开发调试缓存

- 内存缓存

- 文件缓存

- 数据库缓存

- Memcache缓存(使用python-memcached模块)

- Memcache缓存(使用pylibmc模块)

经常使用的有文件缓存和Mencache缓存

### 2.1 各种缓存方式的配置文件说明

#### 2.1.1 开发调试(此模式为开发调试使用,实际上不执行任何操作)

settings.py文件配置

```bash
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.dummy.DummyCache',     # 缓存后台使用的引擎
        'TIMEOUT': 300,                                               # 缓存超时时间（默认300秒，None表示永不过期，0表示立即过期）
        'OPTIONS':{
            'MAX_ENTRIES': 300,                                       # 最大缓存记录的数量（默认300）
            'CULL_FREQUENCY': 3,                                      # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
        },
    }
}
```

#### 2.1.2 内存缓存(将缓存内容保存至内存区域中)

settings.py文件配置

```bash
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',     # 指定缓存使用的引擎
        'LOCATION': 'unique-snowflake',                                 # 写在内存中的变量的唯一值 
        'TIMEOUT':300,                                                  # 缓存超时时间(默认为300秒,None表示永不过期)
        'OPTIONS':{
            'MAX_ENTRIES': 300,                                         # 最大缓存记录的数量（默认300）
            'CULL_FREQUENCY': 3,                                        # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
        }      
    }
}
```

#### 2.1.3 文件缓存(把缓存数据存储在文件中)

settings.py文件配置

```bash
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',   #指定缓存使用的引擎
        'LOCATION': '/var/tmp/django_cache',                                #指定缓存的路径
        'TIMEOUT':300,                                                      #缓存超时时间(默认为300秒,None表示永不过期)
        'OPTIONS':{
            'MAX_ENTRIES': 300,                                             # 最大缓存记录的数量（默认300）
            'CULL_FREQUENCY': 3,                                            # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
        }
    }           
}
```

#### 2.1.4 数据库缓存(把缓存数据存储在数据库中)

settings.py文件配置

```bash
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.db.DatabaseCache',       # 指定缓存使用的引擎
        'LOCATION': 'cache_table',                                      # 数据库表                
        'OPTIONS':{
            'MAX_ENTRIES': 300,                                         # 最大缓存记录的数量（默认300）
            'CULL_FREQUENCY': 3,                                        # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
        }     
    }           
}
```

注意,创建缓存的数据库表使用的语句:

```
python manage.py createcachetable
```

Memcached是Django原生支持的缓存系统.要使用Memcached,需要下载Memcached的支持库

#### 2.1.5 Memcache缓存(使用python-memcached模块连接memcache)

settings.py文件配置

```bash
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',   # 指定缓存使用的引擎
        'LOCATION': '192.168.10.100:11211',                                 # 指定Memcache缓存服务器的IP地址和端口
        'OPTIONS':{
            'MAX_ENTRIES': 300,                                             # 最大缓存记录的数量（默认300）
            'CULL_FREQUENCY': 3,                                            # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
        }
    }
}
```

LOCATION也可以配置成如下:

```bash
'LOCATION': 'unix:/tmp/memcached.sock',         # 指定局域网内的主机名加socket套接字为Memcache缓存服务器
'LOCATION': [                                   # 指定一台或多台其他主机ip地址加端口为Memcache缓存服务器
    '192.168.10.100:11211',
    '192.168.10.101:11211',
    '192.168.10.102:11211',
]
```

#### 2.1.6 Memcache缓存(使用pylibmc模块连接memcache)

```bash
settings.py文件配置
    CACHES = {
        'default': {
            'BACKEND': 'django.core.cache.backends.memcached.PyLibMCCache',     # 指定缓存使用的引擎
            'LOCATION':'192.168.10.100:11211',                                  # 指定本机的11211端口为Memcache缓存服务器
            'OPTIONS':{
                'MAX_ENTRIES': 300,                                             # 最大缓存记录的数量（默认300）
                'CULL_FREQUENCY': 3,                                            # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
            },     
        }
    }
```

LOCATION也可以配置成如下:

```bash
'LOCATION': '/tmp/memcached.sock',      # 指定某个路径为缓存目录
'LOCATION': [                           # 分布式缓存,在多台服务器上运行Memcached进程,程序会把多台服务器当作一个单独的缓存,而不会在每台服务器上复制缓存值
    '192.168.10.100:11211',
    '192.168.10.101:11211',
    '192.168.10.102:11211',
]
```

Memcached是基于内存的缓存

## 3.Django中的缓存应用

Django提供了不同粒度的缓存,可以缓存某个页面,可以只缓存一个页面的某个部分,甚至可以缓存整个网站.

### 3.1 单独视图缓存

例子,为单个视图函数添加缓存

路由配置:

```python
url(r'^index$',views.index),
```

数据库

![](images/WEBRESOURCEd21540704317243d7155045b9e91b51b截图.png)

views代码:

```python
from app01 import  models
from django.views.decorators.cache import cache_page
import time
@cache_page(15)                                 #超时时间为15秒
def index(request):
    user_list=models.UserInfo.objects.all()     #从数据库中取出所有的用户对象
    ctime=time.time()                           #获取当前时间
    return render(request,"index.html",{"user_list":user_list,"ctime":ctime})
```

index.html代码:

```css
body>
<h1>{{ ctime }}</h1>
<ul>
    {% for user in user_list %}
        <li>{{ user.name }}</li>
    {% endfor %}
</ul>
</body>
```

因为缓存的原因,不停的刷新浏览器时会发现,页面上显示的时间每15秒钟变化一次.

在立即刷新浏览器的时候,立即在数据库中添加一个用户对象,此时继续刷新浏览器,前端页面上不会显示刚才添加的用户

一直刷新浏览器

上面的例子是基于内存的缓存配置,基于文件的缓存该怎么配置呢??

更改settings.py的配置

```bash
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',   # 指定缓存使用的引擎
        'LOCATION': 'E:\django_cache',                                      # 指定缓存的路径
        'TIMEOUT': 300,                                                     # 缓存超时时间(默认为300秒,None表示永不过期)
        'OPTIONS': {
            'MAX_ENTRIES': 300,                                             # 最大缓存记录的数量（默认300）
            'CULL_FREQUENCY': 3,                                            # 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL_FREQUENCY（默认3）
        }
    }
}
```

然后再次刷新浏览器,可以看到在刚才配置的目录下生成的缓存文件

![](images/WEBRESOURCE7b53cdd508e614eabbaf73bd8a094b5d截图.png)

通过实验可以知道,Django会以自己的形式把缓存文件保存在配置文件中指定的目录中.

### 3.2 全站使用缓存

既然是全站缓存,当然要使用Django中的中间件.

用户的请求通过中间件,经过一系列的认证等操作,如果请求的内容在缓存中存在,则使用

当返回给用户之前,判断缓存中是否已经存在,如果不存在,则

修改settings.py配置文件

```python
MIDDLEWARE = [
    'django.middleware.cache.UpdateCacheMiddleware',            #响应HttpResponse中设置几个headers
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'django.middleware.cache.FetchFromCacheMiddleware',         #用来缓存通过GET和HEAD方法获取的状态码为200的响应
]
CACHE__MIDDLEWARE_SECONDS=15                                    # 设定超时时间为15秒
```

views视图函数

```python
from django.shortcuts import render
import time
def index(request):
    ctime = time.time()
    return render(request,'index.html',{'ctime':ctime})
```

其余代码不变,刷新浏览器是

### 3.3 局部视图缓存

例子,刷新页面时,整个网页有一部分实现缓存

views视图函数

```python
from django.shortcuts import render
import time
def index(request):
    # user_list = models.UserInfo.objects.all()
    ctime = time.time()
    return render(request,'index.html',{'ctime':ctime})     
```

前端网页

```xml
{% load cache %}                # 加载缓存
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>{{ ctime }}</h1>
{% cache 15 'aaa' %}            # 设定超时时间为15秒
    <h1>{{ ctime }}</h1>
{% endcache %}
</body>
</html>
```

刷新浏览器可以看到,第一个时间实时变化,后面一个时间每