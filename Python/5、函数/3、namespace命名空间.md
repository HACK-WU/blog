# **1、namespace 命名空间**

- 命名空间指的是变量存储的位置，每一个变量都需要存储到指定的命名空间中

- 每一个作用域都会有一个对应的命名空间

- 全局命名空间，用来保存全局变量。函数命名空间用来保存函数中的变量

- 命名空间实际上就是一个字典，是一个专门用来存储变量的字典

# **2、locals() 获取当前作用域的命名空间**

- 如果在全局作用域中调用locals(),则获取全局命名空间

- 如果在函数作用域中调用locals(),则获取函数命名空间

- 返回的是一个字典

```javascript
scope=locals()
print(scope)
```

# **3、global()获取全局的命名空间**

```javascript
scope=global()
print(scope)
```

在局部作用域想要对全局作用域的全局变量进行修改时，需要用到 global(限于字符串，数字)。

```javascript
def func():
  global a
  a = 3
func()
print(a)
count = 1
def search():
  global count
  count = 2
search()
print(count)
```

对可变数据类型（list，dict，set）可以直接引用不用通过global

```javascript
li = [1,2,3]
dic = {'name':'aaron'}
def change():
  li.append(4)
  dic['age'] = 18
  print(dic)
  print(li)
change()
print(dic)
print(li)
```

# **4、nonlocal**

1. 不能修改全局变量。

2. 在局部作用域中，对父级作用域（或者更外层作用域非全局作用域）的变量进行引用和修改，并且引用的哪层，从那层及以下此变量全部发生改变

```javascript
def add_b():
  b = 1
  def do_global():
      b = 10
    print(b)
    def dd_nolocal():
      nonlocal b  # 应用了上一层的变量b
      b = b + 20
      print(b)   # 发生了改变
    dd_nolocal()   # 调用函数，导致do_global的命名空间b也改变了
    print(b)
  do_global()
  print(b)
add_b()   # 最上面一层没有变化
```