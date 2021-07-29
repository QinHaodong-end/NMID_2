# SpringMVC

SpringMVC 要求：了解三层架构和 MVC 模型，编写控制器，SpringMVC 的请求响应流程， 参数绑定机制。自定义类型转换器，异常处理器,拦截器。了解 SpringMVC 的常用注解的使用，了解和使用 rest 风格 url。 

SpringMVC 内容：三层架构，MVC 模型，控制器，拦截器，SpringMVC 的请求响应流程， SpringMVC 的组件，常用注解，rest 风格 url。

---

### 1.1、什么是MVC

ssm：mybatis + Spring + SpringMVC    **MVC三层架构**

**SpringMVC：SpringMVC的执行流程**【重点】

* MVC是模型（Model）、视图（View）、控制器（Controller）的简写，是一种软件设计规范
* Model：数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为），不过现在一般都分离开来：Value Object（数据Dao）和服务层（行为Service）。也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。
* View：负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。
* Controller（控制器）：接受用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示，也就是说控制器做了个调度员的工作
* 最典型的MVC就是JSP + servlet + JavaBean的模式



**1.2、职责分析：**

**Controller：控制器**

1.取得表单数据

2.调用业务逻辑

3.转向指定的页面

**Model：模型**

1.业务逻辑

2.保存数据的状态

**View：视图**

1.显示页面



**1.3、MVC框架要做的事情**

1.将url映射到Java类或Java类的方法

2.封装用户提交的数据

3.处理请求--调用相关的业务处理--封装响应数据

4.将响应的数据进行渲染，html/jsp等表示层数据



---

### 2.1、SpringMVC概述

**概念**：SpringMVC是Spring FrameWork的一部分，是基于Java实现MVC的轻量级Web框架



**2.2、SpringMVC特点：**

1.量级，简单易学

2.高效，基于请求响应的MVC框架

3.与Spring兼容性好，无缝结合

* Spring与SpringMVC的关系：我们可以将SpringMVC中所有要用到的bean，注册到Spring中

4.约定优于配置

5.功能强大：RESTful、数据验证、格式化、本地化、主题等

6.简洁灵活



**2.3、中心控制器**

Spring的web框架围绕**DispacherServlet**设计，DispacherServlet的作用是将请求分发到不同的处理器（不同的方法和类），从Spring 2.5开始的用户可以采用基于注解的controller声明方式



---

### 3.1、第一个MVC程序

1. 新建一个Moudle ， springmvc-02-hello ， 添加web的支持！
2. 确定导入了SpringMVC 的依赖！
3. 配置web.xml ， 注册DispatcherServle

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
        version="4.0">

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

</web-app>

```



4.编写SpringMVC 的 配置文件！名称：springmvc-servlet.xml : [servletname]-servlet.xml

说明，这里的名称要求是按照官方来的

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
 <!--处理器映射器-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
<!--处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>

    <!--视图解析器:DispatcherServlet给他的ModelAndView
    1.获取了ModelAndView的数据
    2.解析ModelAndView的视图名字
    3.拼接视图名字，找到对应的视图 /WEB-INF/jsp/hello
    4.将数据渲染到这个视图上
-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--Handler-->
    <bean id="/hello" class="com.qin.controller.HelloController"/>


</beans>

```



5.编写Controller类,实现Controller接口

```java
public class HelloController implements Controller {

    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        //ModelAndView 模型和视图
        ModelAndView mv = new ModelAndView();

        //封装对象，放在ModelAndView中。Model
        mv.addObject("msg","HelloSpringMVC!");
        //封装要跳转的视图，放在ModelAndView中
        mv.setViewName("hello"); //: /WEB-INF/jsp/hello.jsp
        return mv;
    }
}
```



