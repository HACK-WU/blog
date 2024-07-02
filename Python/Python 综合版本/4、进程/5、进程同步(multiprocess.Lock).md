**进程同步(multiprocess.Lock)**

# 锁 —— multiprocess.Lock

当多个进程使用同一份数据资源的时候，就会引发数据安全或顺序混乱问题。

```
import os
import time
import random
from multiprocessing import Process
def work(n):
  print('%s: %s is running' %(n,os.getpid()))
  time.sleep(random.random())
  print('%s:%s is done' %(n,os.getpid()))
if __name__ == '__main__':
  for i in range(3):
    p=Process(target=work,args=(i,))
    p.start()    
```

```
import os
import time
import random
from multiprocessing import Process,Lock
def work(lock,n):
  lock.acquire()
  print('%s: %s is running' % (n, os.getpid()))
  time.sleep(random.random())
  print('%s: %s is done' % (n, os.getpid()))
  lock.release()
if __name__ == '__main__':
  lock=Lock()
  for i in range(3):
    p=Process(target=work,args=(lock,i))
    p.start()
```

上面这种情况虽然使用加锁的形式实现了顺序的执行，但是程序又重新变成串行了，这样确实会浪费了

时间，却保证了数据的安全。

写一个抢票程序

```
#文件db的内容为：{"count":1}
#注意一定要用双引号，不然json无法识别
#并发运行，效率高，但竞争写同一文件，数据写入错乱
from multiprocessing import Process,Lock
import time,json,random
def search():
  dic=json.load(open('db'))
  print('\033[43m剩余票数%s\033[0m' %dic['count'])
def get():
  dic=json.load(open('db'))
  time.sleep(0.1) #模拟读数据的网络延迟
  if dic['count'] >0:
    dic['count']-=1
    time.sleep(0.2) #模拟写数据的网络延迟
    json.dump(dic,open('db','w'))
    print('\033[43m购票成功\033[0m')
def task():
  search()
  get()
if __name__ == '__main__':
  for i in range(100): #模拟并发100个客户端抢票
    p=Process(target=task)
    p.start()
```

- 用锁来保护票

```
#文件db的内容为：{"count":1}
#注意一定要用双引号，不然json无法识别
#并发运行，效率高，但竞争写同一文件，数据写入错乱
from multiprocessing import Process,Lock
import time,json,random
def search():
  dic=json.load(open('db'))
  print('\033[43m剩余票数%s\033[0m' %dic['count'])
def get():
  dic=json.load(open('db'))
  time.sleep(0.1) #模拟读数据的网络延迟
  if dic['count'] >0:
    dic['count']-=1
    time.sleep(0.2) #模拟写数据的网络延迟
    json.dump(dic,open('db','w'))
    print('\033[43m购票成功\033[0m')
  else:
    print('\033[31m购票失败\033[0m')
def task(lock):
  search()
  lock.acquire()
  get()
  lock.release()
if __name__ == '__main__':
  lock = Lock()
  for i in range(50): #模拟并发50个客户端抢票
    p=Process(target=task,args=(lock,))
    p.start()
```

虽然可以用文件共享数据实现进程间通信，但问题是：

1. 效率低（共享数据基于文件，而文件是硬盘上的数据）

1. 需要自己加锁处理

队列和管道都是将数据存放于内存中

队列又是基于（管道+锁）实现的，可以让我们从复杂的锁问题中解脱出来，

我们应该尽量避免使用共享数据，尽可能使用消息传递和队列，避免处理复杂的同步和锁问题，而且在

进程数目增多时，往往可以获得更好的可获展性。