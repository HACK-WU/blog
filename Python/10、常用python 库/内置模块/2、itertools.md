# 1、count  无限迭代器

- count(start=0,step=1)是一个无限迭代器，默认从0开始，步长为1，然后返回无限的数值序列

- 当然也可以自定义起始值和步长

```python
from itertools import count
counter = count()
print(next(counter))
print(next(counter))
print(next(counter))
print()
counter = count(10, 0.5)
print(next(counter))
print(next(counter))
print(next(counter))
#
0
1
2
10
10.5
11.0
```

# 2、cycle 循环

- terable 参数是一个可迭代对象，比如列表、元组或任何实现了迭代器协议的对象。

cycle() 函数会返回一个迭代器，当你遍历这个迭代器时，它会无限次地循环遍历 iterable 中的元素。

下面是一个使用 itertools.cycle() 的示例：

```python
import itertools
# 创建一个迭代器，它将无限重复遍历 [1, 2, 3]
cycler = itertools.cycle([1, 2, 3])
# 打印前 10 个元素
for _ in range(10):
    print(next(cycler))
```

输出将会是：

```
1
2
3
1
2
3
1
2
3
1
```

可以看到，序列 [1, 2, 3] 被无限重复。

# 3、islice 切片

itertools.islice() 函数是 Python 的 itertools 模块中的一个函数，它用于从一个迭代器中生成一个切片，也就是取出迭代器中的一部分元素，类似于列表的切片操作。

函数的基本用法如下：

```python
islice(iterable, start, stop[, step])
```

- iterable 是一个可迭代对象。

- start 是切片的起始索引（包含该索引对应的元素），默认值为 0。

- stop 是切片的结束索引（不包含该索引对应的元素），默认值是迭代器的长度。

- step 是步长，指定相邻元素之间的间隔，默认值为 1。

islice() 函数返回一个迭代器，它的行为类似于一个序列的切片，但不会一次性计算整个序列，而是在迭代时按需生成元素，这使得它非常适合处理无限迭代器。

下面是一个使用 itertools.islice() 的示例：

```python
import itertools
# 创建一个无限计数迭代器
counter = itertools.count()
# 使用 islice() 取出前 5 个元素
print(list(itertools.islice(counter, 5)))
# 输出: [0, 1, 2, 3, 4]
# 可以指定 start 参数来设置起始索引
print(list(itertools.islice(counter, 10, 15)))
# 输出: [10, 11, 12, 13, 14]
# 可以指定 step 参数来设置步长
print(list(itertools.islice(counter, 20, 30, 2)))
# 输出: [20, 22, 24, 26, 28]
```

islice() 函数的一个常见用途是当需要从一个可能产生无限序列的迭代器中取出有限数量的元素时。

# 4、repeat  重复

itertools.repeat() 函数是 Python 标准库 itertools 模块中的一个函数，它用于生成一个无限序列，该序列无限重复同一个元素。

**注意repeat()只是重复返回对象的原始值，并没有做拷贝。**

函数的基本用法如下：

```python
repeat(object, [times])
```

- object 是需要重复的对象，可以是任何值。

- times 是一个可选参数，用来指定重复的次数。如果不提供 times 或者提供 None，则 repeat() 会无限重复 object。

返回的迭代器可以无限地产生相同的对象，直到程序终止或者你明确地停止迭代。

下面是一个使用 itertools.repeat() 的示例：

```python
import itertools
# 创建一个无限重复元素 'A' 的迭代器
repetition = itertools.repeat('A')
# 取出前 5 个重复的元素
for _ in range(5):
    print(next(repetition))  # 输出: A, A, A, A, A
# 如果需要有限次数的重复，可以指定 times 参数
limited_repetition = itertools.repeat('B', 3)
# 取出有限次数重复的元素
for _ in range(3):
    print(next(limited_repetition))  # 输出: B, B, B
```

由于 itertools.repeat() 可能产生无限序列，通常需要配合使用 itertools.islice() 或者在循环中设置明确的退出条件，以避免程序长时间运行而无法停止。

