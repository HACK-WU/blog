> **new方法用于创建实例对象，init方法用于初始化实例对象。两个方法都是静态方法。**
>
> 在Python中，`__new__`方法和`__init__`方法都是对象创建时调用的特殊方法，但它们的作用不同。
>
> `__new__(cls, *args, **kwargs)`方法在对象创建之前被调用，作用是创建并返回一个新的对象，通常被用于自定义创建对象的过程。`__new__`方法的第一个参数是类自身，剩余的参数是传递给构造函数的参数。`__new__`方法必须返回一个类的实例，否则`__init__`方法将不会被调用。
>
> `__init__(self, *args, **kwargs)`方法在对象创建之后被调用，作用是初始化对象的属性等信息。`__init__`方法的第一个参数是创建的对象本身，剩余的参数也是调用构造函数时传递的参数。在`__init__`方法中可以为对象设置属性，调用其他方法等。
>
> 因此，在Python中，对象的创建过程可以概括为以下几个步骤：
>
> 1.  调用`__new__`方法，返回一个类的实例（如果没有重写`__new__`方法，则使用默认实现）。
> 2.  如果`__new__`方法返回的实例是该类或其子类的实例，则调用`__init__`方法，将该实例作为第一个参数。
> 3.  返回该实例作为结果，该实例即为创建的对象。
>
> 因此，可以看出，`__new__`方法的调用在`__init__`方法之前，用于创建对象；而`__init__`方法的调用则是在`__new__`方法之后，用于初始化对象属性。

# 示例1： 由父类创建实例对象

```python
class MyClass:
    def __new__(cls):
        print("__new__() is called")
        instance = super().__new__(cls)
        return instance

    def __init__(self):
        print("__init__() is called")
        self.name = "MyClass"


obj = MyClass()  # 创建实例，输出 "__new__() is called" 和 "__init__() is called"
print(obj.name)  # 输出 "MyClass"

```

# 示例2： 自定义实例对象(一个字典)

```python
class Fun:
    def __init__(self):
        self["age"] = 20

    def __new__(cls):
        str = {
            "name": "张三"
        }
        cls.__init__(str)
        return str


p = Fun()
print(p["age"])     # 20
print(p)            #  {'name': '张三', 'age': 20}

```

