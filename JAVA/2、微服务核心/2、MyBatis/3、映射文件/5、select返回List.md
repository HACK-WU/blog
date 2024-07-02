# select返回List

如果从数据库查询的结果是多条纪录，可以考虑把这些纪录封装在List中，就像数组一样



---

# 1、xxxMapper.java接口中添加相应的方法

```java
public interface EmployeeMapper {
    List<Employee> getEmpsByLastNameLike(String lastName);
   //返回类型为List类型，它的元素依然为Employee;
}
```

# 2、xxxMapper.xml添加相应的<select>

```xml
<select id="getEmpsByLastNameLike" resultType="mybatis.bean.Employee">
    select * from tbl_employee where last_name like #{lastname}
    <!--
  		  like:模糊查询
    	因为List中的元素依然是Empoyee,所以resultType="mybatis.bean.Employee",而不是list;
    -->
</select>
```

# 3、Test.java测试类运行

```java
public void test04() throws IOException {
    //1、获取sqlSessionFactory对象
    SqlSessionFactory sqlSessionFactory=getSqlSessionFactory();
    //2、获取sqlSession 对象
    SqlSession openSession=sqlSessionFactory.openSession();
    try {
        //3、获取接口的实现类对象
        EmployeeMapper mapper=openSession.getMapper(EmployeeMapper.class);
        List<Employee>  like=mapper.getEmpsByLastNameLike("%e%");//模糊查询
        for(Employee employee:like){
            System.out.println(employee);
        }
      //  System.out.println(employee);
    }finally {
        openSession.close();
    }
}
```