# 5、accumulate  累加器

itertools.accumulate() 函数是 Python 标准库 itertools 模块中的一个函数，它用于计算可迭代对象中元素的累积总和（或累积结果），通常用于计算序列的累积和。

函数的基本用法如下：

```python
accumulate(iterable[, func])
```

- iterable 是一个可迭代对象，如列表、元组等。

- func 是一个可选的二元函数，用于计算累积结果，默认为加法运算（即 operator.add），也可以指定其他二元运算，如乘法。

accumulate() 函数返回一个迭代器，它按顺序产生输入元素的累积结果。如果没有指定 func，则累积结果是通过加法计算的。

下面是一个使用 itertools.accumulate() 的示例：

```python
import itertools
# 计算一个序列的累积和
data = [1, 2, 3, 4, 5]
result = list(itertools.accumulate(data))
print(result)  # 输出: [1, 3, 6, 10, 15]
# 计算累积乘积
data = [1, 2, 3, 4, 5]
result = list(itertools.accumulate(data, func=lambda x, y: x * y))
print(result)  # 输出: [1, 2, 6, 24, 120]
```

在这个例子中，第一次调用 accumulate() 时，使用了默认的加法函数，计算了序列 [1, 2, 3, 4, 5] 的累积和。第二次调用时，通过 func 参数指定了 lambda 函数来计算累积乘积。

accumulate() 函数的一个常见用途是在需要计算序列的累积和或其他累积运算时，它提供了一种高效且内存友好的方式来完成这样的操作，特别是当处理大型数据集或无限序列时。

# 6、chain() 连接

itertools.chain() 函数是 Python 标准库 itertools 模块中的一个函数，它用于将多个可迭代对象串联（concatenate）起来，形成一个连续的迭代流。这个函数非常适合用于合并多个序列，而不需要显式地进行列表拼接或者循环迭代。

函数的基本用法如下：

```python
chain(*iterables)
```

- iterables 是一个或多个可迭代对象，如列表、元组、字符串等。

chain() 函数接受一系列的可迭代对象作为参数，然后按顺序迭代这些对象中的元素，就像它们是单个序列的一部分一样。

下面是一个使用 itertools.chain() 的示例：

```python
import itertools
# 串联多个列表
list = [1, 2, 3]
tup = ("三", "四", "五")
dic = {
    'a': 1,
    'b': 2,
    'c': 3
}
# 使用 chain() 串联多个列表
chained = itertools.chain(list, tup, dic)
# 打印串联后的元素
for element in chained:
    print(element)
#
1
2
3
三
四
五
a
b
c
```

# 7、compress 过滤

itertools.compress() 函数是 Python 标准库 itertools 模块中的一个函数，它使用一个布尔序列来选择另一个可迭代对象中的元素。

函数的基本用法如下：

```python
compress(data, selectors)
```

- data 是一个可迭代对象，包含要被筛选的元素。

- selectors 是一个可迭代的布尔序列（通常由 itertools 中的函数如 filterfalse、takewhile、dropwhile 产生，或者是任何产生布尔值的序列），用于决定 data 中的元素是否应该被包含在结果中。

对于 data 中的每个元素，compress() 会检查 selectors 中对应的元素。如果 selectors 中的元素是 True，则 data 中的相应元素将被包括在迭代器的结果中；如果 selectors 中的元素是 False，则 data 中的相应元素将被跳过。

下面是一个使用 itertools.compress() 的示例：

```python
import itertools
# 定义数据序列
data = [1, 2, 3, 4, 5, 6, 7, 8, 9]
# 定义选择器序列，这里我们使用 itertools.cycle() 来创建一个交替的 True/False 序列
selectors = itertools.cycle([True, False])
# 使用 compress() 筛选数据
compressed_data = list(itertools.compress(data, selectors))
print(compressed_data)  # 输出: [1, 3, 5, 7, 9]
```

在这个例子中，compress() 函数使用 cycle([True, False]) 生成的选择器序列来交替选择 data 中的元素。结果是一个只包含 data 中奇数位置元素的列表。

