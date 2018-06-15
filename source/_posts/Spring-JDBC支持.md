---
title: Spring-JDBC支持
date: 2017-05-05 20:44:24
tags:
- Spring
- JDBC
---
Spring对jdbc技术提供了很好的支持：
体现在：
- Spring对c3p0连接池的支持
- Spring对jdbc操作提供了JdbcTemplate，来简化jdbc操作

## 引入jar包
1. spring-jdbc-3.2.5.RELEASE.jar
2. spring-tx-3.2.5.RELEASE.jar
3. c3p0-0.9.1.2.jar
4. mysql-connector-java-5.1.12-bin.jar

<!-- more -->

### bean对象
```java
package com.xiaopeng.spring.jdbc;

public class User {

    private int id;
    private int age;
    private String name;

    /* getter / setter */

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", age=" + age +
                ", name='" + name + '\'' +
                '}';
    }
}

```

### 编写IUserDao接口
IUserDao接口支持插入数据、查询返回所有用户数据、根据主键查询某个用户数据
```java
package com.xiaopeng.spring.jdbc;

import java.util.List;

public interface IUserDao {

    /**
     * 插入数据
     */
    void save();

    /**
     * 查询所有用户信息
     */
    List<User> findAll();

    /**
     * 根据主键查询数据
     */
    User findById();
}

```

### UserDao
对于常规的jdbc操作，按照以下方式进行：
```java
public void save(User user) {
        try {
            String sql = "insert into user_table(age,name) values(" + user.getAge() + ",'" + user.getName() + "')";

            System.out.println(sql);
            Connection con = null;
            Statement stmt = null;
            Class.forName("com.mysql.jdbc.Driver");
            // 连接对象
            con = DriverManager.getConnection("jdbc:mysql:///spring", "root", "root");
            // 执行命令对象
            stmt = con.createStatement();
            // 执行
            stmt.execute(sql);

            // 关闭
            stmt.close();
            con.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

### 使用JdbcTemplate：
```java
package com.xiaopeng.spring.jdbc;

import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import javax.sql.DataSource;
import java.sql.*;
import java.util.List;

public class UserDao implements IUserDao {

    private DataSource dataSource;
    private JdbcTemplate jdbcTemplate;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    //通过set方式注入 JdbcTemplate
    public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Override
    public void save(User user) {
        String sql = "insert into user_table(age,name) values(" + user.getAge() + ",'" + user.getName() + "')";
        jdbcTemplate.update(sql);  //新增记录
    }

    @Override
    public List<User> findAll() {
        String sql = "select * from user_tables";
        List<User> list = jdbcTemplate.query(sql,new MyRow());
        return list;
    }

    @Override
    public User findById(int id) {
        String sql = "select * from user_tables where id = ?";
        List<User> list = jdbcTemplate.query(sql,new MyRow(),id);
        return list.get(0);
    }


    //返回javabean对象
    class MyRow implements RowMapper<User> {

        //封装查询的返回记录为java对象
        @Override
        public User mapRow(ResultSet resultSet, int i) throws SQLException {
            User user = new User(resultSet.getInt("age"),resultSet.getString("name"));
            return user;
        }
    }
}

```

其中MyRow这个类非常有用，可以把查询结果映射为JavaBean对象，通过org.springframework.jdbc.core.RowMapper接口实现。

### 编写spring-jdbc.xml配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 1. 数据源对象: C3P0连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql:///spring"></property>
        <property name="user" value="root"></property>
        <property name="password" value="root"></property>
        <property name="initialPoolSize" value="3"></property>
        <property name="maxPoolSize" value="10"></property>
        <property name="maxStatements" value="100"></property>
        <property name="acquireIncrement" value="2"></property>
    </bean>

    <!-- 2. 创建JdbcTemplate对象 -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!-- 向Dao中注入JdbcTemplate -->
    <bean id="userDao" class="com.xiaopeng.spring.jdbc.UserDao">
        <property name="JdbcTemplate" ref="jdbcTemplate"></property>
    </bean>
</beans>

```

### 测试类：
```java
package com.xiaopeng.spring.jdbc;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.util.List;

public class App {

    @Test
    public void test() {
        ApplicationContext ac = new ClassPathXmlApplicationContext("com/xiaopeng/spring/jdbc/spring-jdbc.xml");

        IUserDao userDao = (IUserDao) ac.getBean("userDao");
        userDao.save(new User(23, "Jack"));
        userDao.save(new User(24, "Tom"));

        //查询所有数据并输出
        List<User> list = userDao.findAll();

        for (User u : list) {
            System.out.println(u);
        }
    }
}

```

**总结：**
Spring框架的JDBC模块，所有的类可以被划分为四个单独的包：
1. core：包含了jdbc的核心功能，包括JdbcTemplate、SimpleJdbcInsert、SimpleJdbcCall等
2. datasource：数据源配置的相关功能
3. object：对象关系映射功能
4. support：其他支持功能，例如异常装换等

与传统的JDBC代码相比较，JdbcTemplate简单明了，并且有良好的事务管理机制。
