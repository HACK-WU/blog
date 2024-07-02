# 1、is_in 相当于python中的in

```shell
function is_in {            #判断一个对象是否在一个迭代对象中
    # $1 被查找的字符
    local var=$1
    shift
    local item
    for item in $@
    do
    if [ "$item" == "$var"  ];then
            return 0
    fi
    done
    return 1
}
```

# 2、join 相当于python中的str.join()

```shell
function join {    
     local var=$1
     local res=""
     shift
     local item
     for item in $@
     do
         res=$res"$var"$item
     done
     res=$(echo $res| cut -c2-)
     echo $res
 }
```

- 运行效果

```
join "=" 1 2 3 4
>> 1=2=3=4
```

# 3、自定义return 及获取

```shell
declare -A my_return            #用于存储函数的返回的结果
function get_return {            #用于获取返回值
         echo ${my_return[$1]}   #输出返回值
         unset my_return[$1]     #删除这个返回值
         return 0
 }
 
 
function fun {
    ........
    my_return["fun"]=value   #存储此函数函数的返回结果。key值，与函数名相同    
}
res=$(get_return fun)    #这里的fun只作为一个字符参数，不会被当成函数执行
echo $res
```

# 4、split()

- 有两种实现方式，任选其一

```shell
function split {        
        local str=$1
        local fs=$2
        echo $str | sed 's#'''$fs'''# #g'
}
function split {
        local str=$1
        local fs=$2
        echo ${str//$fs/" "}
}
```

# 5、str_in   判断某个字符串中，是否包含某个字符

```shell
function str_in {
    echo "hsjahsa" | grep -q "sa"
    return $?
}
```

# 6、parse_YN 解析Yes和NO

```shell
function parse_YN {
    #默认直接回车返回0
    #也可以指定$2 的值，如果为no ,那么直接回车返回1
    local  yes_no="$1"
    set +u
        local  default=${2-yes}
    set -u
    [ "$(echo $default)" == "" ]  &&  default="yes"  
    yes_no=$(echo $yes_no | tr "[A-Z]" "[a-z]" | cut -c1)
    default=$(echo $default | tr "[A-Z]" "[a-z]" | cut -c1)
    
    [ "$default" == "y"  ] && default=0 || default=1    
    [ "$(echo $yes_no)" == "" ] && return $default 
    [ "$yes_no" == "y" ]  && return 0 || return 1
}

```

# 7、参数设置默认值

```shell
function fun {
    set +u
        local str=${1-yes}        #设置$1 的默认值为yes，没有传入就是yes。
    set -u
    echo $str
}

fun 
```

# 8、 input 键盘输入

```shell
declare -A my_return            #用于存储函数的返回的结果
function get_return {            #用于获取返回值
         echo ${my_return[$1]}   #输出返回值
         unset my_return[$1]     #删除这个返回值
         return 0
 }
 
function input {
    #输入q ,退出
    #$1: 每次输入前的提示信息
    #$2: 接收到空值时，循环的次数,默认是3次。
    set +u
        local msg=${1-">>:"}
        local total=${2-"3"}
    set -u
    local words
    local i
    for((i=0;i<=$total;i++))
    do  
        echo -n "$msg" 
        read words 
        [ "$words" == ""  ] && continue
        [ "$words" == "q" ] && return 1
        break
    done
    [ "$words" == ""  ] &&  return 2
    my_return[input]="$words"    
    return 0
}

input "请输入: " 5
words=$(get_return input)
echo  $words
```