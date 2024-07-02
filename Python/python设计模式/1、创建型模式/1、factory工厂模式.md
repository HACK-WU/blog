# 示例

```
#coding=utf-8
#动物的基类
class Animal:
    def speak(self):
        raise  NotImplementedError
class Cat(Animal):
    def speak(self):
        print "我是Cat"
class Dog(Animal):
    def speak(self):
        print "我是Dog"
#工厂类对象
class AnimalFactory:
    def __init__(self):
        self.animals={
            "dog":Dog,
            "cat":Cat
        }
    def getAnimal(self,animal_type):
        if animal_type in self.animals:
            return self.animals.get(animal_type)()
        else:
            raise Exception("没有这个对象，目前支持的对象只有这些：%s" %self.animals.keys())
if __name__ == '__main__':
    #非工厂模式创建对象
    # cat=Cat()
    # dog=Dog()
    # cat.speak()
    # dog.speak()
    
    #工厂模式创建对象
    animal_factory=AnimalFactory()
    #直接返回实例化好的对象
    cat=animal_factory.getAnimal("cat")
    dog=animal_factory.getAnimal("dog")
    cat.speak()
    dog.speak()
```