6.在WEB-INF下创建jsp目录

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${msg}
</body>
</html>
```



7.配置Tomcat启动测试

**可能遇到的问题：访问出现404，排查步骤：**

1. 查看控制台输出，看一下是不是缺少了什么jar包。

2. 如果jar包存在，显示无法输出，就在IDEA的项目发布中，添加lib依赖！

   ![image-20210727144226485](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210727144226485.png)

3. 重启Tomcat 即可解决！



**3.2、SpringMVC的执行原理**

**SpringMVC三代组件：处理器映射器，适配器映射器，视图解析器**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020071914335890.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDYzNTE5OA==,size_16,color_FFFFFF,t_70)

1.**DispatcherServlet表示前置控制器**，是整个SpringMVC的控制中心，用户发送请求后，DispatcherServlet接受请求并拦截请求

2.**HandlerMapping为处理器映射**。DispatcherServlet调用HandlerMapping，HandlerMapping根据请求的url查找Handler

3.HandlerExecution表示具体的Handler，其主要作用是根据url查找控制器，如上例子中的被查找控制器为：hello

4.HandlerExecution将解析后的信息传递给DispatcherServlet，如解析控制器映射等

5.**HnadlerAdapter表示处理器适配器**，其按照特定的规则去执行Handler

6.Handler让具体的Controller执行

7.Controller将具体的执行信息返回给HnadlerAdapter，如ModelView

8.HnadlerAdapter将视图模型传递给DispatcherServlet

9.DispatcherServlet调用**视图解析器（ViewResolver）**

10.视图解析器将解析的逻辑视图名传给DispatcherServlet

11.DispatcherServlet根据视图解析器解析的试图结果，调用具体的视图

12.最终呈现试图给用户



**3.3、注解编写SpringMVC**

1.新建一个Moudel可能存在资源过滤的问题，将配置完善

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

2.配置web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
        version="4.0">

   <!--1.注册servlet-->
   <servlet>
       <servlet-name>SpringMVC</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <!-- 启动顺序，数字越小，启动越早 -->
       <load-on-startup>1</load-on-startup>
   </servlet>

   <!--所有请求都会被springmvc拦截 -->
   <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>

</web-app>


```

3.在resources下配置springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xmlns:mvc="http://www.springframework.org/schema/mvc"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

   <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
   <context:component-scan base-package="com.qin.controller"/>
  <!-- 让Spring MVC不处理静态资源  .css .js  .html  .mps ...-->
   <mvc:default-servlet-handler />
   <!--
   支持mvc注解驱动
       在spring中一般采用@RequestMapping注解来完成映射关系
       要想使@RequestMapping注解生效
       必须向上下文中注册DefaultAnnotationHandlerMapping
       和一个AnnotationMethodHandlerAdapter实例
       这两个实例分别在类级别和方法级别处理。
       而annotation-driven配置帮助我们自动完成上述两个实例的注入。
    -->
   <mvc:annotation-driven />

   <!-- 视图解析器 -->
   <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
         id="internalResourceViewResolver">
       <!-- 前缀 -->
       <property name="prefix" value="/WEB-INF/jsp/" />
       <!-- 后缀 -->
       <property name="suffix" value=".jsp" />
   </bean>

</beans>


```

4.创建Controller

```java
package com.qin.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloController {
    //真实访问地址:Localhost:8080/hello
    @RequestMapping("/hello")
    public String hello(Model model) {
        //向模型中添加属性msg，可以在jsp页面中取出并渲染
        model.addAttribute("msg", "Hello!SpringMVC!");
//加上配置文件中的前后缀变为：WEB-INF/jsp/hello.jsp
        return "hello"; //会被视图解析器处理
    }
}
```

5.在WEB-INF目录下创建jsp目录再再jsp目录下创建hello.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${msg}
</body>
</html>
```

6.运行Tomcat



**小结：**

1.新建一个web项目，导入相关jar包

2.编写web.xml，注册DispatcherServlet

3.编写springmvc配置文件

4.创建Controller类

5.完善前端代码与Controller之间的对应

6.启动Tomcat

**使用springMVC必须配置的三大件：处理器映射器、处理器适配器、视图解析器**

通常，我们只需要手动配置视图解析器，处理器映射器和处理器适配器只需要开启注解驱动即可，因而省去了大段的xml配置

两个注解：

1.@Controller相当于配置方法中的 implements Controller以及手动配置的Hndler中的Controller

2.@ResquestMapping（"/hello"）代表他的真实地址



---

### 4、Controller 及 RestFul风格

**4.1、控制器Controller**

