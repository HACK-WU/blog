- 用户验证方式：

- PAM(gdm,kdm.su,ssh,ftp,sambal

- 自动数据库验证方式(MySQL,Zabbix)

- web验证方式(htpasswd)

# **PAM介绍**

PAM(Pluggable Authentication Modules)即可插拔式认证模块，它是一种高效而且灵活的用户级别的认证方式 ,它也是当前Linux服务器普遍使用的认证方式。

PAM可以根据用户的网段、时间、用户名、密码等实现认证。

# PAM身份认证

使用PAM做身份认证的服务有：本地（login、gdm、kdm ) , sshd , vsftpd , samba等不使用PAM做身份认证的服务有：MySQL-Server , Zabbix等

- PAM使用帮助

```
[root@localhost ~]# firefox /usr/share/doc/pam-1.1.8/html/Linux-PAM_SAG.htm1
```

- PAM认证原理

```shell
Service(进程文件)  -->  PAM(配置文件)    ---> pam_*.50                  ----->   模块的配置文件
/usr/sbin/sshd          /etc/pam.d/sshd      /lib64/security/pam_access.so     /etc/security/access.conf     
                                             /lib64/security/pam_limits.so     /etc/security/limits.conf
                                             /lib64/security/pam_time.so       /etc/security/time.conf
/bin/su                 /etc/pam.d/su        /lib64/security/pam_rootok.so
```

```
[root@localhost ~]# vim /etc/pam.d/sshd
#%PAM-1.0
auth    required    pam_sepermit.so
auth    substack    password-auth
auth     include    postlogin
# Used with polkit to reauthorize users in remote sessions
#与polkit 起使用以重新授权远程会话中的用户
-auth      optional    pam_reauthorize.so prepare
account    required    pam_nologin.so
account    include     password-auth
password   include     password-auth
# pam selinux.so close should be the first session rule
# selinux关闭执行如下
session    required    pam_selinux.so close
session    required    pam_loginud.so
#pam selinux.so open should only be followed by sessions to be executed in the user context
#selinux开启执行如下
session    required    pam selinux.so open env params
session    required    pam namespace.so
session    optional    pam_keyinit.so force revoke
session    include     password-auth
session    include     postlogin
# Used with polkit to reauthorize users in remote sessions
# 与polkit-起使用以重新授权远程会话中的用户
-auth      optional    pam_reauthorize.so prepare
account    required    pam_nologin.so
account    include     password-auth
password   include     assworl-auth
#pam selinux.so close should be the first session rule
#selinux关闭执行如下
session    required    pam_selinux.so close
session    required    pam_loginuid.so
# pam_selinux.so open should only be followed by sessions to be executed in the user context
#selinux开启执行如下
session    required    pam_selinux.so open env_params
session    required    pam namespace.so
session    optional    pam_keyinit.so force revoke
session    include    password-auth
session    include    postlogin
# Used with polkit to reauthorize users in remote sessions
-session    optional    pam reauthorize.so prepare
```

- PAM常见的四种认证类型

```
auth        认证管理        验证使用者身份，账号和密码
account     用户管理        基于用户时间或密码有效期来决定是否允许访问
password    密码（☐令）     认证管理 禁止用户反复尝试登录，在变更密码时进行密码复杂性控制
session     会话管理         进行日志记录，或者限制用户登录的次数，资源限制
```

- PAM认证流程控制(流程标记)

```
Required     （必要条件） 验证失败时仍然继续I但返回fail                        用户不会知道哪里失败
Requisite    （必要条件） 验证失败时则立即结束整个验证过程，返回fail 面试若不成功，马上失败，效率高
Sufficient   （充分条件） 验证成功则立即返回，不再继续 否则忽略结果并继续        相当于面试中的拔高题
Optional     （可选条件）无论验证结果如何，均不会影响     常用于session类型
Include                包含另外一个配置文件中类型相同的行
substack                垂直疊加
```

- **PAM常用模块**

```
模块：pam_rootok.so
功能：用户UID是O，返回成功
示例：限制root切换用户也需要密码
[root@localhost ～]# head -1 /etc/pam.d/su
#auth sufficient pam_rootok.so
示例：sshd不需要密码登录
[root@localhost ~]# head -1 /etc/pam.d/sshd
auth sufficient pam_rootok.so
```

- 模块：pam_access.so	访问控制模块

```
模块：pam_access.so
功能：访问控制，默认配置文件/etc/security/access.conf
通常作用于登录程序，如su,login,gdm,sshd，例如限制用户从哪些网段登录sshd
示例：不允许root从192,168.1.0/24登录sshd
[root@localhost ~]# grep access.so /etc/pam.d/sshd
auth    required    pam_access.so
[root@localhost ~]# vim /etc/security/access.conf
-:root:192.168.122.0/24
-:root:ALL EXCEPT 192.168.1.0/24
-:root:192.168.122.0/24 EXCEPT 192.168.122.1
示例：使用不同的模块配置文件
[root@localhost ~]# grep access /etc/pam.d/login
auth    required    pam_access.so accessfile=/accessfile2
[root@localhost ~]# grep jack /accessfile2
- :jack:tty5 tty6
[root@localhost ~]# grep access.so /etc/pam.d/sshd
auth    requisite    pam_access.so accessfile=/accessfilel
[root@localhost ~]# grep 110 /accessfilel
-:root:ALL EXCEPT 192.168.2.110
```

- pam_listfile.so		用户黑名单

```
vim /etc/pam.d/sshd
auth    required    pam_listfile item=user sense=deny file=/etc/ssh_users onerr=succed
```

- pam_listfile.so		用户白名单

```
vim /etc/pam.d/sshd
auth    required    pam_listfile item=user sense=allow file=/etc/ssh_users onerr=fail
```

- 模块：pam_time.so

```
模块：pam_time.so
功能：基于时间的访问控制，默认文件/etc/security/time.conf
示例：基于时间限制sshd的访问
[root@localhost ~]# grep time /etc/pam.d/sshd
account    required    pam_time.so
[root@localhost ~]# grep 0800 /etc/security/time.conf
sshd;*;*;MoTuWeThFr0800-1100
```

- 模块：pam_tally2.so

```
模块：pam_tally2.so
功能：登录统计
示例：实现防止对sshd暴力破解
[root@localhost ~]# grep tally2 /etc/pam.d/sshd
auth    required    pam_tally2.so deny=2 even_deny_root root_unlock_time=60    unlock_time=60
#deny=2 连续错误登录最大次数，超过最大次数，将被锁定
#even_deny_root root用户也被要求锁定
#root_unlock_time root用户被锁定后等待的时间，单为秒
#unlock time 普通用户被锁定后等待的时间，单为秒
```