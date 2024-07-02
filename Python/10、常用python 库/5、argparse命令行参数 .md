argparse 是 Python 标准库中用于解析命令行参数的模块。它提供了一种方便的方法来处理命令行参数，使得编写命令行工具变得更加简单。

使用 argparse 模块可以定义程序所需的命令行参数，并自动生成帮助信息和错误消息。

以下是一个简单的示例代码，演示了如何使用 argparse 模块解析命令行参数：

```python
import argparse
# 创建 ArgumentParser 对象
parser = argparse.ArgumentParser(description='这是一个命令行工具的描述')
# 添加命令行参数
parser.add_argument('input', help='输入文件路径')
parser.add_argument('-o', '--output', help='输出文件路径')
# 解析命令行参数
args = parser.parse_args()
# 访问解析后的参数值
print('输入文件路径:', args.input)
print('输出文件路径:', args.output)
```

在上述代码中，我们首先创建了一个 ArgumentParser 对象 parser，并指定了一个描述信息。然后，通过调用 add_argument() 方法向 parser 对象添加命令行参数。第一个参数是参数的名称，第二个参数是帮助信息。最后，调用 parse_args() 方法解析命令行参数，并将解析后的参数存储在 args 对象中。可以通过访问 args 对象来获取具体的参数值。

运行以上示例代码时，可以在命令行中传入参数来测试。例如：

```
python script.py input.txt -o output.txt
```

上述命令行中的 input.txt 和 output.txt 分别是传给 input 和 output 参数的值，通过访问 args.input 和 args.output 可以获取到这些值，并在代码中进行相应的处理。