* 控制器复杂提供访问应用程序的行为，通常通过接口定义或注解定义两种方法实现
* 控制器负责解析用户的请求并将其转换为一个模型
* 在SpringMVC中一个控制器类可以包含多个方法
* 在SpringMVC，对于一个Controller的配置方式有很多种



**4.2、实现Controller接口**

1.新建一个Moudle

2.mvc的配置文件只留下视图解析器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
 <!--处理器映射器-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
<!--处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>

    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>

    <bean name="/t1" class="com.qin.controller.ControllerTest1"/>

</beans>


```

3.编写一个Controller类

```java
//只要实现了Controller接口的类，说明这就是一个控制器了
public class ControllerTest1 implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        ModelAndView mv  = new ModelAndView();
        mv.addObject("msg","夏雪星是猪");
        mv.setViewName("test");
        return mv;
    }
}
```

4.在mvc配置文件中注册请求的bean，name以及class（上面已经配置过）

```xml
<bean name="/t1" class="nuc.ss.controller.ControllerTest1"/>
```

5.编写前端jsp代码，注意对应配置文件中的位置

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>Kuangshen</title>
</head>
<body>
${msg}
</body>
</html>
```

6.启动Tomcat

**说明：**

* 实现类的接口Controller定义控制器是比较老的方法
* 缺点：一个控制器中只有一个方法，如果需要多个方法，则需要定义多个Controller，配置文件中也要重新添加配置，定义的方式比较繁琐



**4.3、使用注解@Controller**

* @Controller注解类型用于声明Spring类的实例是一个控制器（在讲IOC时还提到了3个注解）
  * @Coponent（组件）Service（service）Controller（controller）Repository（dao）
* Spring可以使用扫描机制来找到应用程序中所有基于注解的控制类，为了保证Spring能到你的控制器，需要在配置文件中声明组件扫描

```xml
 <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="com.qin.controller"/>
```

* 让SpringMVC不处理静态资源（.css  .js  .html等等），在配置文件中加入

```xml
<mvc:default-servlet-handler />
```

* 注解中的mvc配置文件中无需加入处理器映射器，处理器适配器，只需在配置文件中加入

```xml
<mvc:annotation-driven />
```

* 视图配置器仍需要配置

```xml
 <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>
```

* 总配置如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="com.qin.controller"/>
    <!-- 让Spring MVC不处理静态资源  .css .js  .html  .mps ...-->
    <mvc:default-servlet-handler />
    <!--
    支持mvc注解驱动
        在spring中一般采用@RequestMapping注解来完成映射关系
        要想使@RequestMapping注解生效
        必须向上下文中注册DefaultAnnotationHandlerMapping
        和一个AnnotationMethodHandlerAdapter实例
        这两个实例分别在类级别和方法级别处理。
        而annotation-driven配置帮助我们自动完成上述两个实例的注入。
     -->
    <mvc:annotation-driven />

    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

* 创建一个新的类ControllerTest2

```java
@Controller //代表这个类会被Spring接管，这个注解的类中的所有方法，如果返回值是String
            //并且有具体的页面可以跳转，那么就会被视图解析器解析
public class ControllerTest2 {
    @RequestMapping("t1")
    //@RequestMapping注解用于映射url到控制器类或一个特定的处理程序方法。可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。t1代表的就是启动Tomcat搜索栏后输入的路径，与非注解方法中在mvc配置文件中<bean name="/t1" class="nuc.ss.controller.ControllerTest1"/>对应
    public String test1(Model model){
        model.addAttribute("msg","夏雪星是猪2");
        return "test";//test对应转到对应jsp的文件名字（加上配置文件中的前缀和后缀）
    }
}
```

* 关于@RequestMapping的测试

```java
@Controller
@RequestMapping("/admin")
public class TestController {
   @RequestMapping("/h1")
   public String test(){
       return "test";
  }
}
//或
@Controller
public class TestController {
   @RequestMapping("/admin/h1")
   public String test(){
       return "test";
  }
}
//访问路径：http://localhost:8080 / 项目名/ admin /h1 , 需要先指定类的路径再指定方法的路径；
```

* 启动Tomcat



**4.4、RestFul风格**

* 概念：Restful就是一个资源定位操作的风格，不是标准也不是协议，只是一种风格，基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制

