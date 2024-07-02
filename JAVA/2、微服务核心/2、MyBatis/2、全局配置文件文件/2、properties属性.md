- mybatis可以使用properties来引入外部properties配置文件的内容；


- resource：引入内路径下的资源；


- url: 引入网络路径或者磁盘路径下的资源；



# 1、在config文件夹下创建文件：dbconfig.properties,并添加如下信息：

```javascript
#conf/dbconfig.properties				//说明文件位置以及文件名称
jdbc.driver=com.mysql.jdbc.Driver			//驱动
jdbc.url=jdbc:mysql://localhost:3306/mybatis	//mysql数据库地址
jdbc.username=root
jdbc.password=123456
```



# 2、修改mybatis-config.xml文件

```javascript
<configuration>
	<!-- 
		1、mybatis可以使用properties来引入外部properties配置文件的内容；
			resource：引入内路径下的资源；
			url: 引入网络路径或者磁盘路径下的资源；
	 -->
  
	<properties resource="dbconfig.properties"></properties>	//添加
 	//resource：指定外部配置文件位置
	
<environments default="development">
<environment id="development">
<transactionManager type="JDBC"/> 

<dataSource type="POOLED">

<property name="driver" value="${jdbc.driver}"/>		//修改
<property name="url" value="${jdbc.url}"/>			//修改
<property name="username" value="${jdbc.username}"/>	//修改
<property name="password" value="${jdbc.password}"/>	//修改
```

# 3、测试运行测试类输出如下结果：

```javascript
DEBUG 09-16 12:36:30,249 ==>  Preparing: select id,last_name lastname,gender,email from tbl_employee where id = ?   (BaseJdbcLogger.java:145) 
DEBUG 09-16 12:36:30,298 ==> Parameters: 1(Integer)  (BaseJdbcLogger.java:145) 
DEBUG 09-16 12:36:30,338 <==      Total: 1  (BaseJdbcLogger.java:145) 
class com.sun.proxy.$Proxy4
Employee [id=1, lastname=tom, email=tom@qq.com, gender=0]
```































