# 代码示例

```python
class People:
    def __init__(self, name, age):
        self._name = name
        self._age=age
    @property
    def name(self):
        print("获取姓名")
        return self._name
    @name.setter
    def name(self, value):
        print("设置姓名")
        self._name = value
    @name.deleter
    def name(self):
        print("删除属性")
        del self._name
if __name__ == '__main__':
    p = People("张三", 20)
    print(p.name)
    p.name="李四"
    print(p.name)
    del p.name
    print(p.__dict__)
    
#
>>
获取姓名
张三
设置姓名
获取姓名
李四
删除属性
{'_age': 20}
```