compress() 函数的一个常见用途是在你有一个数据集和一个相应的布尔掩码（或选择器序列）时，用于快速选择符合条件的数据子集。这种方法特别适用于需要根据特定条件过滤数据的情况，同时保持数据的迭代顺序。

# 8、dropwhile  跳过元素，直到False

itertools.dropwhile() 函数是 Python 标准库 itertools 模块中的一个函数，它用于从一个迭代器中跳过（drop）元素，直到某个条件不再满足为止。

函数的基本用法如下：

```python
dropwhile(predicate, iterable)
```

- predicate 是一个函数，它接受一个参数并返回一个布尔值 True 或 False。

- iterable 是一个可迭代对象。

dropwhile() 函数会返回一个新的迭代器。在开始时，这个迭代器会从 iterable 中跳过（不产生）那些 predicate 函数返回 True 的元素。当 predicate 函数首次返回 False 时，dropwhile() 会停止跳过元素，并开始产生（返回）之后的 iterable 中的所有元素。

下面是一个使用 itertools.dropwhile() 的示例：

```python
import itertools
# 定义一个序列
data = [4, 5, 6, 7, 8, 9, 1, 2, 3, ]
# 4  跳过，但是从5以后，后面的数据都不会跳过
# 使用 dropwhile() 跳过序列中直到条件不满足的所有元素
# 这里我们跳过小于 5 的数
dropped = itertools.dropwhile(lambda x: x < 5, data)
# 打印迭代器中的元素
for element in dropped:
    print(element)
# 输出:
5
6
7
8
9
1
2
3
```

# 9、takewhile 获取元素，直到False

itertools.takewhile() 函数是 Python 标准库 itertools 模块中的一个函数，它用于从一个迭代器中取出元素，直到某个条件不再满足为止。

函数的基本用法如下：

```python
takewhile(predicate, iterable)
```

- predicate 是一个函数，它接受一个参数并返回一个布尔值 True 或 False。

- iterable 是一个可迭代对象。

takewhile() 函数会返回一个迭代器，它产生 iterable 中满足 predicate 函数返回 True 的所有元素。当 predicate 函数首次返回 False 时，takewhile() 会停止产生元素，并且不会产生剩余的元素，即使剩余的元素满足条件。

下面是一个使用 itertools.takewhile() 的示例：

```python
import itertools
# 定义一个序列
data = [1, 2, 3, 4, 5, 6, 7, 8, 9]
# 使用 takewhile() 取出序列中直到条件不满足的所有元素
# 这里我们取出所有小于 5 的数
taken = itertools.takewhile(lambda x: x < 5, data)
# 打印迭代器中的元素
for element in taken:
    print(element)
# 输出:
# 1
# 2
# 3
# 4
```

在这个例子中，takewhile() 会一直从 data 中取出元素，直到遇到第一个不满足 x < 5 条件的元素（即元素 5），然后停止产生元素。

takewhile() 函数的一个常见用途是在处理数据流时提取满足特定条件的前导元素，或者在数据流的某个特定点之前进行操作。这在数据预处理和条件监测中非常有用。

# 10、groupby 分组

itertools.groupby() 函数是 Python 标准库 itertools 模块中的一个函数，它用于根据元素的特征将可迭代对象中的元素分组。

函数的基本用法如下：

```python
groupby(iterable, key=None)
```

- iterable 是一个可迭代对象。

- key 是一个可选的函数，用于从可迭代对象的每个元素中提取一个用于比较的值。如果不提供 key 函数，则直接比较元素本身。

groupby() 函数会返回一个迭代器，它按顺序产生一个 (k, group) 的元组，其中 k 是分组的键（由 key 函数返回的值或元素本身），而 group 是一个迭代器，包含了所有与 k 相等的元素。

需要注意的是，groupby() 只有在迭代器中的元素是有序的情况下才有意义，因为它依赖于元素的顺序来识别不同组的边界。如果迭代器中的元素顺序是随机的，分组将不会按预期工作。

下面是一个使用 itertools.groupby() 的示例：

