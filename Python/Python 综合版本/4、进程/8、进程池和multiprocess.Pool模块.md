**进程池和multiprocess.Pool模块**

# 1、进程池

那么在成千上万个任务需要被执行的时候，我们就需要去创建成千上万个进程么？首先，创建进程需要

消耗时间，销毁进程也需要消耗时间。第二即便开启了成千上万的进程，操作系统也不能让他们同时执

行，这样反而会影响程序的效率。因此我们不能无限制的根据任务开启或者结束进程。

定义一个池子，在里面放上固定数量的进程，有需求来了，就拿一个池中的进程来处理任务，等到处理

完毕，进程并不关闭，而是将进程再放回进程池中继续等待任务。如果有很多任务需要执行，池中的进

程数量不够，任务就要等待之前的进程执行任务完毕归来，拿到空闲进程才能继续执行。也就是说，池

中进程的数量是固定的，那么同一时间最多有固定数量的进程在运行。这样不会增加操作系统的调度难

度，还节省了开闭进程的时间，也一定程度上能够实现并发效果。

# 2、multiprocess.Pool模块

## 1)概念介绍

**Pool([numprocess [,initializer [, initargs]]]):创建进程池**

```
numprocess:      要创建的进程数，如果省略，将默认使用cpu_count()的值
initializer：    是每个工作进程启动时要执行的可调用对象，默认为None
initargs：       是要传给initializer的参数组
```

### 方法介绍

```
p.apply(func [, args [, kwargs]]):    #在一个池工作进程中执行func(*args,**kwargs),然后返回结果。
        '''
        需要强调的是：此操作并不会在所有池工作进程中并执行func函数。如果要通过不同参数并发地执行
        func函数，必须从不同线程调用p.apply()函数或者使用p.apply_async()
        '''
p.apply_async(func [, args [, kwargs]]):    #在一个池工作进程中执行func(*args,**kwargs),然后返回结果。
        '''
        此方法的结果是AsyncResult类的实例，callback是可调用对象，接收输入参数。当func的结果变为可
        用时，将理解传递给callback。callback禁止执行任何阻塞操作，否则将接收其他异步操作中的结果。
        '''
p.close():        #关闭进程池，防止进一步操作。如果所有操作持续挂起，它们将在工作进程终止前完成
P.jion():         #等待所有工作进程退出。此方法只能在close（）或teminate()之后调用
```

### 其他方法

```
方法apply_async()和map_async（）的返回值是AsyncResul的实例obj。
实例具有以下方法:
obj.get():                #返回结果，如果有必要则等待结果到达。timeout是可选的。如果在指定时间内还没有到达，
                            将引发一场。如果远程操作中引发了异常，它将在调用此方法时再次被引发。
obj.ready():              #如果调用完成，返回True
obj.successful():         #如果调用完成且没有引发异常，返回True，如果在结果就绪之前调用此方法，引发异常
obj.wait([timeout]):      #等待结果变为可用。
obj.terminate()：         #立即终止所有工作进程，同时不执行任何清理或结束任何挂起工作。如果p被垃圾回收，将自动调用此函数
```

## 2）代码实例

### 进程池和多进程效率对比

```
from multiprocessing import Pool
import time
def func(n):
  for i in range(10):  # 将1到100，每个数打印十次
    print(n +1)
if __name__ == "__main__":
  start = time.time()
  pool = Pool(5)
  pool.map(func, range(100))  # 一百个任务
  t2 = (time.time() - start)
  print(t2)
```

```
from multiprocessing import Process
import time
def func(n):
  for i in range(10):  # 同样将1到100，每个数打印十次
    print(n+1)
if __name__ == "__main__":
  t1 = time.time()
  p_list = []
  for i in range(100):
    p = Process(target=func, args=(i,))
    p_list.append(p)
    p.start()
  for p in p_list:
    p.join()
  t2 = (time.time() - t1)
  print(t2)
```

### 同步和异步

#### 同步调用

```
import os,time
from multiprocessing import Pool
def work(n):
  print('%s run' %os.getpid())
  time.sleep(3)
  return n**2
if __name__ == '__main__':
  #进程池中从无到有创建三个进程,以后一直是这三个进程在执行任务
  p=Pool(3)
  res_l=[]
  for i in range(10):
    # 同步调用，直到本次任务执行完毕拿到res，等待任务work执行的过程中可能有阻塞也可
能没有阻塞
    res=p.apply(work,args=(i,))
                  # 但不管该任务是否存在阻塞，同步调用都会在原地等
着
  print(res_l)
```

#### 异步调用

