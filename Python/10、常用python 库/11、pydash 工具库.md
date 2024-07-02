# ***介绍***

**pydash**

**pydash**

1. 集合操作：**pydash** 提供了许多操作集合数据（如列表和字典）的函数，如过滤、映射、排序、去重等。

1. 函数式编程：**pydash** 支持函数式编程范式，可以使用函数来转换和处理数据，从而减少副作用和改进代码的可测试性。

1. 链式操作：你可以使用链式语法来依次应用多个操作，这可以使代码更紧凑且易于理解。

1. 管道操作：类似于函数式编程中的管道操作，可以将一个操作的结果传递给另一个操作，形成数据处理流程。

1. 条件操作：**pydash** 提供了一些条件判断函数，用于处理逻辑判断。

1. 深度嵌套对象的处理：**pydash** 可以帮助你轻松地处理多层嵌套的对象，避免手动的深度访问。

以下是 

```python
get    	        #获取嵌套数据结构（如字典）中指定键路径的值。
set_	        #通过指定键路径设置嵌套数据结构的值。
has	        #检查嵌套数据结构中是否存在指定键路径。	
map_            #对可迭代对象的每个元素都应用一个函数，并返回结果组成的新列表。
filter_         #对可迭代对象进行过滤，并返回符合条件的元素组成的新列表。
reduce_         #通过将两个元素的结果应用于下一个元素，递归地缩减可迭代对象为单个值。
sort_by
uniq            #从列表中删除重复的元素，并返回新列表。
chunk           #将列表或字符串按指定的大小分割为多个块。
flatten         #将嵌套的可迭代对象（如列表）平铺为单层列表。
omit            #从字典中排除指定的键并返回一个新的字典。
pick            #从字典中选择指定的键并返回一个新的字典。
deep_get
deep_set
```

需要注意的是，虽然 

# get  链式获取字典的值

```python
#示例1：
from pydash import get
  dic={
    "dic":{
        "dic":{
            "name":"张三"
        }
    }
}
print(get(dic,"dic.dic.name"))
>> 张三
#示例2：
import pydash as _
  
# get() 方法的示例
data = {'name': {'first': 'John', 'last': 'Doe'}}
first_name = _.get(data, 'name.first')
print(first_name)  # 输出 'John'
nonexistent_property = _.get(data, 'age', default='N/A')
print(nonexistent_property)  # 输出 'N/A'
```

# set_ 链式设置字典的值

```python
import pydash as _
# set_() 方法的示例
person = {'name': 'John', 'age': 32, 'address': {'city': 'New York', 'zip': '10001'}}
_.set_(person, 'age', 33)
_.set_(person, 'address.zip', '10002')
_.set_(person, 'phone', '555-1234')
print(person) 
# 输出 {'name': 'John', 'age': 33, 'address': {'city': 'New York', 'zip': '10002'}, 'phone': '555-1234'}
```

# has 链式判断字典是否有某个key

```python
import pydash as _
# has() 方法的示例
data = {'name': {'first': 'John', 'last': 'Doe'}}
has_last_name = _.has(data, 'name.last')
print(has_last_name)  # 输出 True
has_age = _.has(data, 'age')
print(has_age)  # 输出 False
```

# **map_ 映射**

当提到 

示例代码：

```python
from pydash import map_
# 示例1：将列表中的每个元素平方
numbers = [1, 2, 3, 4, 5]
squared_numbers = map_(numbers, lambda x: x ** 2)
print(squared_numbers)  # 输出: [1, 4, 9, 16, 25]
# 示例2：提取字典中每个值的长度
person = {'name': 'Alice', 'age': '30', 'city': 'Wonderland'}
value_lengths = map_(person, len)
print(value_lengths)  # 输出: [5, 2, 10]
# 示例3：使用对象的属性作为映射函数
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age
students = [Student('Alice', 25), Student('Bob', 22), Student('Charlie', 28)]
student_names = map_(students, 'name')
print(student_names)  # 输出: ['Alice', 'Bob', 'Charlie']
# 示例4： 
age = map_([{'name': 'moe', 'age': 40},
                  {'name': 'larry', 'age': 50},], 'age')
print(age) # [40, 50]
```

在上述示例中，

请注意，这里使用的是 

# **filter_ 过滤**

当提到 

示例代码：

```python
from pydash import filter_
# 示例1：从列表中筛选出所有偶数
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_numbers = filter_(numbers, lambda x: x % 2 == 0)
print(even_numbers)  # 输出: [2, 4, 6, 8, 10]
# 示例2：从字典中筛选出所有年龄大于等于 30 的人
people = [
    {'name': 'Alice', 'age': 25},
    {'name': 'Bob', 'age': 35},
    {'name': 'Charlie', 'age': 28},
    {'name': 'David', 'age': 40}
]
senior_people = filter_(people, lambda person: person['age'] >= 30)
print(senior_people)  # 输出: [{'name': 'Bob', 'age': 35}, {'name': 'David', 'age': 40}]
# 示例3：筛选出对象列表中特定属性的值满足条件的对象
class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price
products = [Product('Apple', 1.0), Product('Banana', 0.5), Product('Orange', 2.0)]
expensive_products = filter_(products,  lambda product: product.price >= 1.0)
print([product.name for product in expensive_products]) # ['Apple', 'Orange']
```

在上述示例中，

请注意，这里使用的是 

# **flattern 展平**

当提到 

示例代码：

