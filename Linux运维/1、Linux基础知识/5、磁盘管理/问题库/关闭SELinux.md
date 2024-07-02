# 1、查看SELinux状态（Enforcing 为开启状态，Permissive/Disabled为关闭状态）

```javascript
[root@bogon ~]# getenforce
Enforcing

```

# 2、关闭SELinux状态（临时生效）

- setenforce   0  关闭

- setenforce    1  开启

```javascript
[root@bogon ~]# setenforce 0
[root@bogon ~]# getenforce
Permissive
[root@bogon ~]# 

```

# 3、关闭SELinux(永久生效：修改配置文件）

- vi    /etc/selinux/config  :修改配置文件

-   SELINUX=enforcing   ：开启状态；

-   SELINUX=disabled     :关闭状态；

```javascript
[root@bogon ~]# vi /etc/selinux/config 


# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing             //修改此处
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted

```

 