* 功能：

  * 资源：互联网所有的事物都可以被抽象为资源
  * 资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。
  * 分别对应 添加、 删除、修改、查询。
  * 安全，不会暴露代码

* 传统方式操作资源：通过不同的参数来实现不同的效果！方法单一，post 和 get

  * ```java
    //http://localhost:8080/add?a=1&b=2
    ```

* RestFul风格：可以通过不同的请求方式来实现不同的效果，例如：请求地址相同但是功能不同，就是根据据不同的请求方式来实现的

  * ```java
    //RestFul风格：http://localhost:8080/add/a/b
    //（其中ab为具体的值例如：http://localhost:8080/add/1/2），最大作用是安全，不会暴露代码
    ```

* ResfulController类

```java
@Controller
public class RestfulController {
    //原来的：http://localhost:8080/add?a=1&b=2
    //RestFul风格：http://localhost:8080/add/a/b
    // （其中ab为具体的值例如：http://localhost:8080/add/1/2），最大作用是安全，不会暴露代码
   // @RequestMapping(value = "/add/{a}/{b}",method = RequestMethod.DELETE)
    @GetMapping("/add/{a}/{b}")//与上方注解作用相同
//在Spring MVC中可以使用 @PathVariable 注解，让方法参数的值对应绑定到一个URI模板变量上。
    public String test(@PathVariable int a,@PathVariable int b, Model model){
        int res=a+b;
     model.addAttribute("msg","结果为"+res);
        return "test";
    }
}
```

1、@PathVariable ：在Spring MVC中可以使用该注解，让方法参数的值对应绑定到一个URI模板变量上。

2、@GetMapping 是一个组合注解，平时使用的会比较多！它所扮演的是 @RequestMapping(method =RequestMethod.GET) 的一个快捷方式。

3、我们使用浏览器地址栏进行访问默认是Get请求

4、方法级别的注解变体有如下几个：组合注解

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```



---

### 5、结果跳转方式

**5.1、ModelAndView**

页面 : {视图解析器前缀} + viewName +{视图解析器后缀}，springmvc中的添加视图解析器

```xml
<!-- 视图解析器 -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
     id="internalResourceViewResolver">
   <!-- 前缀 -->
   <property name="prefix" value="/WEB-INF/jsp/" />
   <!-- 后缀 -->
   <property name="suffix" value=".jsp" />
</bean>


```

对应的controller类

```java
public class ControllerTest1 implements Controller {

   public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
       //返回一个模型视图对象
       ModelAndView mv = new ModelAndView();
       mv.addObject("msg","ControllerTest1");
       mv.setViewName("test");
       return mv;
  }
}
```



**5.2、ServletAPI**

通过设置设置ServletAPI , 不需要视图解析器 .

```java
@Controller
public class ResultGo {

   @RequestMapping("/result/t1")
   public void test1(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
       rsp.getWriter().println("Hello,Spring BY servlet API");
  }

   @RequestMapping("/result/t2")
   public void test2(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
       rsp.sendRedirect("/index.jsp");
  }

   @RequestMapping("/result/t3")
   public void test3(HttpServletRequest req, HttpServletResponse rsp) throws Exception {
       //转发
       req.setAttribute("msg","/result/t3");
       req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,rsp);
  }

}


```



**5.3、SpringMVC实现转发和重定向**

测试前，讲视图解析器注释掉（无需实体图解析器）

```java
@Controller
public class ResultSpringMVC {
   @RequestMapping("/rsm/t1")
   public String test1(){
       //转发
       return "/index.jsp";
  }

   @RequestMapping("/rsm/t2")
   public String test2(){
       //转发二
       return "forward:/index.jsp";
  }

   @RequestMapping("/rsm/t3")
   public String test3(){
       //重定向
       return "redirect:/index.jsp";
  }
}
```

有视图解析器

```java
@Controller
public class ResultSpringMVC2 {
   @RequestMapping("/rsm2/t1")
   public String test1(){
       //转发
       return "test";
  }

   @RequestMapping("/rsm2/t2")
   public String test2(){
       //重定向
       return "redirect:/index.jsp";
       //return "redirect:hello.do"; //hello.do为另一个请求/
  }

}


