**PAM资源限制**

- PAM资源限制主要是对用户进行系统资源使用的限制

- PAM资源限制默认已使用，我们只需要调整相应限制值即可。

- 模块：pam_limits.so

```shell
#模块：pam_limits.so
#功能：限制用户会话过程中对各种资源的使用情况。缺省情况下该模块的件是
/etc/security/limits.conf
/etc/security/limits d/* .conf
```

# PAM资源限制案例

- 案例1：设置用户最大打开文件数

```shell
[root@server1 ~]# ulimit -a
[root@localhost ~]# vim /etc/security/limits.conf
*    soft    nofile    10240
*    hard    nofile    10480
[root@server1 ~]# ulimit -n -S
[root@server1 ~]# ulimit -n  -H
```

- 案例2：设置用户最大创建的进程数

```shell
[jack@localhost ulimit -u
1024
[jack@localhost ～]# vim /etc/security/limits.d/90-np
*    soft    nproc    10240
*    hard    nproc    10240
root  soft   nproc    unlimited    //没有限制
```

- 案例3：设置用户nginx最大使用CPU的时间

```shell
[root@localhost ~]# vim /etc/security/limits.conf
nginx	hard	cpu    1
```

**PAM资源限制针对用户，不针对进程，如果需要实现进程资源限制，可以考虑使用Cgroup。**