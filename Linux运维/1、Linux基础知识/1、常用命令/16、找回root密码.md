其实这里的找回，其实直接修改root的密码，但提前条件是拿到服务器；

```
1. 重启系统
2. 将光标移动到要启动的内核
3. 按e编译当前条目
4. 将光标移至以linux开头的行，此为内核命令行
5. 在末尾添加rd.break
6. 按ctrl+x继续启动
7.重启以读写方式挂载/sysroot
    switch_root:/# mount -o remount,rw /sysroot
8. 切换至真正操作系统的根/sysroot
    switch_root:/# chroot /sysroot
9. 重置密码
    sh-4.4# echo 123456 | passwd --stdin root
10. 打标签
    sh-4.4# touch /.autorelabel
11. exit退出两次
```