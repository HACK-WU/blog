## 一、概述

jq

GitHub 地址：[https://github.com/stedolan/jq](https://github.com/stedolan/jq)

jq 官方网站： [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)

![](images/WEBRESOURCE231eab9d1c92b7cdcc9306cbaad321a4stickPicture.png)

## 二、jq 命令安装

```bash
yum install epel-release
yum install -y jq
```

## 三、jq 命令语法与示例详解

jq

### 1）基本用法

```bash
jq [options] [filter] [file]
```

- options：	可选参数，用于指定 jq 的选项。

- filter：	必需参数，用于指定 JSON 数据的查询和转换操作。

- file：	可选参数，要处理的 JSON 数据文件。

### 2）常用选项

- -r		输出原始格式，而不是 JSON 编码。

- -c		输出时将结果按行分隔。

- -s		将输入视为多个 JSON 对象，用于处理多个 JSON 对象的数组。

### 3）查询和过滤

- .				表示当前对象，用于访问字段或属性。

- .fieldName		选择指定字段的值。

- .[ ]				用于遍历数组元素。

- select(condition)	根据条件选择元素。

- map(transform)	数组中的每个元素应用转换操作。

当使用 jq 进行查询和过滤 JSON 数据时，你可以根据需要执行各种操作。以下是一些常见的查询和过滤示例，假设我们有以下 JSON 数据：

```json
[
  {
    "name": "Alice",
    "age": 25,
    "city": "New York"
  },
  {
    "name": "Bob",
    "age": 30,
    "city": "Los Angeles"
  },
  {
    "name": "Charlie",
    "age": 22,
    "city": "Chicago"
  }
]
```

#### 1、选择字段

查询并选择所有人的姓名：

```bash
cat data.json | jq '.[].name'
#cat data.json | jq '.[].name,age'  
# 逗号分隔，可以同时获取到多个字段的值
```

输出：

```bash
"Alice"
"Bob"
"Charlie"
```

#### 同时获取到多个字段的值

```shell
cat json_data  | jq   ".[]| {name: .name,age: .age}"
```

输出：

```json
{
  "name": "Alice",
  "age": 25
}
{
  "name": "Bob",
  "age": 30
}
{
  "name": "Charlie",
  "age": 22
}
```

#### 2、过滤

选择年龄大于 25 岁的人的姓名和城市：

```bash
cat data.json | jq '.[] | select(.age > 25) | .name, .city'
```

输出：

```bash
"Bob"
"Los Angeles"
```

#### 3、遍历数组

遍历并输出所有人的年龄：

```bash
cat data.json | jq '.[] | .age'
```

输出：

```bash
25
30
22
```

#### 4、组合操作

选择年龄在 25 到 30 岁之间的人的姓名和城市，并按照姓名排序：

```bash
cat data.json | jq '.[] | select(.age >= 25 and .age <= 30) | .name, .city' | sort
```

```bash
"Alice"
"New York"
"Bob"
"Los Angeles"
```

这些只是一些基本的查询和过滤示例。jq 支持更多的操作和功能，你可以根据需要进行组合和定制。请根据你的数据和需求来调整和扩展这些示例。

### 4）修改和创建

- .fieldName = value			 修改字段的值。

- del(.fieldName)			删除指定字段。

- .newField = value			创建新的字段。

使用 jq 进行 JSON 数据的修改和创建可以帮助你更新数据或添加新的字段。以下是一些示例，假设我们有以下 JSON 数据：

```bash
{
  "name": "Alice",
  "age": 25,
  "city": "New York"
}
```

#### 1、修改字段值：

修改年龄字段的值为 26：

```bash
cat data.json | jq '.age = 26'
```

输出：

```bash
{
  "name": "Alice",
  "age": 26,
  "city": "New York"
}
```

#### 2、创建新字段：

添加一个新的字段 country 并设置其值为 "USA"：

```bash
cat data.json | jq '.country = "USA"'
```

输出：

```bash
{
  "name": "Alice",
  "age": 25,
  "city": "New York",
  "country": "USA"
}
```

#### 3、组合操作：

修改年龄字段的值为 26，并添加一个新的字段 country：

```bash
cat data.json | jq '.age = 26 | .country = "USA"'
```

输出：

```bash
{
  "name": "Alice",
  "age": 26,
  "city": "New York",
  "country": "USA"
}
```

#### 4、条件修改

如果年龄小于 30，则将城市修改为 "Chicago"：

```bash
cat data.json | jq 'if .age < 30 then .city = "Chicago" else . end'
```

输出（由于年龄小于 30，城市被修改）：

```bash
{
  "name": "Alice",
  "age": 25,
  "city": "Chicago"
}
```

这些示例演示了如何使用 

### 5）运算符

- +, -, *, /					数值运算

- ==, !=, <, >, <=, >=			比较运算

- and, or, not				逻辑运算

jq

```bash
{
  "a": 10,
  "b": 5,
  "c": 15
}
```

#### 1、数值运算

进行加法、减法、乘法和除法运算：

```bash
cat data.json | jq '.a + .b'
cat data.json | jq '.a - .b'
cat data.json | jq '.a * .b'
cat data.json | jq '.a / .b'
```

输出：

```bash
15
5
50
2
```

#### 2、比较运算：

比较字段值，返回布尔结果：

```bash
cat data.json | jq '.a > .b'
cat data.json | jq '.a >= .c'
cat data.json | jq '.b < .c'
```

输出：

```bash
true
false
true
```

#### 3、逻辑运算：

执行逻辑 AND、OR 和 NOT 操作：

```bash
cat data.json | jq '.a > 5 and .b < 10'
cat data.json | jq '.a > 15 or .b > 10'
cat data.json | jq 'not (.c > 20)'
```

输出：

```bash
true
true
false
```

#### 4、条件运算：

使用 if 和 then 进行条件运算：

```bash
cat data.json | jq 'if .a > 10 then "Greater" else "Less or equal" end'
```

输出：

```bash
"Less or equal"
```

这些示例展示了 

### 6）变量和条件

- as $variable					将结果保存到变量中。

- if condition then ... else ... end	件语句。

在 

```bash
{
  "name": "Alice",
  "age": 25,
  "city": "New York"
}
```

#### 1、使用变量：

将字段值存储到变量中，并在输出中使用变量：

```bash
cat data.json | jq '.age as $age | "Name: \(.name), Age: \($age)"'
```

输出：

```bash
"Name: Alice, Age: 25"
```

#### 2、条件语句：

使用 if 和 then 进行条件判断：

```bash
cat data.json | jq 'if .age > 18 then "Adult" else "Minor" end'
```

输出：

```bash
"Adult"
```

#### 3、条件判断和变量组合：

结合条件语句和变量，根据年龄输出不同的消息：

```bash
cat data.json | jq 'if .age > 18 then "Name: \(.name), Status: Adult" else "Name: \(.name), Status: Minor" end'
```

输出：

```bash
"Name: Alice, Status: Adult"
```

#### 4、使用条件操作修改数据：

根据年龄字段的值，如果大于 30 则修改城市字段：

```bash
cat data.json | jq 'if .age > 30 then .city = "Chicago" else . end'
```

输出（由于年龄不大于 30，所以城市字段不变）：

```bash
{
  "name": "Alice",
  "age": 25,
  "city": "New York"
}
```

这些示例演示了如何在 

### 7）函数

jq

在 

```bash
{
  "name": "Alice",
  "age": 25,
  "city": "New York",
  "scores": [85, 90, 78, 95]
}
```

#### 1、length 函数：

获取数组的长度：

```bash
cat data.json | jq '.scores | length'
```

输出：

```bash
4
```

#### 2、map 函数：

对数组中的每个元素应用转换操作：

```bash
cat data.json | jq '.scores | map(. * 2)'
```

输出：

```bash
[170,180,156,190]
```

#### 3、keys 和 values 函数：

获取对象的键和值：

```bash
cat data.json | jq '. | keys'
cat data.json | jq '. | values'
```

输出：

```bash
["name","age","city","scores"]
["Alice",25,"New York",[85,90,78,95]]
```

#### 4、to_entries 函数：

将对象转换为键值对数组：

```bash
cat data.json | jq '. | to_entries'
```

输出：

```bash
[
  {"key":"name","value":"Alice"},
  {"key":"age","value":25},
  {"key":"city","value":"New York"},
  {"key":"scores","value":[85,90,78,95]}
]
```

#### 5、自定义函数：

使用自定义函数进行操作：

```bash
cat data.json | jq 'def average: reduce .[] as $item (0; . + $item) / length; .scores | average'
```

输出：

```bash
87
```

这些是一些常见的 jq 命令参数和选项，可以帮助你进行 JSON 数据的查询、过滤、修改和转换。你可以通过查阅 jq 的[官方文档](https://jqlang.github.io/jq/manual/)和教程，深入学习和掌握更多功能和用法。