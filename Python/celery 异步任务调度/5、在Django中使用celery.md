项目根目录创建celery包，目录结构如下：[](javascript:void(0);)

```python
mycelery/
├── config.py
├── __init__.py
├── main.py
└── sms/
    ├── __init__.py
    ├── tasks.py        #这个不能变，就叫做tasks,里面定义了具体的异步任务函数
```

[](javascript:void(0);)

配置文件config.py:

```python
broker_url = 'redis://127.0.0.1:6379/15'
result_backend = 'redis://127.0.0.1:6379/14'
```

任务文件tasks.py：[](javascript:void(0);)

```python
# celery的任务必须写在tasks.py的文件中，别的文件名称不识别!!!
from mycelerys.main import app
import time
import logging
log = logging.getLogger("django")
@app.task  # name表示设置任务的名称，如果不填写，则默认使用函数名做为任务名
def send_sms(mobile):
    """发送短信"""
    print("向手机号%s发送短信成功!"%mobile)
    time.sleep(5)
    return "send_sms OK"
@app.task  # name表示设置任务的名称，如果不填写，则默认使用函数名做为任务名
def send_sms2(mobile):
    print("向手机号%s发送短信成功!" % mobile)
    time.sleep(5)
    return "send_sms2 OK"
```

[](javascript:void(0);)

最后在main.py主程序中对django的配置文件进行加载[](javascript:void(0);)

```python
# 主程序
import os
from celery import Celery
import django
# 创建celery实例对象
app = Celery("sms")
# 把celery和django进行组合，识别和加载django的配置文件
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'celeryPros.settings.dev')
django.setup()
# 通过app对象加载配置
app.config_from_object("mycelerys.config")
# 加载任务
# 参数必须必须是一个列表，里面的每一个任务都是任务的路径名称
# app.autodiscover_tasks(["任务1","任务2"])
app.autodiscover_tasks(["mycelerys.sms",])
# 启动Celery的命令
# 强烈建议切换目录到mycelery根目录下启动
# celery -A mycelery.main worker --loglevel=info
```

[](javascript:void(0);)

Django视图调用：[](javascript:void(0);)

```python
from django.shortcuts import render
# Create your views here.
from django.shortcuts import render,HttpResponse
from mycelerys.sms.tasks import send_sms,send_sms2
from datetime import timedelta
from datetime import datetime
def test(request):
    ################################# 异步任务
    # 1. 声明一个和celery一模一样的任务函数，但是我们可以导包来解决
    # send_sms.delay("110")
    # send_sms2.delay("119")
    # send_sms.delay() 如果调用的任务函数没有参数，则不需要填写任何内容
    ################################# 定时任务
    # ctime = datetime.now()
    # # 默认用utc时间
    # utc_ctime = datetime.utcfromtimestamp(ctime.timestamp())
    # time_delay = timedelta(seconds=10)
    # task_time = utc_ctime + time_delay
    # result = send_sms.apply_async(["911", ], eta=task_time)
    # print(result.id)
    return HttpResponse('ok')
```

[](javascript:void(0);)

 