```



---

### 6、数据处理

**6.1、处理提交数据**

提交的域名与处理方法的参数名一致

```java
//域名为name
@RequestMapping("/hello")
public String hello(String name){
   System.out.println(name);
   return "hello";
}
```

提交的域名与处理方法的参数名不一致

```java
//@RequestParam("username") : username提交的域的名称 .
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name){
   System.out.println(name);
   return "hello";
}
```

提交的是一个对象

```java
@RequestMapping("/user")
public String user(User user){
   System.out.println(user);
   return "hello";
}

public class User {
   private int id;
   private String name;
   private int age;
   //构造
   //get/set
   //tostring()
}

```

**说明：如果使用对象的话，前端传递的参数名和对象名必须一致，否则就是null**



**6.2、数据显示到前端**

通过ModelAndView

```java
public class ControllerTest1 implements Controller {

   public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
       //返回一个模型视图对象
       ModelAndView mv = new ModelAndView();
       mv.addObject("msg","ControllerTest1");
       mv.setViewName("test");
       return mv;
  }
}


```

通过ModelMap

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name, ModelMap modelMap){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   modelMap.addAttribute("name",name);
   System.out.println(name);
   return "hello";
}



```

通过Model

```java
@RequestMapping("/ct2/hello")
public String hello(@RequestParam("username") String name, Model model){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   model.addAttribute("msg",name);
   System.out.println(name);
   return "test";
}
```

**6.3、小结：**

* Model 只有寥寥几个方法只适合用于储存数据，简化了新手对于Model对象的操作和理解；
*  ModelMap 继承了 LinkedMap ，除了实现了自身的一些方法，同样的继承 LinkedMap 的方法和特性；
*  ModelAndView 可以在储存数据的同时，可以进行设置返回的逻辑视图，进行控制展示层的跳转。



---

### 7、乱码问题

1.编写过滤器，设置编码

```java
public class EncodingFilter implements Filter {

    public void init(FilterConfig filterConfig) throws ServletException {

    }

    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
servletRequest.setCharacterEncoding("utf-8");
servletResponse.setCharacterEncoding("utf-8");

filterChain.doFilter(servletRequest,servletResponse);
    }

    public void destroy() {

    }
}
```

2.SpringMVC给我们提供了一个过滤器 , 可以在web.xml中配置 ，修改了xml文件需要重启服务器！

```xml
<filter>
   <filter-name>encoding</filter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   <init-param>
       <param-name>encoding</param-name>
       <param-value>utf-8</param-value>
   </init-param>
</filter>

<filter-mapping>
   <filter-name>encoding</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

**注意：这里写/*，写/的话过滤不了jsp页面，不能解决乱码**

3.修改Tomcat配置文件：设置编码

```xml
<Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
          connectionTimeout="20000"
          redirectPort="8443" />


```

4.自定义过滤器（万能方法）

```java
package com.kuang.filter;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.Map;

/**
* 解决get和post请求 全部乱码的过滤器
*/
public class GenericEncodingFilter implements Filter {

   @Override
   public void destroy() {
  }

   @Override
   public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
       //处理response的字符编码
       HttpServletResponse myResponse=(HttpServletResponse) response;
       myResponse.setContentType("text/html;charset=UTF-8");

       // 转型为与协议相关对象
       HttpServletRequest httpServletRequest = (HttpServletRequest) request;
       // 对request包装增强
       HttpServletRequest myrequest = new MyRequest(httpServletRequest);
       chain.doFilter(myrequest, response);
  }

   @Override
   public void init(FilterConfig filterConfig) throws ServletException {
  }

}

//自定义request对象，HttpServletRequest的包装类
class MyRequest extends HttpServletRequestWrapper {

   private HttpServletRequest request;
   //是否编码的标记
   private boolean hasEncode;
   //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
   public MyRequest(HttpServletRequest request) {
       super(request);// super必须写
       this.request = request;
  }

