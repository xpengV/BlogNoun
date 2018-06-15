---
title: Java反射
date: 2017-03-21 19:14:15
tags: 反射
---

当应用程序主动使用某个类时，系统会通过加载、连接、初始化3个步骤来对该类进行初始化。类的加载是指将类的class文件读入内存，并为之创建一个java.lang.Class对象

## 类初始化
<!-- more -->

类的初始化主要是对静态的Field进行初始化
1. 声明静态Field时指定初始值
2. 使用静态代码块为静态变量赋值

## 类初始化的时机
1. 创建类的实例，使用new关键字来创建实例
2. 调用某一个类的静态方法
3. 访问类或者接口的静态变量
4. 使用 **反射** 方式来强制创建类对应的Class对象， 语法是:Class.forName("com.xiaopeng.domain.Person");

# 反射
在程序运行状态时，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

获取类Class对象的三种方法：
1. Class.forName(String clazzName) 传入类的全路径（常用）
2. Person.class 调用类的class属性
3. p.getClass()  调用对象的getClass()方法

## 从Class对象中获取信息
Class类提供了大量的实例方法来获取类的属性，常用的如下：
假设有以下Person类：
```java
package com.xiaopeng.domain;

public class Person {

    private int age;
    private String name;

    public Person() {

    }

    private Person(int age,String name){
        this.age = age;
        this.name = name;
    }

    private void sayHello() {
        System.out.println("hello,world!");
    }

    private void jump(int times){
        while(times > 0) {
            System.out.println("jump");
            times--;
        }
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }
}


```
#### 反射获取并调用类的构造函数
**使用反射获取类的构造函数，并调用newInstance()获得类的实例**

```java
package com.xiaopeng.reflect;

import java.lang.reflect.Constructor;

public class App {

    public static void main(String[] args){
        //获取Class对象
        try {
            Class clazz = Class.forName("com.xiaopeng.domain.Person");
            //获取类所有的公有构造函数
            Constructor[] constructors = clazz.getConstructors();

            //获取类所有的构造函数
            Constructor[] constructors1 = clazz.getDeclaredConstructors();

            //获取包含某种参数类型的构造函数
            Constructor constructor = clazz.getDeclaredConstructor(int.class,String.class);

            System.out.println(constructor);

            for(Constructor c : constructors)
            {
                //System.out.println(c.getName());
            }

            //-----------------------------------

            //反射调用构造函数实例化
            Constructor con = clazz.getDeclaredConstructor(int.class,String.class);
            con.setAccessible(true); //构造函数是private，需要修改为允许访问
            Object o = con.newInstance(24,"Jack");

            System.out.println(o);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```
输出：
[获取并调用构造函数](/img/Java反射/Java反射-1.png)

#### 反射获取并调用类的方法
**获取类的方法，并使用invoke()调用方法**

```java
package com.xiaopeng.reflect;

import com.xiaopeng.domain.Person;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class MyMethod {

    public static void main(String[] args){
        try {
            Class clazz = Class.forName("com.xiaopeng.domain.Person");
            //获取类的所有public修饰的方法
            Method[] methods = clazz.getMethods();

            //获取类的所有成员方法
            Method[] methods1 = clazz.getDeclaredMethods();

            //获取含有指定方法名、参数的方法
            Method method = clazz.getDeclaredMethod("jump",int.class);

            System.out.println(method);

            method.setAccessible(true); //修改访问权限
            Person p = (Person) clazz.getConstructor().newInstance();
            
            //使用invoke调用jump方法
            Object returnVal = method.invoke(p,3);


            for(Method m : methods1)
            {
                //System.out.println(m.getName());
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
    }
}

```
输出：
[获取并调用成员方法](/img/Java反射/Java反射-2.png)

注：获取并调用类的main方法：

```java
Method mainMethod = clazz.getMethod("main",String[].class);
/**
 * 因为main方法是静态方法，所以对象的引用传入null即可
 **/
mainMethod.invoke(null,(Object)new String[]{"q","w","e"})
```

#### 获取类的成员变量
**获取类的成员变量，并实例化类**
```java
package com.xiaopeng.reflect;

import com.xiaopeng.domain.Person;

import java.lang.reflect.Field;

public class MyField {

    public static void main(String[] args)
    {
        try {
            Class clazz = Class.forName("com.xiaopeng.domain.Person");

            //获取类的所有公有成员变量
            Field[] fields = clazz.getFields();

            //获取非公有成员变量
            Field[] fields1 = clazz.getDeclaredFields();

            for(Field f : fields1)
            {
                System.out.println(f.getName());
            }

            Object o = clazz.getConstructor().newInstance();
            //获取属性并赋值
            Field f_age = clazz.getDeclaredField("age");
            f_age.setAccessible(true);
            f_age.set(o,24);

            Field f_name = clazz.getDeclaredField("name");
            f_name.setAccessible(true);
            f_name.set(o,"Jack");

            System.out.println((Person)o);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```
**Spring框架就是通过这种将Field值以及依赖对象等放在配置文件进行管理，进行IOC处理，实现较好的解耦性！**

输出：
[获取并调用成员方法](/img/Java反射/Java反射-3.png)

Java反射还可以获取类上的注解信息、类实现的接口信息

# 使用反射越过泛型检查
从jdk1.5之后，java引入了泛型的新特性，泛型用在编译时，编译过后泛型会被擦除。而反射是在应用程序运行期间体现的，所以可以使用反射技术越过泛型检查。
```java
public static void main(String[] args){

        ArrayList<String> arrayList = new ArrayList<>();

        arrayList.add("aaa");
        arrayList.add("bbb");
        //arrayList.add(100);   //编译时报错

        Class clazz = arrayList.getClass();
        try {
            Method m = clazz.getMethod("add",Object.class);

            m.invoke(arrayList,100);

        } catch (Exception e) {
            e.printStackTrace();
        }

        for(Object o : arrayList){
            System.out.println(o);
        }
    }
```

**反射是框架设计的灵魂，可以使用反射技术开发更多基础的、适应性广、灵活性强的代码。**
