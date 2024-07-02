# 1、介绍：

redis-py是python中连接redis数据库的API接口。

# 2、安装

```
pip install redis
```

# 3、基本使用

- 在redis-py接口中，将redis中原本的命令，都转变成了一个方法，且方法名和命令名称一样。

- 默认返回的所有的字符串响应都是以字节的形式，想要返回为字符串，需要指定decode_response=True

- Redis(decode_resposne=True)

- 如果进行批量操作，参数可以用一个字典代替

```
>>> import redis
>>> r = redis.Redis(host='localhost', port=6379, db=0,decode_response=True)    #db=0，表示连接0号库
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
'bar'
```

注意：

- 连接redis时，redis比如处于启动状态。

- 并且protected-mod 保护模式为no.

- bind="127.0.0.1" ，需要在配置文件中注释掉。

- 

## 3.1数据库操作

```
import redis
con=redis.Redis(host="192.168.23.49",port=6379,db=0)
res=con.dbsize()        #查看数据库中一共有多少个key，对应的也就是多少个数据
res=con.flushdb()       #清空当前数据库，这是一个风险操作，慎用,
res=con.flushall()      #清空所有数据库，也就是0-15的数据库全部清空，属于高风险操作，慎用。
```

## 3.2 key操作：

```
import redis
con=redis.Redis(host="192.168.23.49",port=6379,db=0,decode_responses=True)
res=con.keys()              #查看当前数据库中所有的Key,返回一个list
res=con.exists("key11")     #判断某个key是否存在,return True/False
res=con.delete("key1","key2")     #删除多个键值，并返回删除成功的个数return num
res=con.expire("key3","100")       #给某个key设定过期时间，过期的key会被删除,return True/False
res=con.ttl("key3")             #查看某个key还有多久过期,如果返回-2 ，证明已过期,return num
print(res)
```

## 3.3字符串操作

```
'''
    def set(self, name, value, ex=None, px=None, nx=False, xx=False):
        -ex:    #设定超时时间,单位秒
        -px:    #设定超时时间单位毫秒
        -nx:    #True,表示只能添加，不能覆盖：
        -xx:    #True,表示只能覆盖不能添加
'''
#######################################获取key-value,或者相关信息：
'''
     def get(self, name):                   #获取某个key值  
     def getrange(self, key, start, end):   #key值中对应起始位置，的内容，（顾头又顾尾）
     def strlen(self, name):                #获取某个key值的长度
'''
#######################################修改key-value:
'''
      def append(self, key, value):         #将value的添加到原value的末尾，相当于拼接。
      def setrange(self, name, offset, value):  #在对应的offset的位置，加入新的值，相当于插入操作
      def incr(self, name, amount=1):       将key值+1，只有key值是数字时，才能执行
'''
```

# 4、mset、msetnx、zadd

- 当使用redis进行批量添加key-value时，使用字典作为参数传入。

```
dic={
    "k1":"v1",
    "k2":"v2",
    "k3":"v3"
}
res=con.mset(dic)    #返回一个boolen,True,表示操作成功，False表示操作失败
>> True
```

# 5、mget

- 当需要批量获取key-value时，使用列表作为参数传入

```
li=["k1","k2","k3"]
res=con.mget(li)    #返回一个列表
print(res)
>> ['v1', 'v2', 'v3']
```