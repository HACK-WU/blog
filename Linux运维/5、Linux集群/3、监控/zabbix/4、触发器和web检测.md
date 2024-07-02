# **1、触发器（重点）**

触发器(trigger):定义监控项到达一个临界值（阈值）或者满足一个条件，就会发现状态变化(OK变为Problem,Problem变为OK

下面我把监控agent1的登录用户数做一个触发器（大于20个登录用户就警告）

1. 创建触发器

![](images/WEBRESOURCE86da3f2daf449c28e7ccdc7238c74247截图.png)

1. 编辑

![](images/WEBRESOURCE8822565921e311c9d8ef9290df0dbb4e截图.png)

1. 选择报警规则

![](images/WEBRESOURCE77c63d5db6844777ba073b53e24f89e9截图.png)

1. 效果

![](images/WEBRESOURCE44f4ac3640c87fe06f6ebe3102f59350截图.png)

1.  测试

> **1、手动添加多个用户登录**
> **2、然后查看仪表盘，是否有显示问题**


![](images/WEBRESOURCE16cda0f2949b84edd3e3964606e6d39e截图.png)

> **测试成功！！**


# 2、web检测

- 创建web场景

![](images/WEBRESOURCE97a3527446e9193afe14e168bd4fac66截图.png)

- 场景中填入相关信息，然后选择——>步骤

![](images/WEBRESOURCEce2d175fd0dfc7e0644fcf02344cbf1d截图.png)

- 步骤-->选择添加

![](images/WEBRESOURCE4c3ca86d69feebb1c7f1232ed65a1cd6截图.png)

- 配置

![](images/WEBRESOURCEe886131414243ea6fac6842330a256f7截图.png)

> **注意：**
> **1、要求的字符串，只要是网页中包含这个字符串，就算认证成功。**
> **2、也可以某个文件中的内容有这个字符串，比如：httpd://localhost/test.txt  如果text.txt中包含那个字符串，也算认证成功。**


- 效果

![](images/WEBRESOURCEdd045bc7bf4302b9e107f23926f9f6cb截图.png)