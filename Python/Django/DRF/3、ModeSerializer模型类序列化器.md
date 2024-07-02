# 1、基本使用

```python
from  rest_framework import  serializers
from api import models


#写一个类，继ListSerializer,重写update
class BookListSerializer(serializers.ListSerializer):
    # def create(self, validated_data):
    #     print(validated_data)
    #     return super().create(validated_data)
    
    def update(self, instance, validated_data):
        print(instance)
        print(validated_data)
        # 保存数据
        # self.child:是BookModelSerializer对象
        # ll=[]
        # for i,si_data in enumerate(validated_data):
        #     ret=self.child.update(instance[i],si_data)
        #     ll.append(ret)
        # return ll
        return [
            # self.child.update(对象，字典) for attrs in validated_data
            self.child.update(instance[i],attrs) for i,attrs in enumerate(validated_data)
        ]
class BookModelSerializer(serializers.ModelSerializer):
    extend_field = serializers.CharField(write_only=True)
    #如果有额外的字段需要处理extend_field，并且需要有需要进行post，需要将write_only写上，
    #否则会在数据库中读取这个字段，但是它读不到，发生报错
    
    class Meta:
        model=Book  # 对应上models.py中的模型
        #自定义序列化类，当需要批量修改数据的时候，需要指定这个。
        #BookListSerializer是自己重写的一个ListSerializer类，就在上面。
        list_serializer_class=BookListSerializer        
        fields='__all__'
        # fields=('name','price','id','author') # 只序列化指定的字段
        #    fieldls中的值，除了可以写model的类属性（字段名称），也可以写被@property修饰过的方法的名称。
        #    比如publish字段是一个外键，对应的id值为1 ，但是在前端的时候，你不想让它显示1 ，而是显示1这个id对应的publish的名称，
        #    这个时候你就可以自定义一个被@property修饰的方法publish_name -->str,然后这个方法返回的就是名称。
        # exclude=('name',) #跟fields不能都写，写谁，就表示排除谁
        # read_only_fields=('price',)
        # write_only_fields=('id',) #弃用了，使用extra_kwargs
        #depth=0 #显示的深度，如果某个属性是外键depth=0 意思就显示一层，也就是显示那个外键的id值。默认是显示外键所对应的其他字段的纪录，
        extra_kwargs = {  # 类似于这种形式name=serializers.CharField(max_length=16,min_length=4)
            'price': {'write_only': True},
        }
        
# 其他使用一模一样
#不需要重写create和updata方法了
```

# 2、序列化外键

在使用 Django REST framework 的 ModelSerializer 序列化一个包含外键（ForeignKey）的模型时，你可以通过几种方式来控制如何序列化关联对象的信息。

在你给出的 CourseSerializer 类中，teacher 字段是一个外键，关联到另一个模型（假设是 Teacher 模型）。如果你想要序列化 teacher 字段，并返回更多关于教师的信息，你可以使用以下几种方法：

1. **基本序列化：**默认情况下，当序列化一个外键字段时，Django REST framework 会返回一个包含外键关联对象ID的简单表示。如果你没有指定任何额外的序列化选项，它将只返回 teacher 字段的ID。

1. **字符串表示：**你可以定义 Teacher 模型的 __str__ 方法，这样序列化时会使用该方法返回的字符串作为外键的值。

1. **嵌套序列化：**如果你想要返回更多关于 teacher 的详细信息，你可以在 CourseSerializer 中嵌套另一个序列化器，专门用于序列化 teacher 字段。

下面是一个如何嵌套序列化 teacher 字段的示例：

```python
from rest_framework import serializers
from .models import Course, Teacher
class TeacherSerializer(serializers.ModelSerializer):
    class Meta:
        model = Teacher
        fields = ('id', 'name', 'bio', 'department')  # 假设Teacher模型有这些字段
class CourseSerializer(serializers.ModelSerializer):
    teacher = TeacherSerializer(read_only=True)  # 使用TeacherSerializer来序列化teacher字段
    class Meta:
        model = Course
        fields = ('id', 'name', 'course_img', 'brief', 'level', 'teacher')
```

在这个示例中，我们首先定义了一个 TeacherSerializer，它指定了要序列化的 Teacher 模型的字段。然后，在 CourseSerializer 中，我们将 teacher 字段显式地设置为 TeacherSerializer 的实例。这样，当 CourseSerializer 被调用时，teacher 字段将按照 TeacherSerializer 定义的方式进行序列化，返回一个包含教师详细信息的字典。

请注意，如果你的 Teacher 模型非常复杂，包含很多字段，你可能需要考虑只序列化其中一部分字段，或者使用更深层次的嵌套序列化策略。此外，如果你不想让 teacher 字段可编辑，可以在外键字段上使用 read_only=True 参数。