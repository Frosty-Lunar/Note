# SpringDay3

## 1、代理模式

为什么学习代理模式？这是SpringAOP的底层【SpringAOP和SpringMVC】

代理模式的分类：

+ 静态代理
+ 动态代理

### 1.1、静态代理

角色分析：

+ 抽象角色：一般会使用接口或者抽象类来解决
+ 真实角色：被代理的角色
+ 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作
+ 客户：访问代理对象的人！

代理模式的好处：

+ 可以使真实角色的操作更加纯粹！不用去关注一些公共的业务
+ 公共业务交给代理角色！实现了业务的分工！
+ 公共业务发生扩展的时候，方便集中管理！

缺点：

+ 一个真实角色会产生一个代理角色，代码量会翻倍-开发效率会变低

代码步骤：

+ 接口

  ```java
  package com.wzw.demo01;
  
  public interface Rent {
      public void rent();
  }
  ```

+ 真实角色

  ```java
  package com.wzw.demo01;
  
  public class Host implements Rent{
      @Override
      public void rent() {
          System.out.println("房东要出租房子！");
      }
  }
  ```

+ 代理角色

  ```java
  package com.wzw.demo01;
  
  public class Proxy implements Rent {
      private Host host;
  
      public Proxy() {
      }
  
      public Proxy(Host host) {
          this.host = host;
      }
  
      @Override
      public void rent() {
          seeHouse();
          host.rent();
      }
  
      public void seeHouse() {
          System.out.println("中介带你看房！");
      }
  }
  ```

+ 客户端访问代理角色

  ```java
  package com.wzw.demo01;
  
  public class Client {
      public static void main(String[] args) {
          Host host = new Host();
          Proxy proxy = new Proxy(host);
          proxy.rent();
      }
  }
  ```

### 1.2、加深理解

忽略

## 2、动态代理

+ 动态代理和静态代理角色一样
+ 动态代理的代理类是动态生成的，不是我们直接写的
+ 动态代理分为两大类 ：基于接口的动态代理、基于类的动态代理
  + 基于接口的动态代理 JDK动态代理
  + 基于类的动态代理 cglib
  + java字节码实现：javasist

需要了解两个类：Proxy：代理、InvocationHandler：调用处理程序

+ 可以使真实角色的操作更加纯粹！不用去关注一些公共的业务
+ 公共业务交给代理角色！实现了业务的分工！
+ 公共业务发生扩展的时候，方便集中管理！
+ 一个动态代理类代理的是一个接口，一般就是对应得一类业务
+ 一个动态代理类可以代理多个类，只要是实现了同一个接口



ProxyInvocationHandler类：

```java
package com.wzw.demo01;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * 自动生成代理
 *
 * @date 2022/02/16
 */
public class ProxyInvocationHandler implements InvocationHandler {
    private Object target;


    public void setRent(Object target) {
        this.target = target;
    }

    /**
     * 处理代理实例，并返回结果
     *
     * @param proxy  代理
     * @param method 方法
     * @param args   arg游戏
     * @return {@link Object}
     * @throws Throwable throwable
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object invoke = method.invoke(target, args);//动态代理的本质，利用反射机制实现
        return invoke;
    }

    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }
}
```

测试类Client

```java
package com.wzw.demo01;

public class Client {
    public static void main(String[] args) {
        //真实角色
        Host host = new Host();
        //代理角色
        ProxyInvocationHandler proxyInvocationHandler = new ProxyInvocationHandler();
        //通过调用程序处理角色处理我们要调用的接口对象
        proxyInvocationHandler.setRent(host);
        Rent proxy = (Rent) proxyInvocationHandler.getProxy();//这里的proxy就是动态生成的
        proxy.rent();
    }
}
```

## 3、使用Spring方式实现AOP

【重点】导入依赖包

```xml
        <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.8</version>
        </dependency>
```

UserService.java

```java
package com.wzw.service;

public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void select();
}
```

UserServiceImpl.java

```java
package com.wzw.service;

import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;
@Repository("userService")
@Component
public class UserServiceImpl implements UserService {
    @Override
    public void add() {
        System.out.println("增加了一个用户！");
    }

    @Override
    public void delete() {
        System.out.println("删除了一个用户！");
    }

    @Override
    public void update() {
        System.out.println("更新了一个用户！");
    }

    @Override
    public void select() {
        System.out.println("查询了一个用户！");
    }
}
```

AfterLog.java

```java
package com.wzw.log;

import org.springframework.aop.AfterReturningAdvice;
import org.springframework.stereotype.Repository;

import java.lang.reflect.Method;
@Repository
public class AfterLog implements AfterReturningAdvice {
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了" + method.getName() + "方法，返回结果为：" + returnValue);
    }
}
```

BeforeLog.java

```java
package com.wzw.log;

import org.springframework.aop.MethodBeforeAdvice;
import org.springframework.stereotype.Repository;

import java.lang.reflect.Method;
@Repository
public class BeforeLog implements MethodBeforeAdvice {
    /**
     * 之前
     *
     * @param method 要执行的目标对象的方法
     * @param args   参数
     * @param target 目标对象
     * @throws Throwable throwable
     */
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName() + "的" + method.getName() + "被执行了");
    }
}
```

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--指定要扫描的包，这个包下的注解生效-->
    <!--    注册Bean-->
    <context:component-scan base-package="com.wzw"/>
    <context:annotation-config/>
    <!--    配置aop：需要导入aop约束-->
    <!--    方式一：使用原生Spring API接口-->
    <aop:config>
        <!--  切入点expression：表达式 execution(要执行的位置！)-->
        <aop:pointcut id="pointcut" expression="execution(* com.wzw.service.UserServiceImpl.*(..))"/>
        <!-- 执行环绕增加！-->
        <aop:advisor advice-ref="beforeLog" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

方式一：使用Spring的API接口

