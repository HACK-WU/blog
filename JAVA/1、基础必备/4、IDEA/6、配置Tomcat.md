

2.2 配置 Tomcat


# 2.2 配置 Tomcat


在 IDEA 中配置 Tomcat 之前，需要保证已经安装并配置了 Tomcat 的环境变量。


如果没有安装并配置，可以参考《尚硅谷_宋红康_Tomcat 快速部署.pdf》,配置完


成以后，在命令行输入：catalina run 。能够启动 tomcat，则证明安装配置成功。


下面看如何在 IDEA 中配置：

![](images/WEBRESOURCEf386aebf608a1afc7dc796b06514784d截图.png)







点击 Edit Configurations：

![](images/WEBRESOURCEfc11d224b75bde7ddd99209f5468b817截图.png)




这里选择 TomEE Server 或者 Tomcat Server 都可以。接着选择 Local。

![](images/WEBRESOURCEcade5853bcb2d201d9b488c6887be11d截图.png)







这里配置 Tomcat 的名称以及配置应用服务器的位置。根据自己 Tomcat 的安装位


置决定。

![](images/WEBRESOURCEa66e1a4dd824c81be35e953f500ee36f截图.png)




其它位置使用默认值(设置要启动的浏览器以及端口号)，如上。





接着部署：

![](images/WEBRESOURCE47f6ea631ef96d98784f1f3a3db6998d截图.png)




点击 OK 即可。此时：

![](images/WEBRESOURCEa1b86dde375d1725bd73902442cb5712截图.png)




执行刚才创建的 index.jsp 即可：

![](images/WEBRESOURCE1e9756b399cb460311d081c1593d06e4截图.png)




效果如下：

![](images/WEBRESOURCE4d53cbcf07547c560a136499d58f1ff5截图.png)







注意事项：


显示运行以后的 Tomcat 的信息：

![](images/WEBRESOURCE8a4b2f788be8d8e20678c607dcdb32fc截图.png)




可以点击红框，刚点击完毕并不能马上关闭服务器，只是断开了与服务器的连接，


稍后当停止按钮显示为灰色，才表示关闭。