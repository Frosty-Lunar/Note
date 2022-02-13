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

总结：在配置文件加载的时候，容器中管理的对象就已经初始化了。

## 1.3、Spring配置

+ alias（起别名）原名和别名都可以使用

```xml
<alias name="user" alias="user2"/>
```

+ bean 

```xm
    <!--    id : bean的唯一标识符，相当于对象名
            class ： bean对象所对应的全限定名：包名+类型
            name ： 也可作为别名使用，name可以同时取多个别名
    -->
    <bean id="user" class="com.wzw.pojo.User" name="user3 user4">
        <constructor-arg name="name" value="王智威"/>
    </bean>
```

+ import 一般用于团队开发中 利用import将多个配置文件合并到一个，使用的时候采用核心配置文件就好

```xml
<import resource="beans.xml"/>
```

## 1.4、依赖注入

### 1.4.1 构造器注入

上面已经说过了

### 1.4.2 Set方式注入【重点】

+ 依赖注入：set注入！
  + 依赖：bean对象的创建依赖于容器！
  + 注入：bean对象中所有属性，由容器来创建！

【环境搭建】

1. Student.java 真实测试对象

```java
package com.wzw.pojo;

import java.util.*;

public class Student {
    private String name;
    private Address address;
    private String[] books;
    private List<String> hobbys;
    private Map<String, String> card;
    private Set<String> games;
    private String wifer;
    private Properties info;

    public Student() {
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", address=" + address +
                ", books=" + Arrays.toString(books) +
                ", hobbys=" + hobbys +
                ", card=" + card +
                ", games=" + games +
                ", wifer='" + wifer + '\'' +
                ", info=" + info +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    public String[] getBooks() {
        return books;
    }

    public void setBooks(String[] books) {
        this.books = books;
    }

    public List<String> getHobbys() {
        return hobbys;
    }

    public void setHobbys(List<String> hobbys) {
        this.hobbys = hobbys;
    }

    public Map<String, String> getCard() {
        return card;
    }

    public void setCard(Map<String, String> card) {
        this.card = card;
    }

    public Set<String> getGames() {
        return games;
    }

    public void setGames(Set<String> games) {
        this.games = games;
    }

    public String getWifer() {
        return wifer;
    }

    public void setWifer(String wifer) {
        this.wifer = wifer;
    }

    public Properties getInfo() {
        return info;
    }

    public void setInfo(Properties info) {
        this.info = info;
    }
}
```

2. Address.java 复杂测试对象

```java
package com.wzw.pojo;

public class Address {
    private String address;

    public Address(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Address{" +
                "address='" + address + '\'' +
                '}';
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}
```

3. beans.xml

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
    <!--    id : bean的唯一标识符，相当于对象名
            class ： bean对象所对应的全限定名：包名+类型
            name ： 也可作为别名使用，name可以同时取多个别名
    -->
    <bean id="user" class="com.wzw.pojo.User" name="user3 user4">
        <constructor-arg name="name" value="王智威"/>
    </bean>
    <alias name="user" alias="user2"/>

    <bean id="student" class="com.wzw.pojo.Student">
        <property name="name" value="王智威"/>
    </bean>
</beans>
```

4. 测试类Test

```java
    @Test
    void testStudnt() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Student student = (Student) context.getBean("student");
        System.out.println(student.getName());
    }
```

5. 完善注入信息

```xml
<bean id="address" class="com.wzw.pojo.Address">
        <property name="address" value="河南工业大学"/>
    </bean>
    <bean id="student" class="com.wzw.pojo.Student">
        <!-- 第一种注入方式：普通值注入 value-->
        <property name="name" value="王智威"/>
        <!-- 第二种注入方式：Bean注入 ref-->
        <property name="address" ref="address"/>
        <!-- 第三种注入方式：数组注入 array-->
        <property name="books">
            <array>
                <value>宫楼梦</value>
                <value>西游记</value>
                <value>三国演义</value>
            </array>
        </property>
        <!-- 第四种注入方式：List注入 list-->
        <property name="hobbys">
            <list>
                <value>听歌</value>
                <value>看电影</value>
                <value>打游戏</value>
                <value>阅读</value>
            </list>
        </property>
        <!-- 第五种注入方式：Map注入 map-->
        <property name="card">
            <map>
                <entry key="身份证" value="410426388874362512"/>
                <entry key="银行卡" value="4673826388874362512"/>
            </map>
        </property>
        <!-- 第六种注入方式：Set注入 set-->
        <property name="games">
            <set>
                <value>LOL</value>
                <value>王者荣耀</value>
                <value>DNF</value>
            </set>
        </property>
        <!-- 第七种注入方式：Null注入-->
        <property name="wifer">
            <null/>
        </property>
        <!-- 第八种注入方式：Properties注入-->
        <property name="info">
            <props>
                <prop key="学号">201916040419</prop>
                <prop key="姓名">王智威</prop>
                <prop key="性别">男</prop>
            </props>
        </property>
    </bean>
```

