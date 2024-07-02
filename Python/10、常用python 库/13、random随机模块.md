原文连接： [Python随机函数random使用详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/98298060)

## 1、random.random

random.random()用于生成一个0到1的随机符点数: 0 <= n < 1.0

```python
#!/usr/bin/python
import random
# 生成第一个随机数
print ("random 1 : ", random.random())
# 生成第二个随机数
print ("random 2 : ", random.random())
```

结果：

```text
random 1 :  0.3558774735558118
random 2 :  0.46006891154492147
```

## 2、random.uniform

random.uniform的函数原型为：random.uniform(a, b)，用于生成一个指定范围内的随机符点数，两个参数其中一个是上限，一个是下限。如果a > b，则生成的随机数n: b <= n <= a。如果 a <b， 则 a <= n <= b。

```python
import random
print (random.uniform(1, 10))  
print (random.uniform(10, 1))
```

结果：

```text
2.1520386126536115
3.1391272747538731
```

## 3、random.randint

random.randint()的函数原型为：random.randint(a, b)，用于生成一个指定范围内的整数。其中参数a是下限，参数b是上限，生成的随机数n: a <= n <= b,

注意： 下限必须小于上限

```python
import random
print (random.randint(11, 20))  #生成的随机数n: 11 <= n <= 20  
print (random.randint(20, 20))  #结果永远是20
```

结果：

```text
11
20
```

## 4、random.randrange

random.randrange的函数原型为：random.randrange([start], stop[, step])，从指定范围内，按指定基数递增的集合中 获取一个随机数。如：random.randrange(10, 100, 2)，结果相当于从[10, 12, 14, 16, ... 96, 98]序列中获取一个随机数。random.randrange(10, 100, 2)在结果上与 random.choice(range(10, 100, 2) 等效。

```python
import random
print (random.randrange(10, 18, 2))
```

结果：

```text
14
```

## 5、random.choice

random.choice从序列中获取一个随机元素。其函数原型为：random.choice(sequence)。参数sequence表示一个有序类型。这里要说明 一下：sequence在python不是一种特定的类型，而是泛指一系列的类型。list, tuple, 字符串都属于sequence。有关sequence可以查看python手册数据模型这一章

```python
import random
print (random.choice("Pythontab.com"))
print (random.choice(["python", "tab", "com"]))
print (random.choice(("python", "tab", "com")))
```

结果：

```text
t
python
tab
```

## 6、random.shuffle

　　random.shuffle的函数原型为：random.shuffle(x[, random])，用于将一个列表中的元素打乱。如:

```python
import random
list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
random.shuffle(list)
print (list)
```

结果:

```text
[4, 1, 9, 3, 2, 7, 10, 6, 8, 5]
```

## 7、random.sample

　　random.sample的函数原型为：random.sample(sequence, k)，从指定序列中随机获取指定长度的片断。sample函数不会修改原有序列。

```python
import random
list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]  
slice = random.sample(list, 5)  #从list中随机获取5个元素，作为一个片断返回  
print (slice) 
print (list) #原有序列不会改变。
```

结果：

```text
[8, 2, 6, 7, 9]
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```