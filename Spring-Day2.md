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

### 1.4.3 扩展方式注入

我们可以使用C命名空间（构造器注入）或者P命名空间（Set注入）进行注入

官方解释：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="john-classic" class="com.example.Person">
        <property name="name" value="John Doe"/>
        <property name="spouse" ref="jane"/>
    </bean>

    <bean name="john-modern"
        class="com.example.Person"
        p:name="John Doe"
        p:spouse-ref="jane"/>

    <bean name="jane" class="com.example.Person">
        <property name="name" value="Jane Doe"/>
    </bean>
</beans>


<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="beanTwo" class="x.y.ThingTwo"/>
    <bean id="beanThree" class="x.y.ThingThree"/>

    <!-- traditional declaration with optional argument names -->
    <bean id="beanOne" class="x.y.ThingOne">
        <constructor-arg name="thingTwo" ref="beanTwo"/>
        <constructor-arg name="thingThree" ref="beanThree"/>
        <constructor-arg name="email" value="something@somewhere.com"/>
    </bean>

    <!-- c-namespace declaration with argument names -->
    <bean id="beanOne" class="x.y.ThingOne" c:thingTwo-ref="beanTwo"
        c:thingThree-ref="beanThree" c:email="something@somewhere.com"/>

</beans>
```

+ 测试

  ```xml
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:p="http://www.springframework.org/schema/p"
         xmlns:c="http://www.springframework.org/schema/c"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
  <!--P命名空间注入-->
      <bean id="user" class="com.wzw.pojo.User" p:name="王智威" p:age="18"/>
      <bean id="user2" class="com.wzw.pojo.User" c:_0="王智威" c:age="18"/>
  </beans>
  ```

  注意点：P或者C命名空间不能直接使用 需要导入xml约束！

  ```xml
         xmlns:p="http://www.springframework.org/schema/p"
         xmlns:c="http://www.springframework.org/schema/c"
  ```

  ## 1.5、bean的作用域

  ![image-20220214145045217](https://raw.githubusercontent.com/lengyue-github/PicGo/master/images202202141450277.png)

  1.  单例模式（Spring默认机制）

     ```xml
         <bean id="user2" class="com.wzw.pojo.User" c:_0="王智威" c:age="18" scope="singleton"/>
     ```

  2. 原型模式（每次从容器中get时均会产生新的对象）

     ```xml
     <bean id="user2" class="com.wzw.pojo.User" c:_0="王智威" c:age="18" scope="prototype"/>
     ```

## 1.6、自动装配

+ 自动装配是Spring满足bean依赖的一种方式！
+ Spring会在上下文中自动寻找，并自动给bean装配属性。

在Spring中有三种装配方式

1. 在xml中显示的配置
2. 在java中显示的配置
3. 隐式的自动装配

### 1.6.1、自动装配byName

自动在容器上下文中查找，和自己对象set方法后面的值对应得beanid

```xml
    <bean id="people" class="com.wzw.pojo.People" autowire="byName">
        <property name="name" value="王智威"/>
    </bean>
    <bean id="cat" class="com.wzw.pojo.Cat"/>
    <bean id="dog" class="com.wzw.pojo.Dog"/>
```

### 1.6.1、自动装配byType

自动在容器上下文中查找，和自己对象属性类型相同的Bean

```xml
    <bean id="people" class="com.wzw.pojo.People" autowire="byType">
        <property name="name" value="王智威"/>
    </bean>
    <bean id="cat" class="com.wzw.pojo.Cat"/>
    <bean id="dog" class="com.wzw.pojo.Dog"/>
```

小结：

+ byName的时候，保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致

+ byType的时候，保证所有bean的class唯一，并且这个bean和自动注入的对象属性类型相同

## 1.7、使用注解自动装配

使用注解须知：

1. 导入约束

2. 配置注解的支持<span><context:annotation-config/></span>

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:annotation-config/>
   
   </beans>
   ```

+ @Autowired

  + 直接在属性上面使用即可！也可在set方式上使用

  + 使用Autowired我们可以不用写set方法了，前提是自动装配的属性在IOC容器中存在，且名字符合byName!

  + 科普

    ```xml
    @Nullable 字段标记了这个注解，说明这个字段可以为null
    如果显示的定义Autowired(required == false) 说明这个字段可以为null
    如果@Autowired自动装配的环境较为复杂，自动装配无法通过一个注解完成的时候，我们可以使用@Qualifier(value="dog")指定beanid
    ```

+ @Resource
  + 以上两种装配方式的集合体，通过name属性指定@Resource(name="dog")

小结：

@Autowired与@Resource的区别

+ 都是用来自动装配的，都可以放在属性字段上
+ @Autowired通过byType的方式实现，并且必须要求这个对象存在
+ @Resource默认通过byName方式实现，如果找不到名字，则通过ByType实现，如果两个都找不到的情况下，就报错！
+ 执行顺序不同：@Autowired通过byType方式实现 @Resource默认通过byName方式实现。

## 1.8、使用注解开发

+ bean

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd">
      <!--指定要扫描的包，这个包下的注解生效-->
      <context:component-scan base-package="com.wzw.pojo"/>
      <context:annotation-config/>
  </beans>
  ```

+ 属性如何注入

  ```java
  package com.wzw.pojo;
  
  import org.springframework.beans.factory.annotation.Value;
  import org.springframework.stereotype.Component;
  
  //@Component 组件
  @Component
  public class User {
      @Value("wangzhiwei")
      private String name;
      @Value("18")
      private int age;
  
      @Override
      public String toString() {
          return "User{" +
                  "name='" + name + '\'' +
                  ", age=" + age +
                  '}';
      }
  
      public int getAge() {
          return age;
      }
  
      public void setAge(int age) {
          this.age = age;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public User() {
      }
  
      public User(String name, int age) {
          this.name = name;
          this.age = age;
      }
  }
  ```

+ 衍生的注解

  @Component有几个衍生注解，我们在web开发中，会按照mvc三层架构分层！

  + dao【@Repository】
  + service【@Service】
  + controller【@Controller】

  这四个注解的功能是一样的，都是代表将某个类注册到Spring中，装配Bean

+ 自动装配

  @Nullable 字段标记了这个注解，说明这个字段可以为null
  如果显示的定义Autowired(required == false) 说明这个字段可以为null
  如果@Autowired自动装配的环境较为复杂，自动装配无法通过一个注解完成的时候，我们可以使用@Qualifier(value="dog")指定beanid

+ 作用域

  @Scope("")

+ 小结

  xml与注解：

  + xml更加万能，适合于任何场合！维护简单方便
  + 注解 不是自己类使用不了，维护相对复杂

  xml与注解最佳实践：

  + xml用来管理Bean

  + 注解只负责完成属性的注入

  + 我们在使用的过程中，只需要注意一个问题：必须让注解生效，就需要开启注解的支持。

    ```xml
        <context:component-scan base-package="com.wzw"/>
        <context:annotation-config/>
    ```

## 1.9、使用Java的方式配置Spring

不需要Spring的xml配置，交给JavaConfig

```xml
package com.wzw.config;

import com.wzw.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

//这个也会被Spring托管 因为原来就是@Component
//@Configuration 代表配置类 代替beans.xml
@Configuration
@ComponentScan("com.wzw.pojo") //代表扫描注册包
@Import(KuangConfig2.class)//导入多个配置文件
public class KuangConfig {
    //    注册一个bean 相当于bean标签
//    方法名  作为bean标签中的id
//    返回值 相当于class
    @Bean
    public User getUser() {
        return new User();//注入到bean对象
    }
}
```

