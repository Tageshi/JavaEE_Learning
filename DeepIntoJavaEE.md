# DeepIntoJavaEE

2022.09.14 - 学习part1-3，IOC控制反转推理。

2022.09.15 - 学习part4-5，实操HelloSpring，配置applicationContext.xml。

2022.09.27 - 学习part6，实操Spring-03-ioc2，熟悉IOC各种配置方法。

2022.09.28 - 学习part7



## 1.Spring部分

### 1.1 优点

- Spring是一个免费的开源框架

- Spring是一个轻量级的、非入侵式的框架

- 控制反转（IoC容器）、面向切面编程（AOP模块）

- 支持事务的处理，支持框架的整合

- 总结：Spring是一个轻量级的控制反转和面向切面编程的框架。

### 1.2 组成![img](https://api2.mubu.com/v3/document_image/0c2b4dce-7c2d-43b8-87c8-8ff624010cfa-12641848.jpg)

- Spring AOP（面向切面编程）
- Spring ORM（对象关系映射）
- Spring DAO
- Spring Web
- Spring Context
- Spring Web MVC
- Spring Core

### 1.3 拓展

- Spring Boot 构建

  - 基于Spring Boot 可以快速开发单个微服务。

  - 约定大于配置。

- Spring Cloud 协调

  - 基于Spring Boot 实现。

- Spring Cloud Data Flow 连接

### 1.4 IOC（控制反转）理论推导

- 将原本需要每次都new的对象（有多个选择的类）改用set方法创建，于是类可以交由用户自行选择而不用生产者根据需求每次修改。
- 程序控制权从生产者手中转向消费者手中，故称控制反转。
- 降低了代码的耦合性（松耦合）。

```java
private UserDao userDao;

//使用set方法实现动态的对象选择
public void setUserDao(UserDao userdao){
    this.userdao=userdao;
}
```

### 1.5 IOC本质

- 控制反转是一种通过描述（**XML**或**注解**或**Java**）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection，DI）。

### 1.6 配置元数据

- XML配置方法

```xml
<!--配置bean.xml（正式应为ApplicationContext.xml）-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--使用Spring来创建对象，对象被称为Bean-->
    <bean id="hello" class="com.fuzj.pojo.Hello">
        <property name="name" value="Springbulla"/>
    </bean>
    <!--类比JavaSE的语法：id相当于变量名，class相当于对象的类型，property就是对象的属性（name是属性名，value是属性值）-->
    <!--id是bean对象的唯一标识符-->
</beans>

```

```java
//MyTest.java
import com.fuzj.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        //获取Spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //使用上下文对象context的getBean方法调用配置好的bean
        Hello hello= (Hello) context.getBean("hello");
        //返回的是Hello.java中hello对象唯一属性name的值（bean.xml中name属性的value），输出"Springbulla"
        System.out.println(hello.getName());
    }
}
```

```java
//Hello.java
package com.fuzj.pojo;

public class Hello {
    private String name;
    public String getName(){return name;}
    public void setName(String name){
        this.name=name;
    }
    public void show(){
        System.out.println("Hello"+name);
    }
}

```



### 1.7 IOC创建对象的方式

- Spring在运行测试类是会初始化applicationContext.xml容器中管理的所有bean实例。
- 一般使用无参构造。applicationContext.xml中使用property元素为属性赋值。
- 如果使用有参构造，则构造函数中必须对参数进行赋值

```java
//User.java
package com.fzj.pojo;
public class User {
    private String name;
    //有参构造
    public User(String name){ 
        this.name=name;
    }
    public String getName(){
        return name;
    }
    public void setName(String name){
        this.name=name;
    }
    public void show(){
        System.out.println("name: "+name);
    }
}


```

且applicationContext.xml文件中bean实例采用construction-arg元素进行配置。index对应各项参数的顺序（index从0开始），是为下标赋值。

```xml
<bean id="user" class="com.fzj.pojo.User">
        <constructor-arg name="name" value="fuzj"/>
</bean>
```

type属性对应各项参数的类型，但多个参数类型一致时不建议使用此方法。

```xml
<bean id="user" class="com.fzj.pojo.User">
    <constructor-arg type="java.lang.String" value="fuzjstring"/>
</bean>
```

constructor-arg的属性中name属性对应User.java中的各项参数（本例中只有private String name），是为直接赋值。

```xml
<bean id="user" class="com.fzj.pojo.User">
     <constructor-arg name="name" value="fzj"/>
</bean>
```



### 1.8 Spring配置

- 别名（alias）：测试类中getBean("uasdhiuahdi")也会链接到user对象。

  ```xml
  <alias name="user" alias="uasdhiuahdi"/>
  ```

  bean的name属性也是别名，可以取多个别名。

  ```xml
  <bean id="user2" class="com.fzj.pojo.User2" name="userNew"/>
  ```

  

- Bean的配置

- Import
