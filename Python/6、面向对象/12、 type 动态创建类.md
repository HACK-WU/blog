# 1、示例

```python
age=10
def __init__(self,name):
    self.name=name
People=type("Pepole",(object,),{"age":age,"__init__":__init__})
p=People("张三")
print(p.age)
print(p.name)
###
>>
10
张三
```