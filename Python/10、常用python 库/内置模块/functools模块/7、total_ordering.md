这是一个类装饰器，用于自动实现类的比较运算。

给定一个类，这个类定义了一个或者多个比较排序方法，这个类装饰器将会补充其余的比较方法，减少了自己定义所有比较方法时的工作量。

被修饰的类必须至少定义  

```python
from functools import total_ordering
 
 
@total_ordering
class Person:
    # 定义相等的比较函数
    def __eq__(self, other):
        return ((self.lastname.lower(), self.firstname.lower()) ==
                (other.lastname.lower(), other.firstname.lower()))
 
    # 定义小于的比较函数
    def __lt__(self, other):
        return ((self.lastname.lower(), self.firstname.lower()) <
                (other.lastname.lower(), other.firstname.lower()))
 
 
p1 = Person()
p2 = Person()
 
p1.lastname = "123"
p1.firstname = "000"
 
p2.lastname = "1231"
p2.firstname = "000"
 
print(p1 < p2)
print(p1 <= p2)  #
print(p1 == p2)
print(p1 > p2)
print(p1 >= p2)  #
 
'''
True
True
False
False
False
'''
```