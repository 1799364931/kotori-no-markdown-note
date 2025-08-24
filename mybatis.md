# Mybatis学习笔记

## 1 mybatis配置
### 1.1 maven依赖
```xml
<!-- springboot的依赖和org.mybatis选一个安装 -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>3.0.5</version>
        </dependency>
        
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.33</version>
        </dependency>
        
```



## 2 简单使用
### 2.1 手动配置
手动配置的情况下，需要自己构建一个``SqlSessionFactory``来获得对应的``SqlSession``从而获得对应的``Mapper``。
#### 2.1.1 配置mybatis-config.xml文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="abc3183363"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
    	<!-- <package name = "包名"> 对应了 @MapperScan
        	 <mapper class = "类名"> 对应了 @Mapper	
        	 <mapper resource = "../../../xml"> 对应了 在配置文件中指定xml路径
        	 但是springboot无论用哪种，都强制约定接口名和映射文件同名-->
        <mapper resource="mapper/UserServiceMapper.xml"/>
    </mappers>
</configuration>
```
#### 2.1.2 编写获取SqlSession的类
```java
package com.example.mybatis_project.util;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtil {
    private static SqlSessionFactory sqlSessionFactory;

    // 初始化 从xml 里面导入一个 SqlSessionFactory 
    static {
        try{
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        }catch (Exception e){
            e.printStackTrace();
        }
    }

     // 获得SqlSession 的实例
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}
```

#### 2.1.3 使用mybatis
```
    @Test
    public void getUserList() throws ClassNotFoundException {
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        var userList = userMapper.getUserList();
        userList.forEach(System.out::println);

        sqlSession.close();
    }
```

### 2.2 Springboot配置(Mapper + XML)
利用注解@Mapper或者@MapperScan扫描对应的包来进行映射，此时只要依赖注入对应的Mapper接口就可以直接调用接口，无需手动管理SqlSeesion。

#### 2.2.1 在配置文件中添加数据源
```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&serverTimezone=UTC&characterEncoding=utf8&useUnicode=true&allowPublicKeyRetrieval=true
spring.datasource.username=root
spring.datasource.password=root
```

#### 2.2.2 添加映射
```java
@Mapper
public interface UserMapper {
     List<UserDao> getUserList();
}
```
#### 2.2.3 注入Mapper使用
```java
	// 也可以加入@MapperScan("com.example.mybatis_project.dao")会自动注册整个包下面的映射，更方便
    @Autowired
    UserMapper userMapper;
    @Test
    public void getUserList() throws ClassNotFoundException {
        var userList = userMapper.getUserList();
        userList.forEach(System.out::println);
    }
```

### 2.3 通过注解使用Mybatis
其余和2.2一样，注意不要即通过注解又通过xml配置映射，否则会导致映射重复。
```java
	@Mapper
public interface UserMapper {
     @Select({"select * from user"})
     List<UserDao> getUserList();
}
```



## 4 踩坑

### 4.1 springboot自动配置mapper无法解析文件
```
2025-08-23T18:39:34.632+08:00  WARN 70752 --- [mybatis_project] [           main] o.s.w.c.s.GenericWebApplicationContext   : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'sqlSessionFactory' defined in class path resource [org/mybatis/spring/boot/autoconfigure/MybatisAutoConfiguration.class]: Failed to instantiate [org.apache.ibatis.session.SqlSessionFactory]: Factory method 'sqlSessionFactory' threw exception with message: Failed to parse mapping resource: 'file [C:\Users\18316\IdeaProjects\mybatis_project\src\main\resources\mapper\UserServiceMapper.xml]'
```
这种情况下需要进一步查看报错
```
Caused by: java.lang.IllegalArgumentException: Mapped Statements collection already contains key com.example.mybatis_project.dao.UserMapper.getUserList. please check mapper/UserServiceMapper.xml and file [C:\Users\18316\IdeaProjects\mybatis_project\target\classes\mapper\UserServiceMapper.xml]
```
发现是重复的接口定义，这里需要检查：
1. 是否有多个xml定义了同一个接口的mapper
2. 是否配置了多个MapperScan导致重复扫描
3. 是否mapper.xml和接口的命名不一致（这里是一个大坑！）

### 4.2 resource目录下文件无法编译到target中
这种情况一般是在pom中没有配置对应的resource导致的，或者配置了对应的resource，但是没有包含缺失文件的通配符。
```xml
<!-- 比如说这里没有写yml的通配，yml就可能不会加入到target/class文件中-->
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
```
