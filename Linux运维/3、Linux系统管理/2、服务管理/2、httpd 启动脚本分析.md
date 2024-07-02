# 1、独立服务的启动脚本分析（httpd)


既然独立的服务启动是依靠/etc/init.d/httpd 这个脚本来进行启动管理的，那么这个脚本中到


底是什么样子的？既然我们已经学习了 shell 脚本，那么我们就来学习一下这个脚本到底是怎么实现


apache 服务的管理的。


```javascript
#!/bin/bash
#
# httpd Startup script for the Apache HTTP Server
#
# chkconfig: - 85 15
#自启动设定 -代表自启动级别，85（S85）代表启动序号，15（K15）代表关闭序号。
# description: The Apache HTTP Server is an efficient and extensible \
# server implementing the current HTTP standards.
#服务描述。以上两行用于 apache 自启动。
# processname: httpd
# config: /etc/httpd/conf/httpd.conf
# config: /etc/sysconfig/httpd

# pidfile: /var/run/httpd/httpd.pid
#
### BEGIN INIT INFO
# Provides: httpd
# Required-Start: $local_fs $remote_fs $network $named
# Required-Stop: $local_fs $remote_fs $network
# Should-Start: distcache
# Short-Description: start and stop Apache HTTP Server
# Description: The Apache HTTP Server is an extensible server
# implementing the current HTTP standards.
### END INIT INFO
#以上都是注释。
# Source function library.
. /etc/rc.d/init.d/functions
#"."其实就是 source，就是调用 functions 文件。
if [ -f /etc/sysconfig/httpd ]; then
. /etc/sysconfig/httpd
fi
#判断 httpd 如果是文件，则调用 httpd 文件。
# Start httpd in the C locale by default.
HTTPD_LANG=${HTTPD_LANG-"C"}
#定义变量 HTTPD_LANG 的值。并追加变量的值为 C，即英文。
# This will prevent initlog from swallowing up a pass-phrase prompt if
# mod_ssl needs a pass-phrase from the user.
INITLOG_ARGS=""
# Set HTTPD=/usr/sbin/httpd.worker in /etc/sysconfig/httpd to use a server
# with the thread-based "worker" MPM; BE WARNED that some modules may not
# work correctly with a thread-based MPM; notably PHP will refuse to start.
# Path to the apachectl script, server binary, and short-form for messages.
apachectl=/usr/sbin/apachectl
httpd=${HTTPD-/usr/sbin/httpd}
prog=httpd
pidfile=${PIDFILE-/var/run/httpd/httpd.pid}
lockfile=${LOCKFILE-/var/lock/subsys/httpd}
#定义一系列变量，用于后面的执行。
RETVAL=0
#定义全局命令返回变量。

STOP_TIMEOUT=${STOP_TIMEOUT-10}
# The semantics of these two functions differ from the way apachectl does
# things -- attempting to start while running is a failure, and shutdown
# when not running is also a failure. So we just do it the way init scripts
# are expected to behave here.
start() {
echo -n $"Starting $prog: "
LANG=$HTTPD_LANG daemon --pidfile=${pidfile} $httpd $OPTIONS
RETVAL=$?
echo
[ $RETVAL = 0 ] && touch ${lockfile}
return $RETVAL
}
#定义 start 函数，用于 apache 的启动。
#如果守护进程/usr/sbin/httpd 启动成功（$RETVAL = 0），就建立/var/lock/subsys/httpd 文件（touch
#${lockfile}）。通过$httpd 变量执行/usr/sbin/httpd 命令启动 apache。通过$pidfile 变量调用 apache
#的 PID。通过变量$OPTIONS 定义命令执行时的初始化环境配置，依赖/etc/sysconfig/httpd 文件。
# When stopping httpd, a delay (of default 10 second) is required
# before SIGKILLing the httpd parent; this gives enough time for the
# httpd parent to SIGKILL any errant children.
stop() {
echo -n $"Stopping $prog: "
killproc -p ${pidfile} -d ${STOP_TIMEOUT} $httpd
RETVAL=$?
echo
[ $RETVAL = 0 ] && rm -f ${lockfile} ${pidfile}
}
#定义 stop 函数，用来关闭 apache 服务，关闭服务之后会删除 pid 文件。
reload() {
echo -n $"Reloading $prog: "
if ! LANG=$HTTPD_LANG $httpd $OPTIONS -t >&/dev/null; then
RETVAL=6
echo $"not reloading due to configuration syntax error"
failure $"not reloading $httpd due to configuration syntax error"
else
# Force LSB behaviour from killproc
LSB=1 killproc -p ${pidfile} $httpd -HUP
RETVAL=$?
if [ $RETVAL -eq 7 ]; then
failure $"httpd shutdown"
fi

fi
echo
}
#定义 reload 函数，用于 apache 的重新加载。
#通过/usr/sbin/httpd –t 命令判断 apache 的配置文件。如果配置文件报错，则输出错误提示。如果配
#置文件正确，则重新加载 apache。
# See how we were called.
case "$1" in
#判断执行脚本后的第一个参数的值，$1 表示执行脚本时的第一个参数。
start)
start
;;
;;
#如果参数值为 start，则调用 start 函数。
stop)
stop
;;
#如果参数值为 stop，则调用 stop 函数。
status)
status -p ${pidfile} $httpd
RETVAL=$?
;;
#如果参数值为 status，则执行 status –p $httpd 命令测试 apache 状态。
restart)
stop
start
;;
#如果参数值为 restart，则先调用 stop 函数，再调用 start 函数
condrestart|try-restart)
if status -p ${pidfile} $httpd >&/dev/null; then
stop
start
fi
;;
#如果参数值为 condrestart 或 try-restart，则只有 apache 服务是已经运行时才先调用 stop 函数，再调
#用 start 函数，重启 apache。如果 apache 服务没有运行，则不重启 apache。
force-reload|reload)
reload
;;
#如果参数值为 force-reload 或 reload，则调用 reload 函数。
graceful|help|configtest|fullstatus)
$apachectl $@

RETVAL=$?
;;
#如果参数是 graceful 或 help 或 configtest 或 fullstatus，则执行/usr/sbin/apachectl 命令，并把参
#数作为命令的参数传入 apachectl 命令。
*)
echo $"Usage: $prog
{start|stop|restart|condrestart|try-restart|force-reload|reload|status|fullstatus|graceful|help|config
test}"
RETVAL=2
#如果输出的参数不是以上任何参数，则输出错误信息
esac
exit $RETVAL
```



通过这个脚本，我们可以对 apache 服务的启动有更深的了解了