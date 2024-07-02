# cmd.has_exec 

-  判断command是否是一个可执行的命令

```shell
salt  '*' cmd.has_exec command
```

# cmd.retcode

- 在minion端执行一个shell命令并返回命令的返回码。0表示成功，0以外表示失败有问题。

```shell
salt  '*' cmd.retcode 'ls -l /etc/hostname'  
```

# cmd.run

- 执行命令，并返回所有的输出信息

```shell
salt  '*' cmd.run 'echo hello'  
```

# cmd.run_stderr

- 与cmd.run 类似，但是只返回标准错误输出

# cmd.run_stdout

- 与cmd.run 类似，但只返回标准输出，不包括标准错误输出

# cmd.which

- 查看某个命令的所在位置，相当于就是在linux上执行了which命令

```shell
salt '*' cmd.which ifconfig   
#查看节点所有ifconfig命令的位置，在写脚本或者定时任务的时候很有用，因为系统不同执行文件的绝对路径也不通。# salt '*' cmd.run  "which ifconfig"一个效果。
```

# cmd.which_bin

- 可用于判断某个命令位置是否比其他命令靠前

```shell
salt '*' cmd.which_bin '[cat, ifconfig, touch]'  #这是定义了一个列表，返回在命令列表中找到的第一个命令。
```