```python
 from itertools import *
    a = ['aa', 'ab', 'abc', 'bcd', 'abcde']
    for i, k in groupby(a, len):
        print i, list(k)
        
#输出为：
2 ['aa', 'ab']
3 ['abc', 'bcd']
5 ['abcde']
```

# 11、starmap  解构元素，并计算

starmap() 函数是 Python 标准库 itertools 模块中的一个函数，它允许你使用一个可迭代的参数序列来调用一个函数，并将这些参数解包并传递给这个函数。

函数的基本用法如下：

```python
starmap(function, iterable)
```

- function 是要使用解包的参数调用的函数。

- iterable 是一个可迭代对象，它产生包含参数的子可迭代对象，这些参数将被解包并传递给 function。

starmap() 会返回一个迭代器，它产生 function(*args) 的结果，其中 args 是从 iterable 中解包得到的参数。

这个函数的名称来源于 "start *arg" 的概念，其中 *arg 是一个可迭代对象，它被用来解包参数并传递给函数。

下面是一个使用 itertools.starmap() 的示例：

```python
import itertools
# 定义一个简单的函数，用于计算两个数的和
def add(x, y):
    return x + y
# 创建一个可迭代对象，包含多个参数元组
pairs = [(1, 2), (3, 4), (5, 6)]
# 使用 starmap() 将 add() 函数应用于 pairs 中的每个元组
results = list(itertools.starmap(add, pairs))
print(results)  # 输出: [3, 7, 11]
```

在这个例子中，starmap() 将 add() 函数应用于 pairs 中的每个元组，实际上是对每个元组执行 add(*pair)，即解包每个元组并传递给 add() 函数。

starmap() 函数的一个常见用途是当你有一个函数，你想用不同的参数多次调用它，而这些参数是以可迭代对象的形式给出的。这可以减少代码的冗余，并使参数传递更加灵活和清晰。

# 12、tee 迭代器，深拷贝

itertools.tee() 函数是 Python 标准库 itertools 模块中的一个函数，它用于将一个可迭代对象分割成多个迭代器，这些迭代器可以独立地进行迭代，而不会相互干扰。

函数的基本用法如下：

```python
tee(iterable, n=2)
```

- iterable 是一个可迭代对象。

- n 是一个整数，表示要生成的分割迭代器的数量，默认值为 2。

tee() 函数会返回 n 个迭代器，它们都引用同一个 iterable。每个迭代器都可以被独立地使用，它们的迭代进度互不影响。需要注意的是，由于 tee() 需要缓冲输入迭代器以支持多个输出，它可能会消耗更多的内存，特别是当 iterable 是无限的或者非常大时。

此外，tee() 还提供了一个静态方法 itertools.tee(data, n=2)，允许你将一个可迭代对象分割成多个独立的迭代器。

下面是一个使用 itertools.tee() 的示例：

```python
import itertools
# 创建一个可迭代对象
data = (1, 2, 3, 4, 5)
# 使用 tee() 将 data 分割成两个迭代器
it1, it2 = itertools.tee(data)
# 独立地使用两个迭代器
print(next(it1))  # 输出: 1
print(next(it2))  # 输出: 1
print(next(it1))  # 输出: 2
print(next(it2))  # 输出: 2
```

# 13、zip_longest zip 最长版

itertools.zip_longest() 是 Python 标准库 itertools 模块中的一个函数，它用于将多个可迭代对象中元素配对，形成元组，直到最长的可迭代对象被耗尽。

函数的基本用法如下：

```python
zip_longest(*iterables, fillvalue=None)
```

- *iterables 是一个或多个可迭代对象。

- fillvalue 是一个可选参数，它指定了当较短的可迭代对象耗尽后，较长的可迭代对象中元素的配对值。默认值为 None。

zip_longest() 函数会返回一个迭代器，它产生元组，其中包含来自每个可迭代对象的元素。如果可迭代对象的长度不同，fillvalue 将被用来填充较短的可迭代对象中缺失的位置。

下面是一个使用 itertools.zip_longest() 的示例：

