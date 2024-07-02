1、什么叫hash: hash是一种算法（不同的hash算法只是复杂度不一样）（3.x里代替了md5模块和sha模块，主要提供 SHA1, SHA224, SHA256, SHA384, SHA512 ，MD5 算法），该算法接受传入的内容，经过运算得到一串hash值

2、hash值的特点是(hash值/产品有三大特性：)：

- 2.1、只要传入的内容一样，得到的hash值必然一样=====>要用明文传输密码文件完整性校验

- 2.2、不能由hash值返解成内容=======》把密码做成hash值，不应该在网络传输明文密码（只能有内容返回hash值）

- 2.3、只要使用的hash算法不变，无论校验的内容有多大，得到的hash值长度是固定的(如从网上下载文件要进行hash校验，保证网络传输没有丢包)

基于2.1和2.3可以做文件下载一致性的校验

基于2.1和2.2可以对用户密码进行加密

hash算法就像一座工厂，工厂接收你送来的原材料（可以用m.update()

为工厂运送原材料），经过加工返回的产品就是hash值

## 二：将指定的 “字符串” 进行加密。使用hashlib的分步解析

**1）在进行md5哈希运算前，需要对数据进行编码，否则报错**

```python
import hashlib
obj = hashlib.md5()   #构造一个hashlib的对象
obj.update("小马过河")  #update对指定字符串进行加密
print(obj)           
--------------结果：
  obj.update("小马过河")
TypeError: Unicode-objects must be encoded before hashing
```

**2）obj是hash对象**

```python
import hashlib
obj = hashlib.md5()
obj.update("小马过河".encode("utf-8"))
print(obj,type(obj))
-------------------结果：
<md5 HASH object @ 0x001C5590> <class '_hashlib.HASH'>
```

**3）**

```python
import hashlib
obj = hashlib.md5()
obj.update("小马过河".encode("utf-8"))
result=obj.hexdigest()
print(result)
--------------结果：
24f67b0f6d02adc8867d612e0e0fc40a
```

**4）给加密增添难度**

```python
import hashlib
obj = hashlib.md5("mcw@xiaoma@aaaafffff".encode("utf-8"))  #添加一些内容，提高加密复杂度。此处的字符串也要先编码，
obj.update("小马过河".encode('utf-8'))
result = obj.hexdigest()
print(result)
-------------------结果：
b11740508f28e04837f2c0e3a58cf990
```

**5）用hashlib做成加密函数（添加基础的字符了的）**

```python
import hashlib
def get_md5(data):   #传参为需要加密的字符串
    obj = hashlib.md5("sidrsicxwersdfsaersdfsdfresdy54436jgfdsjdxff123ad".encode('utf-8'))
    obj.update(data.encode('utf-8'))
   result = obj.hexdigest()　　　　　　　　　　
    return result
val = get_md5('123')
print(val)
--------------结果：
35093270b6352fa9721370b781f7b4d7
```