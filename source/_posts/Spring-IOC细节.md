---
title: Spring-IOC细节
date: 2017-04-14 21:04:07
tags:
- Spring
---
Spring Bean定义：
bean是一个被实例化，组装并通过Spring IOC容器进行管理的对象
## bean的属性
属性 | 描述
---|---
class | 用来指定bean的bean类
id/name | bean的唯一标识符
scope | bean的作用域
constructor-arg | 使用构造函数进行依赖注入
properties | 类成员变量的依赖注入
lazy-init | 延迟加载，表示bean在使用时才会进行实例化，而不是IOC容器创建时就实例化
init-method | 指定bean被实例化后，调用的回调函数
destroy-method | 指定bean被销毁时，调用的回调函数

<!-- more -->

### bean的作用域
**定义一个bean时，必须声明bean的作作用域。**如果强调每次调用都要重新生成一个bean实例，那么bean的作用域属性scope值为prototype；如果bean在系统中是单例的，那么scope属性值为singleton。

Spring框架支持的作用域：

作用域 | 描述
---|---
singleton | bean在容器中只有一个单一实例【默认】；Spring容器会在创建时就返回一个该对象的实例。
prototype | 每次使用bean都会实例化一个新对象；

```xml
<!-- bean时单例的，而且在IOC容器创建时就会实例化一个bean -->
<bean id="**" class="com.xiaopeng.ioc.backup.**" scope="singleton"/>
	
<!-- bean是多例的，调用时实时创建 -->
<bean id="**" class="com.xiaopeng.ioc.backup.**" scope="prototype"/>
```

### bean的生命周期
为了定义和拆卸一个bean，需要声明bean的init-method/destroy-method
- init-method：在实例化bean时，立即调用该方法
- destroy-method：bean对象从容器中移除之后，调用该方法

**Bean：**
```java
package com.xiaopeng.ioc.backup;

public class Boy {

    private String message;

    public Boy(String message) {
        this.message = message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public void init(){
        System.out.println("Boy say:" + message);
    }

    public void destroy() {
        System.out.println("bean will be destroyed");
    }
}

```

applicationContext.xml配置：
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

	<!-- 配置Boy-bean -->
	<bean id="boy" class="com.xiaopeng.ioc.backup.Boy" init-method="init" destroy-method="destroy">
		<constructor-arg index="0" ref="message"/> <!-- 注入message -->
		<!--<property name="message" value="Hello World"/>-->
	</bean>

	<bean id="message" class="java.lang.String">
		<constructor-arg index="0" value="Hello World"/>
	</bean>

</beans>      
  
```

测试init-method/destroy-method:
```java
package com.xiaopeng.ioc.backup;

import org.springframework.context.support.AbstractApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

    public static void main(String[] args) {
        //得到IOC容器
        AbstractApplicationContext ac = new ClassPathXmlApplicationContext("com/xiaopeng/ioc/backup/applicationContext.xml");

        System.out.println("ac: " + ac.getClass());

        //得到UserAction对象
        Boy boy = (Boy) ac.getBean("boy");

        //确保调用destroy-method
        ac.registerShutdownHook();
    }
}

```

输出：
```
构造函数被调用
Boy say:Hello World
ac: class org.springframework.context.support.ClassPathXmlApplicationContext
bean will be destroyed
```
可以发现：在默认情况下（scope=“singleton”），单例的对象先被创建，然后创建IOC容器，并且init-method(init())和destroy-method(destroy())都被正确调用。

### Spring依赖注入
java对象内部通常会有其他对象（基本类型，引用类型），Spring中，如何给对象的成员变量赋值呢？
1. 通过构造函数
2. 通过set方法进行赋值
3. 注解

#### 通过构造函数进行依赖注入
上面在讲解初始化方法时，Boy类的实例化就是通过构造函数进行依赖注入message的：
```xml
<bean id="boy" class="com.xiaopeng.ioc.backup.Boy" init-method="init" destroy-method="destroy">
	<constructor-arg index="0" ref="message"/> <!-- 注入message -->
</bean>

<bean id="message" class="java.lang.String">
	<constructor-arg index="0" value="Hello World"/>
</bean>
```
<constructor-arg/>标签中的属性说明：

写法 | 描述
---|---
constructor-arg index="0" ref="message" | Boy类的构造函数的第一个参数注入message对应的bean对象
constructor-arg index="0" value="Hello World" | Boy类的构造函数的第一个参数注入字符串Hello World
constructor-arg type="java.lang.String" ref="message" | Boy类的构造函数中String类型形参注入message对应的bean对象
constructor-arg type="java.lang.String" value="Hello World" | Boy类的构造函数中String类型形参注入字符串Hello World

### 使用注解简化IOC配置
通过注解的方式，将对象加入IOC容器，可以和xml配置的方式同时使用，达到简化配置的作用。

注解 | 功能
---|---
@Component | 把一个对象加入Spring-IOC容器
@Resource | 属性注入

```java
package com.xiaopeng.annotation;

import org.springframework.stereotype.Component;

@Component  /* 将UserDao加入IOC容器 */
public class UserDao implements  IUserDao {

    @Override
    public void save() {
        System.out.println("save()");
    }
}

```
在appliacationontext.xml中开启注解扫描：
```xml
<!-- 开启注解扫描 -->
<context:component-scan base-package="com.xiaopeng.annotation"/>

```