```python
复制import itertools
# 定义几个不同长度的列表
list1 = [1, 2, 3]
list2 = ['a', 'b', 'c', 'd']
list3 = [4, 5]
# 使用 zip_longest() 将列表中的元素配对
zipped = list(itertools.zip_longest(list1, list2, list3))
print(zipped)
# 输出:  [(1, 'a', 4), (2, 'b', 5), (3, 'c', None), (None, 'd', None)]
```

# 14、permutations 排列组合

itertools.permutations() 函数是 Python 标准库 itertools 模块中的一个函数，它用于生成可迭代对象中元素的所有可能排列。

函数的基本用法如下：

```python
permutations(iterable, r=None)
```

- iterable 是一个可迭代对象，包含将被排列的元素。

- r 是一个可选参数，指定每个排列的长度。如果省略或为 None，排列的长度默认为 iterable 中元素的数量。

permutations() 函数返回一个迭代器，它产生一个由 tuple 组成的序列，每个 tuple 代表输入序列的一个排列。

下面是一个使用 itertools.permutations() 的示例：

```python
import itertools
# 定义一个列表
items = ['a', 'b', 'c']
# 生成所有可能的排列
perms = list(itertools.permutations(items))
# 打印每个排列
for perm in perms:
    print(perm)
# 输出:
# ('a', 'b', 'c')
# ('a', 'c', 'b')
# ('b', 'a', 'c')
# ('b', 'c', 'a')
# ('c', 'a', 'b')
# ('c', 'b', 'a')
```

在这个例子中，permutations() 函数生成了列表 items 中元素的所有排列。

如果指定了 r 参数，那么只生成长度为 r 的排列：

```python
# 生成长度为 2 的所有排列
perms_r = list(itertools.permutations(items, 2))
# 打印每个排列
for perm in perms_r:
    print(perm)
# 输出:
# ('a', 'b')
# ('a', 'c')
# ('b', 'a')
# ('b', 'c')
# ('c', 'a')
# ('c', 'b')
```

permutations() 函数的一个常见用途是在需要探索所有可能的元素组合时，比如在解决排列组合问题、测试不同的参数组合或生成密码时。由于排列的数量随元素数量的增加而迅速增加，当处理较大的集合时，这个函数可能会产生大量的输出。

# 15、combinations 排列组合，无重复内容

itertools.combinations() 函数是 Python 标准库 itertools 模块中的一个函数，它用于生成可迭代对象中元素的所有可能组合（combinations），而不考虑元素的顺序。

函数的基本用法如下：

```python
combinations(iterable, r)
```

- iterable 是一个可迭代对象，包含将被组合的元素。

- r 是一个整数，指定每个组合的长度。

combinations() 函数返回一个迭代器，它产生一个由 tuple 组成的序列，每个 tuple 代表输入序列的一个组合。

下面是一个使用 itertools.combinations() 的示例：

```python
import itertools
# 定义一个列表
items = ['a', 'b', 'c']
# 生成长度为 2 的所有可能组合
combos = list(itertools.combinations(items, 2))
# 打印每个组合
for combo in combos:
    print(combo)
# 输出:
# ('a', 'b')
# ('a', 'c')
# ('b', 'c')
```

在这个例子中，combinations() 函数生成了列表 items 中元素的所有长度为 2 的组合。

combinations() 与 permutations() 的一个关键区别是，combinations() 不考虑元素顺序，只有元素的选择重要。因此，对于上面的示例，组合 ('a', 'b') 和 ('b', 'a') 被认为是相同的，并且只计算一次。

combinations() 函数的一个常见用途是在需要探索所有可能的元素子集时，比如在组合数学问题、统计分析、化学中的分子组合或是在处理具有固定大小的元素组时。由于组合的数量也随元素数量的增加而迅速增加，当处理较大的集合时，这个函数也可能会产生大量的输出。

# 16、combinations_with_replacement  排列组合 重复内容

itertools.combinations_with_replacement() 函数是 Python 标准库 itertools 模块中的一个函数，它用于生成可迭代对象中元素的所有可能组合，同时允许元素重复选择（即有放回地选择元素）。