   // 对需要增强方法 进行覆盖
   @Override
   public Map getParameterMap() {
       // 先获得请求方式
       String method = request.getMethod();
       if (method.equalsIgnoreCase("post")) {
           // post请求
           try {
               // 处理post乱码
               request.setCharacterEncoding("utf-8");
               return request.getParameterMap();
          } catch (UnsupportedEncodingException e) {
               e.printStackTrace();
          }
      } else if (method.equalsIgnoreCase("get")) {
           // get请求
           Map<String, String[]> parameterMap = request.getParameterMap();
           if (!hasEncode) { // 确保get手动编码逻辑只运行一次
               for (String parameterName : parameterMap.keySet()) {
                   String[] values = parameterMap.get(parameterName);
                   if (values != null) {
                       for (int i = 0; i < values.length; i++) {
                           try {
                               // 处理get乱码
                               values[i] = new String(values[i]
                                      .getBytes("ISO-8859-1"), "utf-8");
                          } catch (UnsupportedEncodingException e) {
                               e.printStackTrace();
                          }
                      }
                  }
              }
               hasEncode = true;
          }
           return parameterMap;
      }
       return super.getParameterMap();
  }

   //取一个值
   @Override
   public String getParameter(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       if (values == null) {
           return null;
      }
       return values[0]; // 取回参数的第一个值
  }

   //取所有值
   @Override
   public String[] getParameterValues(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       return values;
  }
}
```

小结：

1、一般情况下，SpringMVC默认的乱码处理就已经能够很好的解决了！

**2、然后在web.xml中配置这个过滤器即可！**

3、乱码问题，需要平时多注意，在尽可能能设置编码的地方，都设置为统一编码 UTF-8！



---

### 8、JSON

**8.1、概念：**

- JSON(JavaScript Object Notation, JS 对象标记) 是一种轻量级的数据交换格式，目前使用特别广泛。
- 采用完全独立于编程语言的**文本格式**来存储和表示数据。
- 简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。
- 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

**8.2、格式：**

- 对象表示为键值对，数据由逗号分隔
- 花括号保存对象
- 方括号保存数组

```json
{"name": "QinJiang"}
{"age": "3"}
{"sex": "男"}
```

json是js对象的字符串表示法

```js
var obj = {a: 'Hello', b: 'World'}; //这是一个对象，注意键名也是可以使用引号包裹的
var json = '{"a": "Hello", "b": "World"}'; //这是一个 JSON 字符串，本质是一个字符串
```

json与js对象的相互转化

```js
//JSON字符串转换为JavaScript 对象
var obj = JSON.parse('{"a": "Hello", "b": "World"}');
//结果是 {a: 'Hello', b: 'World'}

//JavaScript 对象转换为JSON
var json = JSON.stringify({a: 'Hello', b: 'World'});
//结果是 '{"a": "Hello", "b": "World"}'
```



**8.3、Controller返回JSON数据**

* 使用Jackson、fastjson等解析工具

jackson:

首先要导入jar包：

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-databind</artifactId>
   <version>2.9.8</version>
</dependency>
```

web.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
        version="4.0">

   <!--1.注册servlet-->
   <servlet>
       <servlet-name>SpringMVC</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <!-- 启动顺序，数字越小，启动越早 -->
       <load-on-startup>1</load-on-startup>
   </servlet>

   <!--所有请求都会被springmvc拦截 -->
   <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>

   <filter>
       <filter-name>encoding</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
       <init-param>
           <param-name>encoding</param-name>
           <param-value>utf-8</param-value>
       </init-param>
   </filter>
   <filter-mapping>
       <filter-name>encoding</filter-name>
       <url-pattern>/</url-pattern>
   </filter-mapping>

</web-app>
```

springmvc配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xmlns:mvc="http://www.springframework.org/schema/mvc"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

   <!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
   <context:component-scan base-package="com.kuang.controller"/>

   <!-- 视图解析器 -->
   <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
         id="internalResourceViewResolver">
       <!-- 前缀 -->
       <property name="prefix" value="/WEB-INF/jsp/" />
       <!-- 后缀 -->
       <property name="suffix" value=".jsp" />
   </bean>

</beans>
```

