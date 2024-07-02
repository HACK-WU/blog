# 代码示例

```python
# coding=utf-8
# @Author:wyp
# @FileName:test1.py
# @DateTime:2023/9/25 16:13
from enum import Enum
TreeType = Enum('TreeType', 'apple_tree cherry_tree peach_tree')
class Tree:
    pool = {}
    def __new__(cls, age, x, y, tree_type):
        print("call __new__")
        obj = cls.pool.get(tree_type, None)
        if not obj:
            obj = super().__new__(cls)
            cls.pool[tree_type] = obj
            obj.tree_type = tree_type
        return obj
    def __init__(self, age, x, y, *args):
        print("call __init__")
        self.age = age
        self.x = x
        self.y = y
    def render(self):
        print(f'render a tree of type {self.tree_type} and age {self.age} at ({self.x},{self.y})')
if __name__ == '__main__':
    # main()
    t1 = Tree(10, 11, 12, TreeType.apple_tree)
    print(id(t1))
    print(t1.__dict__)
    t12 = Tree(0, 1, 2, TreeType.apple_tree)
    print(id(t1))
    print(t1.__dict__)
    print()
    print(id(t1))
    print(t1.__dict__)
    print("#" * 70)
    print()
    t2 = Tree(20, 21, 22, TreeType.cherry_tree)
    print(id(t2))
    print(t2.__dict__)
    t2 = Tree(201, 212, 223, TreeType.cherry_tree)
    print(id(t2))
    print(t2.__dict__)
    print()
    t3 = Tree(30, 31, 32, TreeType.peach_tree)
    print(id(t3))
    print(t3.__dict__)
    t3 = Tree(301, 312, 323, TreeType.peach_tree)
    print(id(t3))
    print(t3.__dict__)
    print()
    print()
    print(t1.tree_type)
    print(t2.tree_type)
    print(t3.tree_type)
    print(Tree.pool)
#
>>
call __new__
call __init__
2163696016912
{'tree_type': <TreeType.apple_tree: 1>, 'age': 10, 'x': 11, 'y': 12}
call __new__
call __init__
2163696016912
{'tree_type': <TreeType.apple_tree: 1>, 'age': 0, 'x': 1, 'y': 2}

2163696016912
{'tree_type': <TreeType.apple_tree: 1>, 'age': 0, 'x': 1, 'y': 2}
######################################################################

call __new__
call __init__
2163697323816
{'tree_type': <TreeType.cherry_tree: 2>, 'age': 20, 'x': 21, 'y': 22}
call __new__
call __init__
2163697323816
{'tree_type': <TreeType.cherry_tree: 2>, 'age': 201, 'x': 212, 'y': 223}

call __new__
call __init__
2163697323704
{'tree_type': <TreeType.peach_tree: 3>, 'age': 30, 'x': 31, 'y': 32}
call __new__
call __init__
2163697323704
{'tree_type': <TreeType.peach_tree: 3>, 'age': 301, 'x': 312, 'y': 323}


TreeType.apple_tree
TreeType.cherry_tree
TreeType.peach_tree
{<TreeType.apple_tree: 1>: <__main__.Tree object at 0x000001F7C6551E10>, <TreeType.cherry_tree: 2>: <__main__.Tree object at 0x000001F7C6690F28>, <TreeType.peach_tree: 3>: <__main__.Tree object at 0x000001F7C6690EB8>}
```