使用 argparse 模块可以更加方便地处理命令行参数，包括校验参数、提供默认值、生成帮助信息等。更多高级用法可以参考官方文档：[https://docs.python.org/3/library/argparse.html](https://docs.python.org/3/library/argparse.html)

# nargs 参数

如果想在命令行参数中接受多个值，可以使用 argparse 模块中的 nargs 参数来指定参数接受的数量。下面是一个示例代码，展示了如何定义一个参数后面可以跟多个值的情况：

```python
import argparse
# 创建 ArgumentParser 对象
parser = argparse.ArgumentParser()
# 添加命令行参数，nargs='+' 表示参数可以接受一个或多个值
parser.add_argument('values', nargs='+', help='多个值',required=True)
#required=True 表示必须携带这个参数
# 解析命令行参数
args = parser.parse_args()
# 访问解析后的多个值
print('多个值:', args.values)
```

在上述代码中，通过将 nargs='+' 参数添加到 add_argument() 方法中，来指定该命令行参数可以接受一个或多个值。在解析命令行参数后，args.values 将会成为一个列表，包含了传入的多个值。

运行以上示例代码时，可以像下面这样在命令行中传入多个值：

```shell
python script.py value1 value2 value3
```

value1、value2 和 value3 将被作为 args.values 列表中的元素，并可以在代码中进一步处理。

请注意，在上述示例中，values 是参数的名称，你可以根据实际需求将其替换为你要使用的参数名称。

除了 nargs 参数之外，还有其他一些参数用于控制参数的数量，例如：

- * 表示接受零个或多个值。

- ? 表示接受零个或一个值。

- 1 表示接受且需要一个值。

更多关于 nargs 参数的具体用法，请参考官方文档：[https://docs.python.org/3/library/argparse.html#nargs](https://docs.python.org/3/library/argparse.html#nargs)

# type 参数

> **type参数，一旦进行指定，那么无论有没有设置默认值（default） 都会生效（调用）。**


要设置传入的参数值必须包含某个内容，可以使用自定义的验证函数来检查参数值是否符合要求。以下是一个示例代码：

```python
import argparse
# 自定义函数用于验证参数值
def check_value(value):
    if 'example' not in value:
        raise argparse.ArgumentTypeError('参数值必须包含 "example"')
    return value
# 创建 ArgumentParser 对象
parser = argparse.ArgumentParser()
# 添加命令行参数，并指定验证函数
parser.add_argument('-a', '--arg1', type=check_value, help='参数值必须包含 "example"')
# 解析命令行参数
args = parser.parse_args()
# 访问参数值
print('参数值:', args.arg1)
```

在上述示例代码中，我们定义了一个名为 check_value 的验证函数，用于检查参数值是否包含字符串 'example'。如果不符合要求，则抛出 argparse.ArgumentTypeError 异常。

然后，我们在 add_argument() 方法中使用 type=check_value 参数将自定义的验证函数应用于 -a 或 --arg1 参数。

当用户在命令行中传入参数时，会自动调用验证函数对参数值进行检查。如果参数值符合要求，解析会继续进行。否则，将抛出异常并显示帮助信息。

请注意，这只能确保参数值中包含指定的内容，但无法强制要求参数值只包含指定的内容。如果需要更复杂的验证逻辑，可以通过自定义函数来实现。

> **如果是接收多个值，也是拿那几个值一个一个的调用函数去检查，然后才封装成一个列表。**


# action 参数用法

在使用 Python 的 argparse 模块时，你可以通过设置参数的 action 属性为"store_true"来创建一个不需要值的参数。这样的参数通常被称为"标志"，当在命令行中指定该参数时，它将被视为 True；如果没有指定，则默认为 False。

下面是一个简单的示例代码，演示如何使用 argparse 创建一个不需要值的参数：

```python
import argparse
parser = argparse.ArgumentParser(description='Example with a flag argument')
parser.add_argument('--verbose', action='store_true', help='Produce verbose output')
args = parser.parse_args()
if args.verbose:
    print('Verbose output enabled')
else:
    print('Verbose output not enabled')
```

在上面的示例中，--verbose 参数被设置为store_true，这意味着它不需要额外的值。当在命令行中指定 --verbose 时，args.verbose 将被设置为 True；如果没有指定，则默认为 False。

除了"store_true"之外，argparse 模块的 action 参数还具有其他几种常用的选项，可以根据你的需求进行设置。下面是一些常见的 action 参数选项：

1. "store"（默认）：将参数值存储到一个变量中。这是最常用的 action 类型。例如：

```python
parser.add_argument('--name', action='store', help='Name of the person')
```

1. "store_const"：将一个常量值存储到一个变量中。可以与

const参数结合使用，const参数指定要存储的常量值。例如：

```python
parser.add_argument('--verbose', action='store_const', const=True, help='Enable verbose output')
```

1. "append"：将多个参数值存储到一个列表中。每次遇到该参数时，将值添加到列表中。例如：

```python
parser.add_argument('--file', action='append', help='Input file path')
```

1. "count"：计数参数出现的次数，并将结果存储到一个变量中。适用于需要统计某一选项被指定的次数的情况。例如：

```python
parser.add_argument('--debug', action='count', help='Increase debugging level')
```

1. "help"：显示帮助信息并退出。通常与其他参数结合使用，用于显示额外的帮助说明。例如：

```python
parser.add_argument('--help', action='help', help='Show this help message and exit')
```

这些是一些常见的 action 参数选项，可以根据你的需求选择适合的选项。argparse 还支持自定义的 action 类型，你可以继承 argparse.Action 类并重写其方法来实现自定义的行为。

# choices 参数

可以通过添加参数的choices参数来限制用户输入的参数的值。这将确保用户只能从预定义的选项中选择。

以下是一个示例代码，演示如何使用argparse限制参数值：

```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('--mode', choices=['option1', 'option2', 'option3'], help='Choose a mode')
args = parser.parse_args()
print('Selected mode:', args.mode)
```

在上面的代码中，我们定义了一个名为--mode的参数，并在choices参数中指定了三个选项：option1、option2和option3。用户只能从这三个选项中选择其中一个作为参数值。

运行脚本时，用户可以通过指定--mode参数来选择模式，例如：

```shell
python script.py --mode option2
```

如果用户尝试输入不在预定义选项中的值，argparse会抛出错误并显示帮助信息。

注意：choices参数只适用于字符串类型的参数。如果要限制数值类型的参数值范围，可以在解析后的参数对象上进行额外的验证。