编写实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
  private int age;
}
```

编写Controller

```java
@Controller
public class UserController {
@RequestMapping("/j1")
    @ResponseBody//他就不会走视图解析器，会直接返回一个 字符串
    public String json1() throws JsonProcessingException {
        //jackson,ObjectMapper
        ObjectMapper mapper = new ObjectMapper();

        //创建一个对象
        User user = new User(1, "夏雪猪", 12);
        //System.out.println(user);

        String str = mapper.writeValueAsString(user);
        return str;
    }
```

此时输入中文会产生乱码

```java
//produces:指定响应体返回类型和编码
@RequestMapping(value = "/j1",produces = "application/json;charset=utf-8")
```

启动Tomcat



**8.4、JSON乱码问题**

在springmvc配置

```xml
<mvc:annotation-driven>
   <mvc:message-converters register-defaults="true">
       <bean class="org.springframework.http.converter.StringHttpMessageConverter">
           <constructor-arg value="UTF-8"/>
       </bean>
       <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
           <property name="objectMapper">
               <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                   <property name="failOnEmptyBeans" value="false"/>
               </bean>
           </property>
       </bean>
   </mvc:message-converters>
</mvc:annotation-driven>
```



**8.5、返回JSON字符串统一解决**

* @ResponseBody解决（每个方法都要加，不建议使用）

```java
@Controller
public class UserController {
   //produces:指定响应体返回类型和编码
   @RequestMapping(value = "/json1")
   @ResponseBody
   public String json1() throws JsonProcessingException {
       //创建一个jackson的对象映射器，用来解析数据
       ObjectMapper mapper = new ObjectMapper();
       //创建一个对象
       User user = new User(1, "夏雪猪", 12);
       //将我们的对象解析成为json格式
       String str = mapper.writeValueAsString(user);
       //由于@ResponseBody注解，这里会将str转成json格式返回；十分方便
       return str;
  }
}
```

* @RestController（直接加到类上即可）

```java
@RestController //加了此注解就不会走视图解析器，会直接返回一个字符串
public class UserController {
   @RequestMapping(value = "/j1")
   public String json1() throws JsonProcessingException {
       //创建一个jackson的对象映射器，用来解析数据
       ObjectMapper mapper = new ObjectMapper();
       //创建一个对象
       User user = new User(1, "夏雪猪", 12);
       //将我们的对象解析成为json格式
       String str = mapper.writeValueAsString(user);
       return str;
  }
}
```

* 添加测试方法

```java
@RequestMapping("/j2")
public String json2() throws JsonProcessingException {

    //创建一个jackson的对象映射器，用来解析数据
    ObjectMapper mapper = new ObjectMapper();
    //创建一个对象
   	User user1 = new User(1, "1号", 12);
    User user2 = new User(2, "2号", 12);
    User user3 = new User(3, "3号", 12);
    User user4 = new User(4, "4号", 12);
    User user5 = new User(5, "5号", 12);
    List<User> list = new ArrayList<User>();
    list.add(user1);
    list.add(user2);
    list.add(user3);
    list.add(user4);
    list.add(user5);
    //将我们的对象解析成为json格式
    String str = mapper.writeValueAsString(list);
    return str;
}
```



**8.6、输出时间对象**

* 新增方法

```java
@RequestMapping("/j3")
public String json3() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   //创建时间一个对象，java.util.Date
   Date date = new Date();
   //将我们的对象解析成为json格式
   String str = mapper.writeValueAsString(date);
   return str;
}
```

* 将时间戳咋转为具体时间

```java
@RequestMapping("/j3")
public String json4() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   //不使用时间戳的方式
   mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
   //自定义日期格式对象
   SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
   //指定日期格式
   mapper.setDateFormat(sdf);

   Date date = new Date();
   String str = mapper.writeValueAsString(date);

   return str;
}
```

* 如果要经常使用的话，这样是比较麻烦的，我们可以将这些代码封装到一个工具类中

```java
public class JsonUtils {
    public static String getJson(Object object) {
        return getJson(object,"yyyy-MM-dd HH:mm:ss");
    }
    public static String getJson(Object object, String dateFormat) {
        ObjectMapper mapper = new ObjectMapper();

        //java自定义日期格式
        SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
        //sdf.format(date)

        // 使用ObjectMapper 来格式化输出
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
        mapper.setDateFormat(sdf);

        try {
            //ObjectMapper,时间解析后的默认格式为：TImestamp.时间戳
            return mapper.writeValueAsString(object);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return null;
    }

}
```

```java
@RequestMapping("/j3")
    public String json3(){

        Date date = new Date();

        return JsonUtils.getJson(date,"yyyy-MM-dd HH:mm:ss");
    }
}


