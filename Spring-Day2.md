# Spring-Day2

## 1.1、Hello Spring（XML配置文件）

+ beans.xml配置文件中创建对象

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--使用Spring创建对象，在Spring中称为Bean
        id = 变量名
        class = 权限定类名 创建的对象
        核心调用set方法
    -->
    <bean id="userDaoImpl" class="com.wzw.dao.UserDaoImpl"/>
    <bean id="userServiceImpl" class="com.wzw.service.UserServiceImpl">
        <!--        ref:引用Spring容器中创建好的对象-->
        <property name="userDao" ref="userDaoImpl"/>
    </bean>
</beans>
```

+ Test测试

```java
    @Test
    void testGetUser() {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        UserService userService = (UserService) context.getBean("userServiceImpl");
        userService.getUser();
    }
```

通过  **ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml")**读取配置文件。

## 1.2、IOC（控制反转）创建对象的方式

+ 使用无参构造方法创建对象。如果直接采用采用有参构造方法创建对象则报错！

```java
    public User() {
        System.out.println("使用无参构造方式创建对象！");
    }
```

```xml
    <bean id="user" class="com.wzw.pojo.User">
        <property name="name" value="王智威"/>
    </bean>
```

```java
    @Test
    void testGetUser() {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        User user = (User) context.getBean("user");
    }
```

+ 使用有参构造方法创建对象

  ```java
      public User(String name) {
          this.name = name;
      }
  ```

  ```java
      @Test
      void testGetUser() {
          ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
          User user = (User) context.getBean("user");
          user.show();
      }
  ```

  

  + 通过下标实现

  ```xml
      <bean id="user" class="com.wzw.pojo.User">
          <constructor-arg index="0" value="王智威"/>
      </bean>
  ```

  + 通过参数的类型传递

  基本类型可以直接用，引用类型需要全限定类名

  <span style="color:red">不建议使用（例如多个String）</span >

  ```xml
      <bean id="user" class="com.wzw.pojo.User">
          <constructor-arg type="java.lang.String" value="王智威"/>
      </bean>
  ```

  + 直接通过参数名设置

  ```xml
      <bean id="user" class="com.wzw.pojo.User">
          <constructor-arg name="name" value="王智威"/>
      </bean>
  ```

Spring中无论调用不，bean中所有的对象都被创建了，通过getBean("")方法引用时，引用的是同一对象。

