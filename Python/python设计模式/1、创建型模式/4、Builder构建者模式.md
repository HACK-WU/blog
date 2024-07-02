# 1、A示例：构建者模式的简单实现

```python
# coding=utf-8
# @Author:wyp
# @FileName:test2.py
# @DateTime:2023/9/25 17:30
from abc import ABCMeta, abstractmethod
class Computer(metaclass=ABCMeta):
    def __init__(self):
        self.title = None
        self.cpu = None
        self.memory = None
        self.disk = None
    @abstractmethod
    def set_title(self, title):
        pass
    @abstractmethod
    def set_cpu(self, cpu):
        pass
    @abstractmethod
    def set_memory(self, memory):
        pass
    @abstractmethod
    def set_disk(self, disk):
        pass
    @abstractmethod
    def __str__(self):
        pass
class HuaweiComputerBuilder(Computer):
    def set_title(self, title):
        self.title = title
    def set_cpu(self, cpu):
        self.cpu = cpu
    def set_memory(self, memory):
        self.memory = memory
    def set_disk(self, disk):
        self.disk = disk
    def __str__(self):
        return "华为电脑，鸿蒙生态，遥遥领先"
class XiaomiComputerBuilder(Computer):
    def set_title(self, title):
        self.title = title
    def set_cpu(self, cpu):
        self.cpu = cpu
    def set_memory(self, memory):
        self.memory = memory
    def set_disk(self, disk):
        self.disk = disk
    def __str__(self):
        return "小米笔记本，为发烧而生"
# 创建指导者
class Director:
    def __init__(self, builder):
        self.builder = builder
    def construct_computer(self):
        self.builder.set_cpu("Intel i7")
        self.builder.set_memory("16GB")
        self.builder.set_disk("512GB SSD")
        return self.builder
if __name__ == '__main__':
    huawei_builder=HuaweiComputerBuilder()
    huawei=Director(huawei_builder).construct_computer()
    print(huawei)
    print(huawei.__dict__)
    print()
    xiaomi_builder=XiaomiComputerBuilder()
    xiaomi=Director(xiaomi_builder).construct_computer()
    print(xiaomi)
    print(xiaomi.__dict__)
    
##
>>
华为电脑，鸿蒙生态，遥遥领先
{'title': None, 'cpu': 'Intel i7', 'memory': '16GB', 'disk': '512GB SSD'}

小米笔记本，为发烧而生
{'title': None, 'cpu': 'Intel i7', 'memory': '16GB', 'disk': '512GB SSD'}
```

# 2、B示例：构造者模式的高级实现

```python
# coding=utf-8
# @Author:wyp
# @FileName:test3.py
# @DateTime:2023/9/25 17:58
from abc import ABCMeta, abstractmethod
class Computer(metaclass=ABCMeta):
    def __init__(self):
        self.title = None
        self.cpu = None
        self.memory = None
        self.disk = None
    @abstractmethod
    def set_title(self, title):
        pass
    @abstractmethod
    def set_cpu(self, cpu):
        pass
    @abstractmethod
    def set_memory(self, memory):
        pass
    @abstractmethod
    def set_disk(self, disk):
        pass
    @abstractmethod
    def __str__(self):
        pass
class HuaweiComputerBuilder(Computer):
    def set_title(self, title):
        self.title = title
    def set_cpu(self, cpu):
        self.cpu = cpu
    def set_memory(self, memory):
        self.memory = memory
    def set_disk(self, disk):
        self.disk = disk
    def __str__(self):
        return "华为电脑，鸿蒙生态，遥遥领先"
class XiaomiComputerBuilder(Computer):
    def set_title(self, title):
        self.title = title
    def set_cpu(self, cpu):
        self.cpu = cpu
    def set_memory(self, memory):
        self.memory = memory
    def set_disk(self, disk):
        self.disk = disk
    def __str__(self):
        return "小米笔记本，为发烧而生"
class ThinkPadComputerBuilder(Computer):
    def set_title(self,title=None):
        self.title = "thinkpad"
    def set_cpu(self,cpu=None):
        self.cpu = "Inter"
    def set_memory(self, memory=None):
        self.memory = "32G"
    def set_disk(self, disk=None):
        self.disk = "1000G"
    def __str__(self):
        return "ThinkPad,极致的办公体验"
class Director:
    def __init__(self):
        self.builder_call = {
            XiaomiComputerBuilder: self._construct_xiaomi_computer,
            HuaweiComputerBuilder: self._construct_Huawei_computer,
        }
    def construct_computer(self, builder):
        self.builder = builder
        parent_class = type(builder)
        builder_obj=self.builder_call.get(parent_class)
        if builder_obj:
            builder_obj()
        else:
            self._construct_common_computer()
        return self.builder
    def _construct_common_computer(self):
        self.builder.set_title()
        self.builder.set_cpu()
        self.builder.set_disk()
        self.builder.set_memory()
        return self.builder
    def _construct_Huawei_computer(self):
        self.builder.set_title("华为")
        self.builder.set_cpu("Inter")
        self.builder.set_disk("1000G")
        self.builder.set_memory("16G")
        return self.builder
    def _construct_xiaomi_computer(self):
        self.builder.set_title("小米")
        self.builder.set_cpu("AMD")
        self.builder.set_disk("512G")
        self.builder.set_memory("16G")
        return self.builder
if __name__ == '__main__':
    director=Director()
    xiaomi_builder= XiaomiComputerBuilder()
    xiaomi = director.construct_computer(xiaomi_builder)
    print(xiaomi)
    print(xiaomi.__dict__)
    print()
    huawei_builder=HuaweiComputerBuilder()
    huawei=director.construct_computer(huawei_builder)
    print(huawei)
    print(huawei.__dict__)
    print()
    thinkpad_builder=ThinkPadComputerBuilder()
    thinkpad=director.construct_computer(thinkpad_builder)
    print(thinkpad)
    print(thinkpad.__dict__)
    
 ##
 >>
 小米笔记本，为发烧而生
{'title': '小米', 'cpu': 'AMD', 'memory': '16G', 'disk': '512G'}

华为电脑，鸿蒙生态，遥遥领先
{'title': '华为', 'cpu': 'Inter', 'memory': '16G', 'disk': '1000G'}

ThinkPad,极致的办公体验
{'title': 'thinkpad', 'cpu': 'Inter', 'memory': '32G', 'disk': '1000G'}        
```