```

**8.7、FastJson**

- fastjson.jar是阿里开发的一款专门用于Java开发的包，
- 实现json对象与JavaBean对象的转换，
- 实现JavaBean对象与json字符串的转换，
- 实现json对象与json字符串的转换。
- 实现json的转换方法很多，最后的实现结果都是一样的。

导入依赖

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>fastjson</artifactId>
   <version>1.2.60</version>
</dependency>
```

新建测试类

```java
@RequestMapping("/j4")
    //@ResponseBody//他就不会走视图解析器，会直接返回一个 字符串
    public String json4(){

        User user1 = new User(1, "1号", 12);
        User user2 = new User(2, "2号", 12);
        User user3 = new User(3, "3号", 12);
        User user4 = new User(4, "4号", 12);
        User user5 = new User(5, "5号", 12);

        List<User> list = new ArrayList<User>();
        list.add(user1);
        list.add(user2);
        list.add(user3);
        list.add(user4);
        list.add(user5);

        System.out.println("*******Java对象 转 JSON字符串*******");
        String str1 = JSON.toJSONString(list);
        System.out.println("JSON.toJSONString(list)==>"+str1);
        String str2 = JSON.toJSONString(user1);
        System.out.println("JSON.toJSONString(user1)==>"+str2);

        System.out.println("\n****** JSON字符串 转 Java对象*******");
        User jp_user1=JSON.parseObject(str2,User.class);
        System.out.println("JSON.parseObject(str2,User.class)==>"+jp_user1);

        System.out.println("\n****** Java对象 转 JSON对象 ******");
        JSONObject jsonObject1 = (JSONObject) JSON.toJSON(user2);
        System.out.println("(JSONObject) JSON.toJSON(user2)==>"+jsonObject1.getString("name"));

        System.out.println("\n****** JSON对象 转 Java对象 ******");
        User to_java_user = JSON.toJavaObject(jsonObject1, User.class);
        System.out.println("JSON.toJavaObject(jsonObject1, User.class)==>"+to_java_user);

        return JSON.toJSONString(list);
    }


```



---

### 9、SpringMVC拦截器

**9.1、概述：**

- SpringMVC的处理器拦截器类似于Servlet开发中的过滤器Filter,用于对处理器进行预处理和后处理。
- 开发者可以自己定义一些拦截器来实现特定的功能。

**过滤器：**

- servlet规范中的一部分，任何javaweb工程都可以使用
- 在url-pattern中配置了/*之后，可以对所有要访问的资源进行拦截

**拦截器：**

- 拦截器是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用
- 拦截器只会拦截访问的控制器方法， 如果访问的是jsp/html/css/image/js是不会进行拦截的

**过滤器与拦截器的区别：**拦截器是AOP思想的具体应用。



**9.2、自定义拦截器**

1. 新建一个Moudule ， springmvc-Interceptor ， 添加web支持
2. 配置web.xml 和 springmvc-servlet.xml 文件

```xml
<!--拦截器配置-->
<mvc:interceptors>
    <mvc:interceptor>
        <!--包括这个请求下面的所有请求-->
        <mvc:mapping path="/**"/>
        <bean class="nuc.ss.config.MyInterceptor"/>
    </mvc:interceptor>

</mvc:interceptors>


```

3.编写一个拦截器（必须实现 HandlerInterceptor 接口）

```java
package nuc.ss.config;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyInterceptor implements HandlerInterceptor {
    //return true; 执行下一个拦截器，放行
    //return false; 不执行下一个拦截器，拦截
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("===========处理前===========");
        return true;
    }
    //日志
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("===========处理后===========");

    }
    //日志
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("===========清理===========");

    }
}

```

4.编写Controller类

```java
package nuc.ss.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TestController {
    @GetMapping("/t1")
    public String test() {
        System.out.println("TestController.test");
        return "ok";
    }
}
```

5.启动Tomcat
