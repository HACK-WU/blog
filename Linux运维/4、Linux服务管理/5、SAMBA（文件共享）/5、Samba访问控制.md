```
控制读写权限
    writable = yes/no
    readonly = yes/no
如果资源可写，但只允许某些用户可写，其他都是只读
    write list = admin, root, @staff（用户组）
    read list = mary, @students
控制访问对象
    valid users = tom,mary,@itcast
    invalid users = tom
注意：以上两个选项只能存在其中一个
网络访问控制：
    hosts deny = 192.168.0.     拒绝某个网段
    hosts allow = 192.168.0.254     允许某个IP
    hosts deny = all             拒绝所有
    hosts allow = 192.168.0. EXCEPT 192.168.0.254 允许某个网段，但拒绝某个单个IP
    注意：deny和allow同时存在，allow优先
```

7、总结

1. ftp 局域网和外网都可以

1. nfs** 局域网** 挂载方式访问 mount.nfs 侧重于Linux与Linux之间

1. samba** 局域网** 直接访问(smbclinet)挂载的方式mount.cifs 侧重于Windpws与Linux之间