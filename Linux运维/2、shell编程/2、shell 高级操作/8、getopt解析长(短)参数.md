**原文：**[linux getopt命令介绍 非常详细_caixxiong-华为云开发者联盟 (csdn.net)](https://huaweicloud.csdn.net/635669afd3efff3090b5e5f6.html?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Eactivity-1-118731808-blog-125938027.235%5Ev38%5Epc_relevant_anti_vip&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Eactivity-1-118731808-blog-125938027.235%5Ev38%5Epc_relevant_anti_vip&utm_relevant_index=1#devmenu1)

# 1.命令行选项的那些事

在学习getopt如何使用之前，必须先知道命令行的一些常识。这些，都可以通过getopt来实现，但有些实现起来可能会比较复杂。

**1.区分option、parameter、argument、option argument和non-option parameter**

parameter和argument都表示参数，前者通常表示独立性的参数，后者通常表示依赖于其它实体的参数。parameter的含义更广，argument可以看作parameter的一种。

例如，定义函数时

再例如，一个命令行：

```shell
tar -zcf a.tar.gz /etc/pki
```

粗分的话，

- "-z -c -f"称为选项，即option

- a.tar.gz是选项"-f"的**选项参数**

- /etc/pki既不属于选项，也不属于某个选项的参数，它称为**非选项类型的参数**

本文要介绍的是getopt，所以只考虑命令行参数的情况。

**2.短选项和长选项以及它们的"潜规则"**

Linux中绝大多数命令都提供了短选项和长选项。一般来说，短选项是只使用一个"-"开头，选项部分只使用一个字符，长选项是使用两个短横线(即"--")开头的。

例如"-a"是短选项，"--append"是长选项。

一般来说，选项的顺序是无所谓的，但并非绝对如此，有时候某些选项必须放在前面，必须放在某些选项的前面、后面。

一般来说，短选项：

- 可以通过一个短横线"-"将多个短选项连接在一起，但如果连在一起的短选项有参数的话，则必须作为串联的最后一个字符。

例如"-avz"其实会被解析为"-a -v -z"，

- 短选项的参数可以和选项名称连在一起，也可以是用空白分隔。例如

-n 3

- 如果某个短选项的参数是可选的，那么它的参数必须紧跟在选项名后面，不能使用空格分开。至于为什么，见下面的第3项。

一般来说，长选项：

- 可以使用等号或空白连接两种方式提供选项参数。例如

--

--

- 如果某个长选项的参数是可选的，那么它的参数必须使用"="连接。至于为什么，见下面的第3项。

- 长选项一般可以缩写，只要不产生歧义即可。

例如，ls命令，以"a"开头的长选项有3个。

```shell
$ ls --help | grep -- '--a' 
  -a, --all                  do not ignore entries starting with .
  -A, --almost-all           do not list implied . and ..
      --author               with -l, print the author of each file
```

如果想要指定

```shell
$ ls --a
ls: option '--a' is ambiguous; possibilities: '--all' '--author' '--almost-all'
Try 'ls --help' for more information.
```

**3.不带参数的选项、可选参数的选项和带参数的选项**

有不同类型的命令行选项，这些选项可能不需要参数，也可能参数是可选的，也可能是强制要求参数的。

前面说了，如果某个选项的参数是可选的，那么它的参数必须不能使用空格将参数和选项分开。如果使用空格分隔，则无法判断它的下一个元素是该选项的参数还是非选项类型的参数。

例如，

一般来说，使用可选参数的情况非常少，至少我目前回忆不起来这样的命令(mysql -p选项是一个)。

**4.使用"--"将选项(及它们的选项参数)与非选项类型参数进行分隔**

unix的命令行中，总是可以在非选项类型的参数之前加上"--"，表示选项和选项参数到此为止，后面的都是非选项类型的参数。

例如：

```shell
seq -w -- 3
seq -w -- 1 3
```

分别表示3和"1 3"是seq的非选项类型参数，而"--"前面的一定是选项或选项参数。

**5.命令行参数中的短横线开头的并不一定总是短选项，也可能是负数参数**

例如seq命令：

```shell
seq -w -5 -1 5
```

其中-5和-1都是负数非选项类型的参数。

**6.选项的依赖性和互斥性**

有些命令的选项是有依赖性和互斥性的。比如某个选项要和另一个选项一起使用，某个选项不能和另一个选项一起使用。

例如

**7.模式化(模块化)类型的选项**

很多unix命令都将选项进行模块化设计。例如ip命令，address模式、route模式、link模式等等。

```shell
ip addr OPTIONS
ip route OPTIONS
ip link OPTIONS 
ip neigh OPTIONS
```

**8.其他特性的选项**

有些命令还有比较个性化的选项。

比如head命令，

再比如有的命令支持逻辑运算，例如find命令的-a、-o选项。

# 2.getopt解析选项的工作机制

bash的getopt命令经常用在shell脚本内部或函数内部，用来解析脚本执行或函数执行时传递的选项、参数。

下面都以命令行为例解释getopt是如何解析参数的，但用来解析函数参数是一样的。

## 2.1 getopt选项

下面这个是最常用的getopt解析方式(有这个命令就够了)。如果要了解getopt更完整的语法，见man getopt。

```shell
getopt -o SHORT_OPTIONS -l LONG_OPTIONS -n "$0" -- "$@"
```

其中：

-o SHORT_OPTIONS

*--options SHORT_OPTIONS*

getopt通过"-o"选项收集命令行传递的短选项和它们对应的参数。关于SHORT_OPTIONS的格式见下一小节。

-l LONG_OPTIONS

*--longoptions LONG_OPTIONS*

getopt通过"-l"选项收集命令行传递的长选项和它们对应的参数。可能从别人的脚本中经常看到"--long"，是等价的，前文已经解释过，长选项只要不产生歧义，是可以进行缩写的。关于LONG_OPTIONS的格式见下一小节。

-n 

getopt在解析命令行时，如果解析出错(例如要求给参数的选项没带参数，使用了无法解析的选项等)将会报告错误信息，getopt将使用该NAME作为报错的脚本名称。

-- 

其中

## 2.2 getopt如何解析选项和参数

getopt使用"-o"或"-l"解析短、长选项和参数时，将会对每个解析到的选项、参数进行输出，然后不断放进一个字符串中。这个字符串的内容就是完整的、规范化的选项和参数。

getopt使用"-o"选项解析短选项时：

- 多个短选项可以连在一起

- 如果某个要解析的选项需要一个参数，则在选项名后面跟一个冒号

- 如果某个要解析的选项的参数可选，则在选项名后面跟两个冒号

- 例如，

getopt -o ab:c::

-

getopt使用"-l"选项解析长选项时：

- 可以一次性指定多个选项名称，需要使用逗号分隔它们

- 可以多次使用-l选项，多次解析长选项

- 如果某个要解析的选项需要一个参数，则在选项名后面跟一个冒号

- 如果某个要解析的选项的参数可选，则在选项名后面跟两个冒号

- 例如，

getopt -l add:,remove::,show

--add

*--add*

*--remove*

*--show*

如果解析的是带参数的选项，则getopt生成的字符串中，会将选项的参数值作为该选项的下一个参数。如果解析的是可选参数的选项，如果为该选项设置了参数，则会将这个参数放在选项的下一个参数位置，如果没有为该选项设置参数，则会生成一个用引号包围的空字符串作为选项的下一个参数。

getopt

默认情况下，该加强版本的getopt会将所有参数值(包括选项参数、非选项类型的参数)使用引号进行包围，以便保护空白字符和特殊字符。如果是兼容版本的getopt，则不会用引号保护，所以会破坏参数解析。

看后面的示例就很容易理解了。

## 2.3 示例分析getopt的解析方式

例如在脚本test.sh中，下面的getopt的结果保存到变量parameters中，然后输出getopt解析完成后得到的完整参数列表。

```shell
#!/usr/bin/env bash
parameters=getopt -o ab:c:: --long add:,remove::,show -n <span class="hljs-string">"<span class="hljs-variable">$0</span>"</span> -- <span class="hljs-string">"<span class="hljs-variable">$@</span>"</span>
echo “$parameters”
```

如果还不知道这里的

执行这个脚本，并给这个脚本传递一些选项和参数，这些脚本参数将被收集到

```shell
$ ./test.sh -a non-op_arg1 -b b_short_arg non-op_arg2 --rem --add /path --show -c non-op_arg3
 -a -b 'b_short_arg' --remove '' --add '/path' --show -c '' -- 'non-op_arg1' 'non-op_arg2' 'non-op_arg3'
```

首先可以看出，传递给脚本的参数都是无序的：

- 长选项有：

- --rem

- --add

- --show

- 短选项有：

- -a

- -b

- -c

- 非选项类型的参数有：

- non-op_arg1

- non-op_arg2

- non-op_arg3

从getopt的输出结果中，可以看出：

- 先解析选项和选项参数

- 选项和选项参数是按照从左向右的方式进行解析的

- 参数都使用引号包围

- 那些参数可选的选项，当没有为它们提供参数时，将生成一个引号包围的空字符串参数

- 解析完所有的选项和选项参数后，开始解析非选项类型的参数

- 非选项类型的参数前面，会生成一个"--"字符串，它将选项(以及选项参数)与非选项类型的参数隔开了

# 3.处理getopt解析的结果

getopt解析得到了完整、规范化的结果，当然要拿来应用。例如直接传递个函数，或者根据while、case、shift将选项、参数进行分割单独保存。

如果要进行分割，由于getopt的解析结果通常保存在一个变量中，要解析这个结果字符串，需要使用eval函数将变量的内容进行还原，一般来说会将其设置为一个位置参数(因为shift只能操作位置变量)。

一般来说，整个处理流程是这样的：

```shell
parameters=$(getopt -o SHORT_OPTIONS -l LONG_OPTIONS -n "$0" -- "$@")
[ $? != 0 ] && exit 1
eval set -- "$parameters"   # 将$parameters设置为位置参数
while true ; do             # 循环解析位置参数
    case "$1" in
        -a|--longa) ...;shift ;;    # 不带参数的选项-a或--longa
        -b|--longb) ...;shift 2;;   # 带参数的选项-b或--longb
        -c|--longc)                 # 参数可选的选项-c或--longc
            case "$2" in 
                "")...;shift 2;;  # 没有给可选参数
                *) ...;shift 2;;  # 给了可选参数
            esac;;
        --) ...; break ;;       # 开始解析非选项类型的参数，break后，它们都保留在$@中
        *) echo "wrong";exit 1;;
    esac
done
```

需要注意，getopt解析既可以放在脚本中解析命令行参数，也可以放在某个函数中解析函数参数。

# 4.getopt的两种扫描模式

getopt提供了两种扫描模式，只要在getopt的短选项前加上加号或负号，就能指定两种扫描模式，即

- +

- -

例如，对于命令行参数

```shell
$ set -- -w -s -5 3 -2  # 设置位置参数
$ getopt -o +s:w -n "$0" -- "$@"
 -w -s '-5' -- '3' '-2'      # 解析结果
```

注意，上面的-5是被解析成了-s的参数，而不是选项或非选项类型的参数，因为-s选项必须要指定一个参数。

上面的3必须不能是负数，因为

使用

```shell
$ set -- 3 -w 4 -s -5 a 3
$ getopt -o -s:w -n "$0" -- "$@"
 '3' -w '4' -s '-5' 'a' '3' --    # 解析结果
```

可以看到，上面的所有参数位置都是保持原样的，且将分隔符号"--"补在了最尾部。

# 5.如何实现命令行选项的各种个性功能

在前面[命令行选项的那些事](https://huaweicloud.csdn.net/635669afd3efff3090b5e5f6.html?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Eactivity-1-118731808-blog-125938027.235%5Ev38%5Epc_relevant_anti_vip&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Eactivity-1-118731808-blog-125938027.235%5Ev38%5Epc_relevant_anti_vip&utm_relevant_index=1#blog1)中介绍了几种有"个性"的选项功能，包括：

- 选项依赖：例如"-a"或"--add"要依赖于"-m"或"--manage"选项

- 选项互斥：例如"-a"或"--add"与"-r"或"--remove"是互斥的

- 识别负数参数：例如

-w 

- 模式化选项：例如

script_name 

- 选项参数替代选项：例如

head

head

这里介绍下用getopt解析参数后实现它们的思路。

在getopt解析完成后，假设返回结果保存到了

**1.选项依赖性**

这个其实很好实现，只需使用grep对

例如实现依赖性，只需：

```shell
{ echo "$parameters" | grep -E '\-\-add|\-a ' | grep -E '\-\-manage|\-m '; } &>/dev/null
[ $? -ne 0 ] && exit
```

**2.选项互斥性**

要实现互斥性，只需：

```shell
or_op=`echo "$parameters" | grep -Eo '\-\-add|\-a | \-\-remove|\-r ' | wc -l`
[ "$or_op" = "2" ] && exit
```

**3.识别负数参数**

前面解释过，getopt提供了两种扫描模式，只要使用

**4.模式化选项**

一般来说，模式化选项都是命令行的第一个参数。所以，只需将

例如，要提供addr、show、route三种模式，那么其它的非选项类型参数值都不应该是模式参数。

```shell
eval set -- "$parameters"
while true ; do
    case "$1" in
            ...
        --) 
            shift
            [ "$x" = "addr" -o "$x" = "route" -o "$x" = "show" ] && MODE=$1
            shift
            break ;;
        *) echo "wrong";exit 1;;
    esac
done
```

**5.选项参数替代选项**

就以

```shell
NUM=`echo "$@" | grep -Eo "\-[0-9]+"`
ARGS=`echo "$@" | sed -nr 's!(.*)-[0-9]+(.*)!\1\2!'p`
eval set -- "$ARGS"
```

# 6.使用getopt设计shell脚本选项示例

这里提供一个和seq命令功能相同的脚本seq.sh，然后设计这个脚本的选项。

先看一下seq命令的各个选项说明：

```shell
seq [OPTION]... LAST                  # 语法1
seq [OPTION]... FIRST LAST            # 语法2
seq [OPTION]... FIRST INCREMENT LAST  # 语法3
```

选项：

-

使用指定的STRING分隔各数值，默认值为

-w, --equal-width

使用

–help

显示帮助信息并退出

–version

输出版本信息并退出

以下是脚本内容：和seq相比，只有两个问题：第一个起点数值FIRST不能为负数；不支持小数功能。其它功能完全相同

```shell
#!/usr/bin/env bash
usage(){
cat <<'EOF'
Usage: $0 [OPTION]... LAST
  or:  $0 [OPTION]... FIRST LAST
  or:  $0 [OPTION]... FIRST INCREMENT LAST
EOF
}
```

*# getopt的版本是增强版吗*

getopt -T &>/dev/null;[ $? -ne 4 ] && { 

*# 参数解析*

parameters=

[ $? -ne 0 ] && { 

eval

while

case

-w|–equal-width) ZERO_PAD=

-s|–separator) SEPARATOR=

–version) 

–

–)

shift

FIRST=

INCREMENT=

LAST=

break

*) usage;

esac

done