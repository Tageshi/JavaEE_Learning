# 0.DeepIntoJavaEE

2022.09.14 - 学习part1-3，IOC控制反转推理。

2022.09.15 - 学习part4-5，实操HelloSpring，配置applicationContext.xml。

2022.09.27 - 学习part6，实操Spring-03-ioc2，熟悉IOC各种配置方法。

2022.09.28 - 学习part7

2022.10.18 - 学习SpringMVC-part3

2022.10.20 - 学习SpringMVC-part4-6

2022.10.23 - 学习SpringMVC-part7-10



# 1.Spring

## 1.1 优点

- Spring是一个免费的开源框架

- Spring是一个轻量级的、非入侵式的框架

- 控制反转（IoC容器）、面向切面编程（AOP模块）

- 支持事务的处理，支持框架的整合

- 总结：Spring是一个轻量级的控制反转和面向切面编程的框架。

## 1.2 组成![img](https://api2.mubu.com/v3/document_image/0c2b4dce-7c2d-43b8-87c8-8ff624010cfa-12641848.jpg)

- Spring AOP（面向切面编程）
- Spring ORM（对象关系映射）
- Spring DAO
- Spring Web
- Spring Context
- Spring Web MVC
- Spring Core

## 1.3 拓展

- Spring Boot 构建

  - 基于Spring Boot 可以快速开发单个微服务。

  - 约定大于配置。

- Spring Cloud 协调

  - 基于Spring Boot 实现。

- Spring Cloud Data Flow 连接

## 1.4 IOC（控制反转）理论推导

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

## 1.5 IOC本质

- 控制反转是一种通过描述（**XML**或**注解**或**Java**）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection，DI）。

## 1.6 配置元数据

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



## 1.7 IOC创建对象的方式

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



## 1.8 Spring配置

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



# 2.SpringMVC

- SpringMVC部署中关键的三要素：处理器映射器、处理器适配器、视图解析器。

## 2.1 DispatcherServlet

- DispatcherServlet即前端控制器，拦截并处理用户各种使用Servlet的请求。

![mvc context hierarchy](C:\Users\23215\Desktop\DeepIntoJavaEE.assets\mvc-context-hierarchy.png)

## 2.2 部署不使用注解的SpringMVC工程

### 2.2.1 具体步骤

- 在web.xml中配置DispatcherServlet，用于转发到相应的Controller。
  - 需要关联一个springMVC的配置文件【servlet-name】-servlet.xml。
  - <servler-mapping>元素的<url-pattern>下匹配响应请求的url。

```xml
<!--1.注册DispatcherServlet-->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--关联一个springmvc的配置文件:【servlet-name】-servlet.xml-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc-servlet.xml</param-value>
    </init-param>
    <!--启动级别-1-->
    <load-on-startup>1</load-on-startup>
</servlet>
<!--/ 匹配所有的请求；（不包括.jsp）-->
<!--/* 匹配所有的请求；（包括.jsp）-->
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

- 在src/main/resource目录下配置springmvc-servlet.xml文件（“springmvc”是DispatcherServlet的name）。
  - 添加处理器映射器org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping
  - 添加处理器适配器org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter
  - 添加视图解析器springframework.web.servlet.view.InternalResourceViewResolver
    - 前缀对应jsp文件的文件路径。
  - <u>配置具体的Controller。</u>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
    <!--视图解析器:DispatcherServlet转发的ModelAndView-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--Handler-->
    <bean id="/hello" class="com.fzj.controller.HelloController"/>
</beans>
```

- 编写HelloController。
  - HelloController需要继承Controller接口，并实现Controller接口中返回ModelAndView类的handleRequest方法。

```java
package com.fzj.controller;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class HelloController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        //创建ModelAndView对象
        ModelAndView modelAndView=new ModelAndView();
        //在对象中添加属性和属性值，对应session级别的变量
        modelAndView.addObject("msg","fzj");
        //指定返回的jsp页面
        modelAndView.setViewName("hello");
        return modelAndView;
    }
}
```

- 编写jsp页面。

### 2.2.1 遇到的问题

- 部署Tomcat后，启动出现404异常。打开项目结构，为工件添加lib，导入所有jar包依赖。

![image-20221020091217464](C:\Users\23215\Desktop\DeepIntoJavaEE.assets\image-20221020091217464.png)

