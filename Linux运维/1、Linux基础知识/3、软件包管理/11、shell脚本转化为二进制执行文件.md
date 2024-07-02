# shell脚本转化为二进制执行文件的两种方法因为脚本中需要明文显示密码的需求，想法是将已经编辑好的shell脚本，封装成二进制可执行文件。

要点：该工具不支持生成的二进制文件在其他主机上使用。

使用工具shc

官方下载网站：[http://www.datsi.fi.upm.es/~frosal/](http://www.datsi.fi.upm.es/~frosal/)

使用最新版本：shc-3.8.9b.tgz

```
tar -zxvf shc-3.8.9b.tgz
cd shc-3.8.9b/
make
```

编译成功后

当前目录下增加一个可执行文件shc

```
cp shc /usr/local/bin/
```

使用命令

shc -r -f xxx.sh

运 行后会生成两个文件,xxx.sh.x 和 xxx.sh.x.c. 其中xxx.sh.x是加密后的可执行的二进制文件；用./xxx.sh.x即可运行，xxx.sh.x.c是生成 xxx.sh.x的原文件(c语言).

shc -v -r -T -f

另外还有个-T选项，加了T选项后，在Mac上双击运行竟然可以看到脚本里面的内容。

-T Allow binary to be traceable (using strace, ptrace, truss, etc.)

/usr/local/src/shc-3.8.7/shc -e 27/03/2018 -m "contact xxx@163.com" -v -r -f ./xxx.sh

```
-e:指定过期时间为2018年03月20日
-m:过期后打印出的信息；
-v: verbose
-r: 可在相同操作系统的不同主机上执行
-f: 指定源shell
```

在rhel6上使用请注意

实测 shc -r -f 二进制封装脚本，但是无法使用cron正常执行，换用可trace的方式进行进行封装，shc -T -f ，cron正常执行。

制定任务计划

08 08 * /path/xxx.sh.x

第二种为gzexe

gzexe命令即可隐藏shell源码

gzexe xxx.sh

生成加密后的脚本xxx.sh和shell源码xxx.sh~

缺点：gzexe可以直接转换明文。

gzexe -d xxx.sh

转载于:[https://blog.51cto.com/mengix/2091341](https://blog.51cto.com/mengix/2091341)