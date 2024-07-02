# 二、ansible



![1541733368758](图片/ansible.png)

ansible是一种由Python开发的自动化运维工具，集合了众多运维工具（puppet、cfengine、chef、func、fabric）的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能。

特点:

* 部署简单
* **默认使用ssh进行管理，基于python里的==paramiko==模块开发**
* 管理端和被管理端不需要启动服务
* 配置简单，功能强大，扩展性强
* 能过playbook(剧本)进行多个任务的编排



![1542029281767](图片/ansible3.png)



## ansible环境搭建

**实验准备:** 三台机器，一台管理，两台被管理

![1542020956973](图片/ansible2.png)

1. 静态ip
2. 主机名及主机名互相绑定
3. 关闭防火墙, selinux
4. 时间同步
5. 确认和配置yum源(需要epel源)



**实验过程:**

**第1步: 管理机上安装ansible，被管理节点必须打开ssh服务.**

~~~powershell
# yum install epel-release
# yum install ansible
# ansible --version
ansible 2.8.4
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, Oct 30 2018, 23:45:53) [GCC 4.8.5 20150623 (Red Hat 4.8.5-36)]
~~~

**第2步:  实现master对agent的免密登录，只在master上做。(如果这一步不做，则在后面操作agent时都要加-k参数传密码;或者在主机清单里传密码)**

~~~powershell
master# ssh-keygen

master# ssh-copy-id -i 10.1.1.12
master# ssh-copy-id -i 10.1.1.13
~~~

**第3步: 在master上定义主机组,并测试连接性**

~~~powershell
master# vim /etc/ansible/hosts 
[group1]
10.1.1.12
10.1.1.13
~~~

~~~powershell
master# ansible -m ping group1
10.1.1.13 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
10.1.1.12 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}    
~~~

~~~powershell
master# ansible -m ping all
10.1.1.13 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
10.1.1.12 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
~~~



## 服务器分组

ansible通过一个主机清单功能来实现服务器分组。

Ansible的默认主机清单配置文件为/etc/ansible/hosts.

**示例:** 

~~~powershell
[nginx]					组名
apache[1:10].aaa.com	表示apache1.aaa.com到apache10.aaa.com这10台机器
nginx[a:z].aaa.com		表示nginxa.aaa.com到nginxz.aaa.com共26台机器
10.1.1.[11:15]			表示10.1.1.11到10.1.1.15这5台机器
~~~

**示例:** 

~~~powershell
[nginx]
10.1.1.13:2222			表示10.1.1.13这台，但ssh端口为2222
~~~

**示例: 定义10.1.1.12:2222这台服务器的别名为nginx1**

~~~powershell
nginx1 ansible_ssh_host=10.1.1.13 ansible_ssh_port=2222
~~~

**示例: 没有做免密登录的服务器可以指定用户名与密码**

~~~powershell
nginx1  ansible_ssh_host=10.1.1.13 ansible_ssh_port=2222 ansible_ssh_user=root ansible_ssh_pass="123456"
~~~

**示例: 利用别名来分组**

~~~powershell
nginx1  ansible_ssh_host=10.1.1.13 ansible_ssh_port=2222 ansible_ssh_user=root ansible_ssh_pass="123456"
nginx2  ansible_ssh_host=10.1.1.12

[nginx]
nginx1
nginx2
~~~

**小结:**

主机清单的作用: 服务器分组。

主机清单的常见功能:

1. 可以通过IP范围来分, 主机名名字的范围来分

2. 如果ssh端口不是22的，可以传入新的端口。

3. 没有做免密登录，可以传密码。



**练习:** 不论你用哪种环境(免密或不免密，端口是否22）, 请最终将两台被管理机器加入到group1组即可