### 2.2.3 SpringMVC执行原理

 ![未命名文件(1)](C:\Users\23215\Desktop\DeepIntoJavaEE.assets\未命名文件(1).png)

- Controller将ModelAndView信息传递给HandleAdaptor，HandleAdaptor传递给DispatcherServlet，DispatcherServlet调用视图解析器匹配视图的名称，然后传递给用户。

## 2.3 部署使用注解的SpringMVC工程

### 2.3.1 具体步骤

- 在无注解的配置中，springmvc-servlet.xml首先需要注册2条生产线（处理器映射器、处理器适配器）。在注解配置中只需添加如下配置，项目上下文自动生成这2条生产线。

```xml
<!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
<context:component-scan base-package="com.fzj.controller"/>
```

- 让Spring MVC忽略静态资源（.css/.html/.mp3...）的配置。

```xml
<mvc:default-servlet-handler />
```

- 在controller中使用@ControllerMapping注解。要想使其生效，必须在springmvc-servlet.xml添加配置。该配置使得Spring MVC项目在上下文中自动注册DefaultAnnotationHandlerMapping和AnnotationHandlerAdapter实例（总之就是可以使用注解）。

```xml
<mvc:annotation-driven/>
```

- 编写HelloController.java，并在其中使用Controller注解。
  - @RequestMapping中的value表示在浏览器访问时的url。
  - 访问sayHello方法的url：[http://localhost:8080](http://localhost:8080/)/HelloController/hello。

```java
package com.fzj.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/HelloController")
public class HelloController {

    @RequestMapping("/hello")
    public String sayHello(Model model){
        model.addAttribute("msg","HelloSpring");
        return "hello";
    }
}
```



### 2.3.2 可能存在的问题

- maven可能存在自动过滤资源的问题，可以在pom.xml中添加如下配置（目前没遇到过）。

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```

## 2.4 比较手动配置和使用注解方法的区别

- 手动配置Controller时必须实施org.springframework.web.servlet.mvc包下的Controller接口，并实现该接口中ModelAndView类的handleRequest()方法，这个方法就是实现具体业务逻辑的方法。且必须在springmvc-servlet.xml中注册指向该controller的bean实例。

```java
//实现该接口的类获得控制器功能
public interface Controller {
    //处理请求且返回一个模型与视图对象
    ModelAndView handleRequest(HttpServletRequest var1, HttpServletResponse var2) throws Exception;
}
```

- 使用注解配置时则需要在springmvc-servlet.xml注册和配置controller。然后在controller.java中使用@Controller和@ControllerMapping注解。

- 无论使用哪种Controller的注册方法，最终返回的页面都是hello.jsp，且页面内容msg随不同的controller而改变。说明视图和控制器呈现弱耦合状态。

## 2.5 RestFul风格

### 2.5.1 指定url栏中输入参数

- 使用@RequestMapping注解，映射带参的访问路径。
- 创建业务逻辑方法，在其形参中url输入的参数前加上@PathVariable注解，表示该参数是从url中截取的。

```java
package com.fzj.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class RestFulController {
    //映射访问路径
    @RequestMapping("/restful/{p1}/{p2}/{p3}")
    public String index(@PathVariable int p1, @PathVariable int p2, @PathVariable String p3,Model model){
        int result=p1+p2;
        String res2= p3+"okokokok";
        model.addAttribute("msg","p1+p2="+result+","+res2);
        return "result";
    }
}
```

### 2.5.2 使用method属性指定请求类型

- 传统的请求资源方法一般只有get和post，RestFul风格可以指定更多的方法（使得访问同一地址可以执行多种方法）。
- **不太懂，回来继续看。**

```java
package com.fzj.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class RestFulController {
    //映射访问路径
    @RequestMapping("/restful/{p1}/{p2}/{p3}")
    public String index(@PathVariable int p1, @PathVariable int p2, @PathVariable String p3,Model model){
        int result=p1+p2;
        String res2= p3+"okokokok";
        model.addAttribute("msg","p1+p2="+result+","+res2);
        return "result";
    }
    //映射访问路径
    @RequestMapping(value = "/hello",method = {RequestMethod.GET})
    public String index2(Model model){
        model.addAttribute("msg", "hello!");
        return "result";
    }
}
```

