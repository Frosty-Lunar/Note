# Spring-Day1

## 1.1、简介

+ Spring：春天========》给软件行业带来春天
+ 2002年 推出Spring框架的雏形：interface21框架
+ Spring框架以interface21为基础，经过重新设计，不断丰富内涵，于2004年3月24日发布了1.0正式版本。
+ **Rod Johnson**，Spring Framework的创始人。
+ Spring框架理念：使现有的东西更加容易使用，本身是一个大杂烩，整合了现有的技术框架。
+ SSH：Struct2 + Spring + Hibernate
+ SSM：SpringMVC + Spring + Mybatis

官网：https://spring.io/projects/spring-framework#overview

## 1.2、优点

+ Spring是一个开源的免费的框架（容器）！
+ Spring是一个轻量级的、非入侵式的框架！
+ 控制反转（IOC） ， 面向切面编程（AOP）！
+ 支持事务的处理，对框架整合的支持！

## 1.3、组成

![image-20220209224038367](https://gitee.com/lengyue_wzw/PicGo_img/raw/master/images/202202121301856.png)

+ Spring Boot
  + 一个快速开发的脚手架。
  + 基于Spring Boot可以快速的开发单个微服务
  + 约定大于配置
+ Spring Cloud
  + Spring Cloud是基于Spring Boot实现的

## IOC理论推导

1. UserDao接口

```java
package com.wzw.dao;

public interface UserDao {
    void getUser();
}
```

2. UserDaoImpl实现类

```java
package com.wzw.dao;

public class UserDaoImpl implements UserDao{
    @Override
    public void getUser() {
        System.out.println("默认获取用户的数据！");
    }
}
```

3. UserService接口

```java
package com.wzw.service;

public interface UserService {
    void getUser();
}
```

4. UserServiceImpl实现类

```java
package com.wzw.service;

import com.wzw.dao.UserDao;
import com.wzw.dao.UserDaoImpl;

public class UserServiceImpl implements UserService {
    private UserDao userDao = new UserDaoImpl();

    @Override
    public void getUser() {
        userDao.getUser();
    }
}
```

5. Test测试类

```java
package com.wzw.service;

import org.junit.jupiter.api.Test;

class UserServiceTest {
    @Test
    void testGetUser() {
        //调用业务层
        UserService userService = new UserServiceImpl();
        userService.getUser();
    }
}
```

以上这种写法，代码量十分大，修改一次的成本非常高！

使用Set接口实现，发生革命性的变化！

```java
    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
```

+ 之前的程序是主动创建对象！控制权在程序员手中！
+ 使用Set注入之后，程序不再具有主动性，而是变成了被动的接受对象！

这种思想，从本质上解决了问题，我们程序员不在关注于对象的创建，系统的耦合性大大降低，可以专注于业务的实现上！这是IOC控制反转的原型！

控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IOC容器，其实现方法是依赖注入。

Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中。