```python
from pydash import flatten,flatten_deep
# 示例1：展平嵌套的列表
nested_list1 = [[1, 2, 3], [4, 5], [6, 7, 8]]
flattened_list = flatten(nested_list1)
print(flattened_list)  # 输出: [1, 2, 3, 4, 5, 6, 7, 8]
# 示例2：展平多层嵌套的列表
nested_list2 = [1, 2, [3, [4, 5, [6, 7]]]]
flattened_list21 = flatten(nested_list2) # 只展平一层
flattened_list22 = flatten_deep(nested_list2) # 递归展平多层
print(flattened_list21)  # 输出: [1, 2, 3, [4, 5, [6, 7]]]
print(flatten(flattened_list21))  # 输出: [1, 2, 3, 4, 5, [6, 7]]
print(flattened_list22)  # 输出: [1, 2, 3, 4, 5, 6, 7, 8]
```

在上述示例中，

请注意，这里使用的是 

# **sort_by 排序**

当提到 

示例代码：

```python
from pydash import sort_by
# 示例1：按照数字大小对列表进行排序
numbers = [10, 5, 8, 3, 1]
sorted_numbers = sort_by(numbers)
print(sorted_numbers)  # 输出: [1, 3, 5, 8, 10]
# 示例2：按照字符串长度对字符串列表进行排序
words = ['apple', 'banana', 'cherry', 'date', 'elderberry']
sorted_words_by_length = sort_by(words, len)
print(sorted_words_by_length)  # 输出: ['date', 'apple', 'banana', 'cherry', 'elderberry']
# 示例3：按照字典的某个属性对字典列表进行排序
people = [
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25},
    {'name': 'Charlie', 'age': 35}
]
sorted_people_by_age = sort_by(people, 'age')
print(sorted_people_by_age)
# 输出: [{'name': 'Bob', 'age': 25}, {'name': 'Alice', 'age': 30}, {'name': 'Charlie', 'age': 35}]
```

在上述示例中，

请注意，这里使用的是 

# **uniq 去重**

当提到 

示例代码：

```python
from pydash import uniq
# 示例1：从列表中移除重复的整数
numbers = [1, 2, 2, 3, 4, 4, 5]
unique_numbers = uniq(numbers)
print(unique_numbers)  # 输出: [1, 2, 3, 4, 5]
# 示例2：从列表中移除重复的字符串
words = ['apple', 'banana', 'apple', 'cherry', 'banana']
unique_words = uniq(words)
print(unique_words)  # 输出: ['apple', 'banana', 'cherry']
# 示例3：从列表中移除重复的字典
people = [
    {'name': 'Alice', 'age': 25},
    {'name': 'Bob', 'age': 30},
    {'name': 'Alice', 'age': 25}
]
unique_people = uniq(people)
print(unique_people)
# 输出: [{'name': 'Alice', 'age': 25}, {'name': 'Bob', 'age': 30}]
```

在上述示例中，

请注意，这里使用的是 

# **chunk 分割成块**

当提到 

示例代码：

```python
from pydash import chunk
# 示例1：将列表分割成大小为3的块
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
chunked_numbers = chunk(numbers, 3)
print(chunked_numbers)  # 输出: [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]
# 示例2：将字符串分割成大小为2的块
text = "Hello, world!"
chunked_text = chunk(text, 2)
print(chunked_text)  # 输出: ['He', 'll', 'o,', ' w', 'or', 'ld', '!']
# 示例3：将列表分割成大小为2的块，同时保留不足块大小的元素
data = [1, 2, 3, 4, 5]
chunked_data = chunk(data, 2)
print(chunked_data)  # 输出: [[1, 2], [3, 4], [5]]
```

在上述示例中，

请注意，这里使用的是 

# **omit 删除**

当提到 

示例代码：

```python
from pydash import omit
# 示例1：从字典中删除指定的属性
person = {'name': 'Alice', 'age': 30, 'city': 'Wonderland'}
omitted_person = omit(person, ['age', 'city'])
print(omitted_person)  # 输出: {'name': 'Alice'}
# 示例2：从对象中删除指定的属性
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age
student = Student('Bob', 22)
omitted_student = omit(student, ['age'])
print(omitted_student)  # 输出: {'name': 'Bob'}
```

在上述示例中，

请注意，这里使用的是 

# **pick 摘取**

当提到 

示例代码：

```python
from pydash import pick
# 示例1：从字典中选取指定的属性
person = {'name': 'Alice', 'age': 30, 'city': 'Wonderland'}
picked_attributes = pick(person, ['name', 'age'])
print(picked_attributes)  # 输出: {'name': 'Alice', 'age': 30}
# 示例2：从对象中选取指定的属性
class Student:
    def __init__(self, name, age, major):
        self.name = name
        self.age = age
        self.major = major
student = Student('Bob', 22, 'Computer Science')
picked_attributes = pick(student, ['name', 'major'])
print(picked_attributes)  # 输出: {'name': 'Bob', 'major': 'Computer Science'}
```

在上述示例中，

请注意，这里使用的是 

# **管道操作**

当提到 

示例代码：

```python
from pydash import py_
# 示例1：使用管道操作对列表进行多步处理
numbers = [1, 2, 3, 4, 5]
processed_numbers = py_(numbers) \
    .map_(lambda x: x * 2) \
    .filter_(lambda x: x % 3 == 0) \
    .reverse() \
    .value()
print(processed_numbers)  # 输出: [10, 6]
# 示例2：使用管道操作对字典列表进行处理
people = [
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25},
    {'name': 'Charlie', 'age': 35}
]
processed_people = py_(people) \
    .filter_(lambda person: person['age'] >= 30) \
    .map_(lambda person: person['name']) \
    .value()
print(processed_people)  # 输出: ['Alice', 'Charlie']
```

在上述示例中，使用 

请注意，这里使用的是 