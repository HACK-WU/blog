# 代码示例:

```python
# coding=utf-8
# @Author:wyp
# @FileName:test1.py
# @DateTime:2023/9/25 16:13
def count_to(count):
    """Counts by word numbers, up to a maximum of five"""
    numbers = ["one", "two", "three", "four", "five"]
    # enumerate() returns a tuple containing a count (from start which
    # defaults to 0) and the values obtained from iterating over sequence
    for pos, number in zip(range(count), numbers):
        yield number
print('Counting to two...')
for number in count_to(2):
    print(number)
print()
print('Counting to five...')
for number in count_to(5):
    print(number)
##
>>
Counting to two...
one
two

Counting to five...
one
two
three
four
five
```