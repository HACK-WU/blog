# **1、SELinux安全子系统**

SELinux（Security-Enhanced Linux）是美国国家安全局在Linux开源社区的帮助下开发的一个强制访问

控制（MAC，Mandatory Access Control）的安全子系统。RHEL 7系统使用SELinux技术的目的是为了

让各个服务进程都受到约束，使其仅获取到本应获取的资源。

- SELinux服务有三种配置模式，具体如下。

- enforcing：强制启用安全策略模式，将拦截服务的不合法请求。

- permissive：遇到服务越权访问时，只发出警告而不强制拦截。

- disabled：对于越权的行为不警告也不拦截。

然在禁用SELinux服务后确实能够减少报错几率，但这在生产环境中相当不推荐。

```
[root@localhost ~]# vim /etc/selinux/config
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#   enforcing - SELinux security policy is enforced.
#   permissive - SELinux prints warnings instead of enforcing.
#   disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of three values:
#   targeted - Targeted processes are protected,
#   minimum - Modification of targeted policy. Only selected processes are
protected.
#   mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

SELinux服务的主配置文件中，定义的是SELinux的默认运行状态，可以将其理解为系统重启后的状态，

因此它不会在更改后立即生效。可以使用getenforce命令获得当前SELinux服务的运行模式

```
[root@localhost ~]# getenforce
Enforcing
```

可以用setenforce [0|1]命令修改SELinux当前的运行模式（0为禁用，1为启用）。注意，这种修改只是

临时的，在系统重启后就会失效

我们比较一下自己创建的acpache目录和原本的目录的安全上下文

```
[root@localhost ~]# ls -Zd /var/www/html
drwxr-xr-x. root root system_u:object_r:httpd_sys_content_t:s0 /var/www/html
[root@localhost ~]# ls -Zd /home/wwwroot/
drwxr-xr-x. root root unconfined_u:object_r:home_root_t:s0 /home/wwwroot/
```

在文件上设置的SELinux安全上下文是由用户段、角色段以及类型段等多个信息项共同组成的。其中，

- 用户段system_u代表系统进程的身份，

- 角色段object_r代表文件目录的角色，

- 类型段httpd_sys_content_t代表网站服务的系统文件

# 2、semanage

用于管理SELinux的策略

semanage [选项] [文件]

使用semanage命令时，经常用到的几个参数及其功能如下所示：

- -l参数用于查询；

- -a参数用于添加；

- -m参数用于修改；

- -d参数用于删除。

可以向新的网站数据目录中新添加一条

httpd服务程序所访问到

```
[root@localhost ~]# semanage fcontext -a -t httpd_sys_content_t
/home/wwwroot/
[root@localhost ~]# semanage fcontext -a -t httpd_sys_content_t
/home/wwwroot/*
[root@localhost ~]# restorecon -Rv /home/wwwroot/
restorecon reset /home/wwwroot context unconfined_u:object_r:home_root_t:s0-
>unconfined_u:object_r:user_home_dir_t:s0
restorecon reset /home/wwwroot/index.html context
unconfined_u:object_r:home_root_t:s0-
>unconfined_u:object_r:httpd_sys_content_t:s0
```

注意，执行上述设置之后，还无法立即访问网站，还需要使用restorecon命令将设置好的SELinux安全

上下文立即生效。在使用restorecon命令时，可以加上-Rv参数对指定的目录进行递归操作，以及显示

SELinux安全上下文的修改过程。最后，再次刷新页面，就可以正常看到网页内容了。

使用getsebool命令查询并过滤出所有与HTTP协议相关的安全策略。其中，off为禁止状态，on为允许状态。

```
[root@localhost ~]# getsebool -a | grep http
httpd_anon_write --> off
httpd_builtin_scripting --> on
httpd_can_check_spam --> off
httpd_can_connect_ftp --> off
httpd_can_connect_ldap --> off
httpd_can_connect_mythtv --> off
httpd_can_connect_zabbix --> off
httpd_can_network_connect --> off
httpd_can_network_connect_cobbler --> off
httpd_can_network_connect_db --> off
httpd_can_network_memcache --> off
httpd_can_network_relay --> off
httpd_can_sendmail --> off
httpd_dbus_avahi --> off
httpd_dbus_sssd --> off
httpd_dontaudit_search_dirs --> off
httpd_enable_cgi --> on
httpd_enable_ftp_server --> off
httpd_enable_homedirs --> off
httpd_execmem --> off
httpd_graceful_shutdown --> on
httpd_manage_ipa --> off
httpd_mod_auth_ntlm_winbind --> off
httpd_mod_auth_pam --> off
httpd_read_user_content --> off
httpd_run_ipa --> off
httpd_run_preupgrade --> off
httpd_run_stickshift --> off
httpd_serve_cobbler_files --> off
httpd_setrlimit --> off
httpd_ssi_exec --> off
httpd_sys_script_anon_write --> off
httpd_tmp_exec --> off
httpd_tty_comm --> off
httpd_unified --> off
httpd_use_cifs --> off
httpd_use_fusefs --> off
httpd_use_gpg --> off
httpd_use_nfs --> off
httpd_use_openstack --> off
httpd_use_sasl --> off
httpd_verify_dns --> off
named_tcp_bind_http_port --> off
prosody_bind_http_port --> off
[root@localhost ~]# setsebool -P httpd_enable_homedirs=on
```

面对如此多的 SELinux 域安全策略规则，实在没有必要逐个理解它们，我们只要能通过名字大致猜测出

相关的策略用途就足够了。比如，想要开启 httpd 服务的个人用户主页功能，那么用到的 SELinux 域安

全策略应该是 httpd_enable_homedir