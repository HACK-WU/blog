原文连接： [python重试库-tenacity 使用指南 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/391812968)

## 一、简介

在与接口的通信过程中，为了防止由于网络不稳定情况，造成请求错误或者超时等问题，或者其他不可控因素等造成功能性问题，我们一般都会加入重试功能以增加代码的健壮性。

Tenacity 是一个 Apache 2.0 许可的通用重试库，用 Python 编写，用于简化向几乎任何事物添加重试行为的任务。

它具有如下特性：

- 通用装饰器 API

- 指定停止条件（即尝试次数限制）

- 指定等待条件（即尝试之间的指数退避睡眠）

- 自定义重试异常

- 自定义对预期返回结果的重试

- 重试协程

- 使用上下文管理器重试代码块

## 二、安装

```python
$ pip install tenacity
```

## 三、使用

**1、基本重试**

```python
from tenacity import retry, wait_fixed, stop_after_attempt, TryAgain
@retry
def never_give_up_never_surrender():
    print("无条件重试，重试之间无间隔，报错之后就立马重试")
    raise Exception
```

**2、停止重试**

设置重试次数：

```python
@retry(stop=stop_after_attempt(7))
def stop_after_7_attempts():
    print("重试7次后停止")
    raise Exception
```

设置时间限制：

```python
@retry(stop=stop_after_delay(10))
def stop_after_10_s():
    print("10秒后停止重试")
    raise Exception
```

组合多个停止条件：

```python
@retry(stop=(stop_after_delay(10) | stop_after_attempt(5)))
def stop_after_10_s_or_5_retries():
    print("10秒后或者5次重试后停止重试")
    raise Exception
```

**3、重试前等待**

重试之间等待固定时间：

```python
@retry(wait=wait_fixed(2))
def wait_2_s():
    print("每次重试前等待2秒")
    raise Exception
```

等待随机时间：

```python
@retry(wait=wait_random(min=1, max=2))
def wait_random_1_to_2_s():
    print("每次重试前等待1-2秒之间")
    raise Exception
```

等待指数时间：

```python
@retry(wait=wait_exponential(multiplier=1, min=4, max=10))
def wait_exponential_1():
    print("每次重试等待 2^x * multiplier 秒，x为重试次数，最小4秒，最多10秒")
    raise Exception
```

结合固定和抖动等待：

```python
@retry(wait=wait_fixed(3) + wait_random(0, 2))
def wait_fixed_jitter():
    print("每次重试前等待3到3+2秒之间")
    raise Exception
```

链式：

```python
@retry(wait=wait_chain(*[wait_fixed(3) for i in range(3)] +
                       [wait_fixed(7) for i in range(2)] +
                       [wait_fixed(9)]))
def wait_fixed_chained():
    print("前3次等待3秒，接下来2次等待7秒，后面等待9秒")
    raise Exception
```

**4、是否重试**

可以通过设置是否重试条件，处理引发特定或一般异常的重试：

```python
@retry(retry=retry_if_exception_type(IOError))
def might_io_error():
    print("IOError错误时重试")
    raise Exception
```

使用函数：

```python
def is_none_p(value):
    """Return True if value is None"""
    return value is None
@retry(retry=retry_if_result(is_none_p))
def might_return_none():
    print("返回 None 则重试")
```

结合几个条件：

```python
def is_none_p(value):
    """Return True if value is None"""
    return value is None
@retry(retry=(retry_if_result(is_none_p) | retry_if_exception_type()))
def might_return_none():
    print("返回 None 则重试")
```

也可以通过引发 TryAgain 异常随时显式重试：

```python
@retry
def do_something():
    result = something_else()
    if result == 23:
       raise TryAgain
```

**5、错误处理**

虽然默认情况下“超时”重试的可调用对象会引发RetryError，但如果需要，我们可以重新引发最后一次尝试的异常：

```python
@retry(reraise=True, stop=stop_after_attempt(3))
def raise_my_exception():
    raise MyException("Fail")
try:
    raise_my_exception()
except MyException:
    # timed out retrying
    pass
```

**6、重试前后和记录**

before重试之前：

