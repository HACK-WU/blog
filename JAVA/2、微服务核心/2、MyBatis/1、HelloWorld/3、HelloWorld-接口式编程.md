# 1、定义一个接口interface xxxMapper:

```javascript
package com.atguigu.mybatis.dao;

import com.atguigu.mybatis.bean.Employee;

public interface EmployeeMapper {
	public Employee getEmpById(Integer id);
	
 //返回对象是一个JavaBea对象，已上线数据的输出；
 //Integer id : 指定的是数据库的数据类型，必须是Integer类型的，比如double的就不行；
 //在原来的方法中 #{id} ,指定的是数据名称，没有办法指定数据类型；
}
```

# 不需要人为的去实现这个接口，在绑定xxxMapper.xml映射文件之后，系统会自动为这个接口产生一个代理对象，并且实现它；

---

# 2、修改映射文件xxxMapper.xml:

```javascript
/*
*xxxMapper.xml文件是用于映射数据库的；
*	-<mapper namespace="">:指定的就是用哪一个对象去映射数据库
*		映射成功后，就可以简单地把这个对象看成是数据库的副本，因为在执行操作时，会首先把数据库的内容映射到这个类里面；
*
*	-<select id="方法名" resultType="返回的数据类型">Sql语句：#{param1} 、#{param2}</select>
*		1、执行时：测试类文件会使用接口Mapper类对象调用相应的方法比如
*			mapper.getxxx("param1","param2");此时这个方法里的实参会传递给
*			Sql语句中的#{param1},#{param2},然后使用获取到的实参，到数据库里进行查询相应的数据，
*			并把数据返回给一个POJO；
*			(POJO中的属性名称，必须得和数据库返回字段的名称相同，如果不同可使用别名，使得返回字段的名称和POJO属性名称一致)
*			如果mapper.get(object对象)，如果实参是一个对象，那么会使用对象的属性作为实参传递；
*		2、当POJO获取到数据后，Mapper对象就会真正的调用方法去获取POJO中属性的值，我们最后获取的数据实际上是从POJO中得到了，
*			而不是直接从数据库中获取；
*			具体Mapper调用的是哪个方法，必须要在id="方法名"中标注；
*			方法的返回值类型也需要在resultType="返回值类型"中标注；
*/
<mapper namespace="com.atguigu.mybatis.dao.EmployeeMapper">
//名字空间namespace:指定的是接口的类全名："包名.接口名"；


<select id="getEmpById" resultType="com.atguigu.mybatis.bean.Employee">
//id:指定的是接口中的方法；
//resultType:指定的是返回类型，是指定JavaBean的类全名；

select id,last_name lastname,gender,email from tbl_employee where id = #{id}
//具体的SQL语句
</select>
</mapper>
```



---

# 3、修改测试类代码

```javascript
public SqlSessionFactory  getSqlSessionFactory() throws IOException{
//封装了一个获取SqlSessionFactory对象的方法；
//获取SqlsessionFactory对象是获取SqlSession对象的必要前提；
		String resource="mybatis-confing1.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		return new SqlSessionFactoryBuilder().build(inputStream);
}
	
	@Test			//不可省略；
	public void test01() throws IOException {
	
		SqlSessionFactory sqlSessionFactory=getSqlSessionFactory();
 	 	//1、获取sqlSessionFactory对象
		
		SqlSession openSession=sqlSessionFactory.openSession();
  		//2、获取sqlSession 对象
		try {
		
		EmployeeMapper mapper=openSession.getMapper(EmployeeMapper.class);
  		//3、获取接口的实现类对象    
		Employee employee =mapper.getEmpById(1);	
		System.out.println(mapper.getClass());
		System.out.println(employee);
		}finally {
			openSession.close();
		}
	}

```





1、接口式编程

原生：		Dao		======>DaoImpl

mybatis:	Mapper	======>xxxMapper.xml



2、SqlSession代表和数据库一次会话，用完必须关闭；

3、SqlSession和connection一样都是非线程安全。每次使用都应该去获取新的对象。

4、mapper接口没有实现类，但是mybatis会为这个接口生成一个代理对象。







































