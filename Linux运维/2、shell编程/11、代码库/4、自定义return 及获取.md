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