```python
import logging
logging.basicConfig(stream=sys.stderr, level=logging.DEBUG)
logger = logging.getLogger(__name__)
@retry(stop=stop_after_attempt(3), before=before_log(logger, logging.DEBUG))
def raise_my_exception():
    raise MyException("Fail")
```

after重试之后：

```python
import logging
logging.basicConfig(stream=sys.stderr, level=logging.DEBUG)
logger = logging.getLogger(__name__)
@retry(stop=stop_after_attempt(3), after=after_log(logger, logging.DEBUG))
def raise_my_exception():
    raise MyException("Fail")
```

也可以只记录将要重试的失败。通常在等待间隔后重试，因此调用关键字参数

```python
import logging
logging.basicConfig(stream=sys.stderr, level=logging.DEBUG)
logger = logging.getLogger(__name__)
@retry(stop=stop_after_attempt(3),
       before_sleep=before_sleep_log(logger, logging.DEBUG))
def raise_my_exception():
    raise MyException("Fail")
```

**7、统计数据**

使用附加到函数的重试属性及其统计属性来访问有关对函数进行的重试的统计信息：

```python
@retry(stop=stop_after_attempt(3))
def raise_my_exception():
    raise MyException("Fail")
try:
    raise_my_exception()
except Exception:
    pass
print(raise_my_exception.retry.statistics)
```

**8、自定义回调**

您还可以定义自己的回调。回调应该接受一个被调用的参数

```python
def return_last_value(retry_state):
    """return the result of the last call attempt"""
    return retry_state.outcome.result()
def is_false(value):
    """Return True if value is False"""
    return value is False
# will return False after trying 3 times to get a different result
@retry(stop=stop_after_attempt(3),
       retry_error_callback=return_last_value,
       retry=retry_if_result(is_false))
def eventually_return_false():
    return False
```

retry_state

- start_time(float) 重试开始时间戳

- retry_object(BaseRetrying) 重试对象

- fn(callable) 此重试调用包装的函数

- args(tuple) 此重试调用包装的函数参数

- kwargs(dict) 此重试调用包装的函数的关键字参数

- attempt_number(int) 当前尝试次数

- outcome(tenacity.FutureorNone) 函数产生的最后结果（结果或异常）

- outcome_timestamp(floatorNone) 最后结果的时间戳

- idle_for(float) 重试等待时间

- next_action(tenacity.RetryActionorNone) 由重试管理器决定的下一步操作

还可以使用 my_stop、my_wait、my_retry、my_before、my_after、my_before_sleep自定义回调。

**9、运行时更改参数**

通过使用附加到包装函数的 retry_with 函数调用时，您可以根据需要更改重试装饰器的参数：

```python
@retry(stop=stop_after_attempt(3))
def raise_my_exception():
    raise MyException("Fail")
try:
    raise_my_exception.retry_with(stop=stop_after_attempt(4))()
except Exception:
    pass
print(raise_my_exception.retry.statistics)
```

**10、重试代码块**

结合 for 循环和上下文管理器，重试代码块，而无需将其包装在隔离的函数中：

```python
from tenacity import Retrying, RetryError, stop_after_attempt
try:
    for attempt in Retrying(stop=stop_after_attempt(3)):
        with attempt:
            raise Exception('My code is failing!')
except RetryError:
    pass
```

如果是异步代码，您可以使用 AsyncRetrying：

```python
from tenacity import AsyncRetrying, RetryError, stop_after_attempt
async def function():
   try:
       async for attempt in AsyncRetrying(stop=stop_after_attempt(3)):
           with attempt:
               raise Exception('My code is failing!')
   except RetryError:
       pass
```

**11、异步并重试**

retry

```python
@retry
async def my_async_function(loop):
    await loop.getaddrinfo('8.8.8.8', 53)
```

```python
@retry
@tornado.gen.coroutine
def my_async_function(http_client, url):
    yield http_client.fetch(url)
```

您还可以通过传递特定的 sleep 函数来使用替代事件循环，例如curio或Trio：

```python
@retry(sleep=trio.sleep)
async def my_async_function(loop):
    await asks.get('
```