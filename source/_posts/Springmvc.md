---
title: Springmvc
date: 2017-06-24 11:47:31
tags:
- Spring
- Springmvc
---

使用Struct和Spring开发web项目的问题：
1. 每次请求都要创建action，（scope="prototype"）
2. action类一成不变间接或直接继承ActionSupport，不能拓展
3. action类中，接收参数要用实例变量和对应的set方法（用作依赖注入）
4. struts.xml配置文件方式固定

springmvc属于spring框架的后续产品，是表现层的产品