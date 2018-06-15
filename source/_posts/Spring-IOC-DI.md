---
title: Spring-IOC/DI
date: 2017-04-9 10:55:45
tags:
- Spring
---

Spring是最受企业级Java应用程序开发的框架之一，它是一个基于IOC(Inversion Of Control-控制反转)、DI(Dependency Injection-依赖注入)和AOP(-面向切面编程)来构架多层系统的框架，可以很大程度的简化开发。

**特性：**
1. 降低组件之间的耦合度，实现软件各层之间的解耦（MVC模式）
2. IOC（依赖注入）：管理应用对象的配置和生命周期，可以配置每个bean是怎样创建的，以及bean之间的关联关系
3. AOP（面向切面）：采用面向切面编程来实现很多基础但是与业务逻辑无关的功能的解耦，比如：事务管理、日志记录、权限验证等
4. DAO：Spring提供了JDBC的封装，使用JdbcTemplate来简化数据库操作
5. WEB：提供Spring MVC和对显示层框架的集合支持

<!-- more -->

# Spring模块关系
![Spring模块划分](/img/Spring/Spring-1.png)

## 控制反转
Spring容器是Spring框架的核心。容器将创建对象，进行相关配置，并管理他们的整个生命周期。Spring使用依赖注入（DI）来管理组成一个应用程序的组件。这些对象被称为Spring容器的Beans。
如果在应用程序内部进行其他对象的创建，例如：
```java
public class A {

    private B b = new B();  //在A内部显示创建B，对象之间有依赖

    public void say()
    {
        b.say();
    }
}
```
**在程序运行期间，由外部容器（Spring的IOC容器）动态将依赖对象注入到另一个对象中：**
```java
public class A {

    private B b;  //在A内部显示创建B，对象之间有依赖

    public void say()
    {
        b.say();
    }
}
```

### 代码实现Spring的IOC功能

现在模拟实现web项目中action调用service；service调用dao的场景。

1.引入spring的核心jar包
2.配置applicationContext.xml文件
   在src/com/xiaopeng/ioc/backup下新建applicationContext.xml文件如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	   xmlns:p="http://www.springframework.org/schema/p"
	   xmlns:context="http://www.springframework.org/schema/context"
	   xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

</beans>      
```

3.新建接口IUserDao.java
```java
package com.xiaopeng.ioc.backup;

public interface IUserDao {

    /**
     * 保存数据
     */
    public void save();
}

```
4.新建Dao：UserDao.java
```java
package com.xiaopeng.ioc.backup;

public class UserDao implements IUserDao {
    @Override
    public void save() {
        System.out.println("保存用户数据");
    }
}

```

5.新建UserService.java

```java
package com.xiaopeng.ioc.backup;

public class UserService {

    private IUserDao dao;

    public UserService(IUserDao dao) {
        this.dao = dao;
    }

    public void save()
    {
        dao.save();
    }
}

```

6.新建UserAction.java
```java
package com.xiaopeng.ioc.backup;

public class UserAction {

    private UserService userService;

    public UserAction(UserService userService) {
        this.userService = userService;
    }

    public void save()
    {
        userService.save();
    }
}

```

7.传统方式调用Dao
```java
package com.xiaopeng.ioc.backup;

public class App {

    public static void main(String[] args)
    {
        IUserDao dao = new UserDao(); //获取dao(数据持久化)
        UserService service = new UserService(dao); //获取serivce(业务控制)
        UserAction action = new UserAction(service); //事件处理
        action.save();
    }
}

```

从上面的测试App类可以发现，传统应用程序都是由我们在类的内部主动创建依赖对象，从而导致类与类之间高耦合，难于测试。

8.使用IOC容器存储数据
- 在上述applicationContext.xml的11行添加如下内容
```xml
<!-- 将UserDao添加进ioc容器 -->
<bean id="userDao" class="com.xiaopeng.ioc.backup.UserDao"/>

<bean id="userService" class="com.xiaopeng.ioc.backup.UserService">
	<constructor-arg index="0" ref="userDao"/>   <!-- 向service的构造函数中动态注入userDao -->
</bean>

<bean id="userAction" class="com.xiaopeng.ioc.backup.UserAction">
	<constructor-arg index="0" ref="userService"/>  <!-- 向action的构造函数中动态注入userService -->
</bean>
```

- 通过加载applicationContext.xml文件得到bean容器对象，进而得到bean对象，并调用其方法
```java
package com.xiaopeng.ioc.backup;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

    public static void main(String[] args)
    {
        //得到IOC容器
        ApplicationContext ac = new ClassPathXmlApplicationContext("com/xiaopeng/ioc/backup/applicationContext.xml");

        //得到UserAction对象
        UserAction userAction = (UserAction) ac.getBean("userAction");

        //保存数据
        userAction.save();
    }
}

```

**总结：**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spring所倡导的开发方式：所有的类都会在spring容器中登记，告诉spring你是个什么东西[Service]，你需要什么东西[Dao]，然后spring会在系统运行到适当的时候，把你要的东西[Dao]主动给你，同时也把你交给其他需要你的东西。所有的类的创建、销毁都由 spring来控制，也就是说控制对象生存周期的不再是引用它的对象，而是spring。对于某个具体的对象而言，以前是它控制其他对象，现在是所有对象都被spring控制，所以这叫控制反转。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;控制反转不是一种技术，只是一种思想，一个重要的面向对象编程的法则，它能够指导我们编写松耦合的程序。使用IOC容器的思想，把创建和查找对象之间依赖关系的控制权限交给了容器，由容器进行注入，所以对象之间是松耦合的。这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;IOC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，依赖注入）来实现的。比如对象A需要操作数据库，以前我们总是要在A中自己编写代码来获得一个Connection对象，有了 spring我们就只需要告诉spring，A中需要一个Connection，至于这个Connection怎么构造，何时构造，A不需要知道。在系统运行时，spring会在适当的时候制造一个Connection，然后像打针一样，注射到A当中，这样就完成了对各个对象之间关系的控制。A需要依赖 Connection才能正常运行，而这个Connection是由spring注入到A中的，否则就会出现空指针异常，依赖注入的名字就这么来的。那么DI是如何实现的呢？ Java 1.3之后一个重要特征是[反射](https://xpengv.github.io/2017/03/21/Java反射/)（reflection），它允许程序在运行的时候动态的生成对象、执行对象的方法、改变对象的属性，spring就是通过反射来实现注入的。
















































