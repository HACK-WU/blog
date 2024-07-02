# 1、os.system()

这个方法是直接调用标准C的system() 函数，仅仅在一个子终端运行系统命令，而不能获取命令执行后的返回信息。

os.system(cmd)的返回值。如果执行成功，那么会返回0，表示命令执行成功。否则，则是执行错误。

使用os.system返回值是脚本的退出状态码，该方法在调用完shell脚本后，返回一个16位的二进制数，低位为杀死所调用脚本的信号号码，高位为脚本的退出状态码。

> os.system()    返回值为0,linux命令返回值也为0。
> os.system()  **  **返回值为256，十六位二进制数示为：00000001，00000000，高八位转成十进制为 1        对应   linux命令返回值 1。
> os.system()    返回值为512，十六位二进制数示为：00000010，00000000，高八位转成十进制为 2        对应   linux命令返回值 2。


```
import os
result = os.system('cat /etc/passwd')
print(result)      # 0
```

# 2、os.popen()

os.popen()方法不仅执行命令而且返回执行后的信息对象(常用于需要获取执行命令后的返回信息)，是通过一个管道文件将结果返回。通过 os.popen() 返回的是 file read 的对象，对其进行读取 read() 的操作可以看到执行的输出。

```
import os
result = os.popen('cat /etc/passwd')
print(result.read())
```

# 3、commands 模块

```python
import commands
 
status = commands.getstatus('cat /etc/passwd')
print(status)
output = commands.getoutput('cat /etc/passwd')
print(output)
(status, output) = commands.getstatusoutput('cat /etc/passwd')
print(status, output)
```

# 4、subprocess模块

Subprocess是一个功能强大的子进程管理模块，是替换os.system ,os.spawn* 等方法的一个模块。

当执行命令的参数或者返回中包含了中文文字，那么建议使用subprocess。

```python
import subprocess
res = subprocess.Popen('cat /etc/passwd', shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT) # 使用管道
# print res.stdout.read()  # 标准输出
for line in res.stdout.readlines():
    print line
res.stdout.close()         # 关闭
```

# 5、总结

```
os.system：获取程序执行命令的返回值。
os.popen： 获取程序执行命令的输出结果。
commands：获取返回值和命令的输出结果。
```