函数的基本用法如下：

```python
combinations_with_replacement(iterable, r)
```

- iterable 是一个可迭代对象，包含将被组合的元素。

- r 是一个整数，指定每个组合的长度。

combinations_with_replacement() 函数返回一个迭代器，它产生一个由 tuple 组成的序列，每个 tuple 代表输入序列的一个允许重复选择的组合。

下面是一个使用 itertools.combinations_with_replacement() 的示例：

```python
import itertools
# 定义一个列表
items = ['a', 'b', 'c']
# 生成长度为 2 的所有可能的带放回组合
combos_with_replacement = list(itertools.combinations_with_replacement(items, 2))
# 打印每个组合
for combo in combos_with_replacement:
    print(combo)
# 输出:
# ('a', 'a')
# ('a', 'b')
# ('a', 'c')
# ('b', 'b')
# ('b', 'c')
# ('c', 'c')
```

# 17、product 笛卡尔积 

itertools.product() 函数是 Python 标准库 itertools 模块中的一个函数，它用于生成可迭代对象的笛卡尔积（Cartesian product）。简单来说，笛卡尔积是指给定多个序列时，它们所有可能组合的集合。

函数的基本用法如下：

```python
product(*iterables, repeat=1)
```

- *iterables 是一个或多个可迭代对象，它们的元素将被用于生成笛卡尔积。

- repeat 是一个可选参数，表示生成笛卡尔积的次数。默认值为 1。

product() 函数返回一个迭代器，它产生一个由元组（tuple）组成的序列，每个元组都包含来自每个 iterable 的一个元素。

下面是一个使用 itertools.product() 的示例：

```python
import itertools
# 定义几个可迭代对象
iterable1 = ['a', 'b']
iterable2 = [1, 2]
# 生成两个可迭代对象的笛卡尔积
product_result = list(itertools.product(iterable1, iterable2))
# 打印每个组合
for item in product_result:
    print(item)
# 输出:
# ('a', 1)
# ('a', 2)
# ('b', 1)
# ('b', 2)
```

在这个例子中，product() 函数生成了两个列表 iterable1 和 iterable2 的笛卡尔积。

repeat 参数允许你指定生成笛卡尔积的次数。例如，如果我们想要生成两个列表的二次笛卡尔积，我们可以这样写：

```python
复制# 生成两个可迭代对象的二次笛卡尔积
product_result_repeat = list(itertools.product(iterable1, iterable2, repeat=2))
# 打印每个组合
for item in product_result_repeat:
    print(item)
# 输出:
# ('a', 1, 'a', 1)
# ('a', 1, 'a', 2)
# ('a', 1, 'b', 1)
# ('a', 1, 'b', 2)
# ...
# ('b', 2, 'b', 2)
```

product() 函数的一个常见用途是在需要探索多个选项的所有组合方式时，比如在参数测试、数据建模或是模拟不同配置的系统行为时。通过使用 repeat 参数，可以生成更高维度的笛卡尔积，从而探索更复杂的组合空间。

# 18、pairwise 两两组合

itertools.pairwise() 是 Python 中 itertools 模块提供的一个函数，用于生成可迭代对象中相邻元素的所有可能的两两组合。

具体来说，pairwise() 函数接受一个可迭代对象作为参数，并返回一个生成器，该生成器依次产生相邻元素的所有可能的两两组合。如果可迭代对象中的元素数量少于两个，那么不会生成任何组合。

下面是一个简单的示例，演示了如何使用 pairwise() 函数生成相邻元素的两两组合：

```python
import itertools
# 可迭代对象
data = [1, 2, 3, 4, 5]
# 使用 pairwise 生成相邻元素的两两组合
pairs = itertools.pairwise(data)
# 打印结果
for pair in pairs:
    print(pair)
```

在这个示例中，如果我们打印 pairs，将会得到如下输出：

```python
(1, 2)
(2, 3)
(3, 4)
(4, 5)
```

这里，pairwise() 函数生成了相邻元素的所有可能的两两组合。