```
import os
import time
import random
from multiprocessing import Pool
def work(n):
  print('%s run' %os.getpid())
  time.sleep(random.random())
  return n**2
if __name__ == '__main__':
  p=Pool(3) #进程池中从无到有创建三个进程,以后一直是这三个进程在执行任务
  res_l=[]
  for i in range(10):
    res=p.apply_async(work,args=(i,)) # 异步运行，根据进程池中有的进程数，每次
最多3个子进程在异步执行
                     # 返回结果之后，将结果放入列表，归还进
程，之后再执行新的任务
                     # 需要注意的是，进程池中的三个进程不会同
时开启或者同时结束
                     # 而是执行完一个就释放一个进程，这个进程
就去接收新的任务。
    res_l.append(res)
    # 异步apply_async用法：如果使用异步提交的任务，主进程需要使用join，等待进程池内任务
都处理完，然后可以用get收集结果
  # 否则，主进程结束，进程池可能还没来得及执行，也就跟着一起结束了
  p.close()
  p.join()
  for res in res_l:
    print(res.get()) #使用get来获取apply_aync的结果,如果是apply,则没有get方
法,因为apply是同步执行,立刻获取结果,也根本无需get
```

### 进程池聊天

#### 服务端

```
#Pool内的进程数默认是cpu核数，假设为4（查看方法os.cpu_count()）
#开启6个客户端，会发现2个客户端处于等待状态
#在每个进程内查看pid，会发现pid使用为4个，即多个客户端公用4个进程
from socket import *
from multiprocessing import Pool
import os
server=socket(AF_INET,SOCK_STREAM)
server.setsockopt(SOL_SOCKET,SO_REUSEADDR,1)
server.bind(('127.0.0.1',8080))
server.listen(5)
def talk(conn):
  print('进程pid: %s' %os.getpid())
  while True:
    try:
      msg=conn.recv(1024)
      if not msg:break
      conn.send(msg.upper())
    except Exception:
      break
if __name__ == '__main__':
  p=Pool(4)
  while True:
    conn,addr=server.accept()
    p.apply_async(talk,args=(conn,))
    #p.apply(talk,args=(conn,)) # 同步的话，则同一时间只有一个客户端能访问
```

#### 客户端

```
from socket import *
client=socket(AF_INET,SOCK_STREAM)
client.connect(('127.0.0.1',8080))
while True:
  msg=input('>>: ').strip()
  if not msg:continue
  client.send(msg.encode('utf-8'))
  msg=client.recv(1024)
  print(msg.decode('utf-8'))
```

并发开启多个客户端，服务端同一时间只有4个不同的pid，只能结束一个客户端，另外一个客户端才会进来.

### 回调函数

```
需要回调函数的场景：进程池中任何一个任务一旦处理完了，就立即告知主进程：我好了额，你可以处理
我的结果了。主进程则调用一个函数去处理该结果，该函数即回调函数
我们可以把耗时间（阻塞）的任务放到进程池中，然后指定回调函数（主进程负责执行），这样主进程在
执行回调函数时就省去了I/O的过程，直接拿到的是任务的结果。
```

```
from multiprocessing import Pool
import requests
import json
import os
def get_page(url):
  print('<进程%s> get %s' %(os.getpid(),url))
  respone=requests.get(url)
  if respone.status_code == 200:
    return {'url':url,'text':respone.text}
def pasrse_page(res):
  print('<进程%s> parse %s' %(os.getpid(),res['url']))
  parse_res='url:<%s> size:[%s]\n' %(res['url'],len(res['text']))
  with open('db.txt','a') as f:
    f.write(parse_res)
if __name__ == '__main__':
  urls=[
    'https://www.baidu.com',
    'https://www.python.org',
    'https://www.openstack.org',
    'http://www.sina.com.cn/'
 ]
  p=Pool(3)
  res_l=[]
  for url in urls:
       res=p.apply_async(get_page,args=(url,),callback=pasrse_page)
    res_l.append(res)
  p.close()
  p.join()
  print([res.get()['url'] for res in res_l]) #拿到的是get_page的结果,其实完全
没必要拿该结果,该结果已经传给回调函数处理了
```

### 爬虫实例

```
import re
from urllib.request import urlopen
from multiprocessing import Pool
def get_page(url,pattern):
  response=urlopen(url).read().decode('utf-8')
  return pattern,response
def parse_page(info):
  pattern,page_content=info
  res=re.findall(pattern,page_content)
  for item in res:
    dic={
      'index':item[0].strip(),
      'title':item[1].strip(),
      'actor':item[2].strip(),
      'time':item[3].strip(),
   }
    print(dic)
if __name__ == '__main__':
  regex = r'<dd>.*?<.*?class="board-index.*?>(\d+)</i>.*?title="(.*?)".*?
class="movie-item-info".*?<p class="star">(.*?)</p>.*?<p
class="releasetime">(.*?)</p>'
  pattern1=re.compile(regex,re.S)
  url_dic={
    'http://maoyan.com/board/7':pattern1,
 }
  p=Pool()
  res_l=[]
  for url,pattern in url_dic.items():
    res=p.apply_async(get_page,args=(url,pattern),callback=parse_page)
    res_l.append(res)
  for i in res_l:
    i.get()
```

如果在主进程中等待进程池中所有任务都执行完毕后，再统一处理结果，则无需回调函数