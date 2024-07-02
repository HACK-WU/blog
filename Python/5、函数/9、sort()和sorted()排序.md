# 一、sort与sorted区别

1.sort() 函数只适用于

2.sort() 函数排序

3、sorted() 函数

- 例一：

```
a = [3, 6, 7, 8, 5]
b = sorted(a)
print(a)
print(b)
```

结果如下：

```
[3, 6, 7, 8, 5]
[3, 5, 6, 7, 8]
```

- 例二：

```
a = [3, 6, 7, 8, 5]
a.sort()
print(a)
```

结果如下：

```
[3, 5, 6, 7, 8]
```

3.在使用列表进行排序时，需要考虑是否需要保存原列表，如果无需保存原列表，则优先使用sort() 节省内存空间，提高效率

# 二、sorted 语法：

- sorted(iterable, key=None, reverse=False)

```java
参数说明：
iterable    – 可迭代对象。
key         – 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
reverse     – 排序规则，reverse = True 降序 ， reverse = False 升序（默认）。
```

- 练习题：

```auto
假设我们用一组tuple表示学生名字和成绩：
L = [('Bob', 75), ('Adam', 92), ('Bart', 66), ('Lisa', 88)]
用 sorted() 对上述列表:

(1)按名字排序：
(2)按成绩从高到低排序：
运行结果：
>>> print('sorted_by_name:', sorted(L, key=lambda x: x[0]))
sorted_by_name: [('Adam', 92), ('Bart', 66), ('Bob', 75), ('Lisa', 88)]

>>>print('sorted_by_score:', sorted(L, key=lambda x: x[1], reverse=True))
sorted_by_score: [('Adam', 92), ('Lisa', 88), ('Bob', 75), ('Bart', 66)]
```

# 三、sort() 语法

- list.sort(cmp=None, key=None, reverse=False)

```auto
参数说明：
cmp     – 可选参数, 如果指定了该参数会使用该参数的方法进行排序。
key     – 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
reverse – 排序规则，reverse = True 降序， reverse = False 升序（默认）。
```

- 实例1：

```auto
# 列表
vowels = ['e', 'a', 'u', 'o', 'i']
 
# 降序
vowels.sort(reverse=True)
 
# 输出结果
print('降序输出:')
print( vowels )
```

以上实例输出结果如下：

```auto
降序输出:
['u', 'o', 'i', 'e', 'a']
```