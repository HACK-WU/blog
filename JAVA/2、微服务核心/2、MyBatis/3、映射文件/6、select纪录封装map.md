# select纪录封装map

从数据库查询的多条纪录，除了可以封装在List中，

也可以选择封装在Map中



# 一个Map封装多条纪录



## 1、xxxMapper.java接口中填写相应的方法

```javascript
public interface EmployeeMapper {
    //多条纪录封装一个map:Map<Integer,Employee>:键是这条纪录的主键，值是纪录封装后的javabean
    //告诉mybatis封装这个map的时候使用哪个属性作为主键，这个主键是Map的主键，
    //Map(Integer,Object)中，Integer就是主键，
    @MapKey("id")       		
    Map<Integer,Employee> getEmpByLastNameLikeReturnMap(String lastName);
}

```

## 2、在xxxMapper.xml中添加<select>

```javascript
//一个map中封装的多条纪录，每条纪录的类型依然是Employee类型
<select id="getEmpByLastNameLikeReturnMap" resultType="mybatis.bean.Employee">
  select  * from tbl_employee where  last_name like #{lastName}
</select>
```

## 3、test.java测试运行

```javascript
public void test04() throws IOException {
    //1、获取sqlSessionFactory对象
    SqlSessionFactory sqlSessionFactory=getSqlSessionFactory();
    //2、获取sqlSession 对象
    SqlSession openSession=sqlSessionFactory.openSession();
    try {
        //3、获取接口的实现类对象
        EmployeeMapper mapper=openSession.getMapper(EmployeeMapper.class);
        Integer,Employee>map=mapper.getEmpByLastNameLikeReturnMap("%r%");
        System.out.println(map);
      //  System.out.println(employee);
    }finally {
        openSession.close();
    }
}
```



---

## 一个Map封装一条纪录



## 1、xxxMapper.java中添加方法

```javascript
Map<String,Object> getEmpByIdReturnMap(Integer id);
```

## 2、xxxMapper.xml添加<select>

```javascript
<select id="getEmpByIdReturnMap" resultType="map">
    select * from tbl_employee where id=#{id}
</select>
```

## 3、Test.java测试运行

```javascript
public void test04() throws IOException {
    //1、获取sqlSessionFactory对象
    SqlSessionFactory sqlSessionFactory=getSqlSessionFactory();
    //2、获取sqlSession 对象
    SqlSession openSession=sqlSessionFactory.openSession();
    try {
        //3、获取接口的实现类对象
        EmployeeMapper mapper=openSession.getMapper(EmployeeMapper.class);
        Map<String,Object>map=mapper.getEmpByIdReturnMap(1);
        System.out.println(map);
      //  System.out.println(employee);
    }finally {
        openSession.close();
    }
}
```















