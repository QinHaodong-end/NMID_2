

# Spring5

Spring要求：掌握 AOP 和 IOC，了解一些常用注解，能够使用注解或者 XML 进行开 发。 

Spring内容：了解 IOC 和 AOP 的相关概念，通过配置或则注解体验 IOC 和 AOP,了解 Spring 事务的事务控制。

## 一

#### 1、Spring框架啊概述：

**1.1、Spring框架是一个轻量级的 开源的JavaEE框架**

**1.2、解决企业应用开发的复杂性**

**1.3、Spring有两个核心部分：IOC 和 Aop**

(1) IOC：控制反转，把创建对象的过程交给Spring进行管理

(2) Aop：面向切面，不修改源代码的情况下进行功能的增强

#### 2、Spring特点：

(1) 方便解耦，简化开发

(2) Aop编程的支持

(3) 方便程序的测试 

(4) 方便和其他框架整合

(5) 方便进行事务操作

(6) 降低API开发难度

**3、选取Spring版本5.x**

**4、SSM：SpringMvc + Spring + Mybatis**

**5、官网：**https://spring.io/projects/spring-framework#overview

**6、官方下载地址：**https://repo.spring.io/release/org/springframework/spring

**7、GitHub：**https://github.com/spring-projects/spring-framework

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.8</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.8</version>
</dependency>
```

**8、模块**

![image-20210716204841446](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210716204841446.png)

**9、拓展**

* Spring Boot（大多数公司都在使用）
  * 一个快速开发的脚手架
  * 基于SpringBoot可以快速的开发单个微服务
  * 约定大于配置
  * **必须要先掌握Spring和SpringMVC**
* Spring Cloud
  * 是基于SpringBoot实现的

**10、Spring的弊端**

* 违背了原来的理念，配置十分繁琐，人称 “配置地狱”

---

### 二、IOC理论推导

1.UserDao 接口

2.UserDaolmpl 实现类

3.UserService 业务接口

4.UserServicelmpl 业务实现类

在之前的业务中，用户的需求可能会影响原来的代码，我们需要根据用户的需求去修改原代码，如果代码量十分大，修改一次的成本代价十分昂贵



使用一个Set接口，已经发生了革命性的变化

```java
private UserDao userDao;

//使用Set进行动态实现值的注入
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
```

* 之前，程序是主动创建对象，控制权在程序猿手上
* 使用了set注入过后，程序不再具有主动性，而是变成了被动的接收对象
* 这种思想，本质上解决了问题，我们程序员再也不用去管理对象的创建了，系统耦合性大大降低，可以更加专注的在业务的实现上！这是IOC的原型！（控制反转）

* **IOC本质**
* **控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IOC容器，其实现方法是依赖注入**



### 三、HelloSpring

```java
//Hello类
package com.qin.pojo;
public class Hello {
    private String str;
    public String getStr() {
        return str;
    }
    public void setStr(String str) {
        this.str = str;
    }
    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}
```

```xml
<!--配置xml文件,在resources目录下创建-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

<bean id="mysqlImpl" class="com.qin.dao.UserDaoMysqlImpl"/>
<bean id="oracleImp" class="com.qin.dao.UserDaoOracleImpl"/>
<bean id="userServiceImpl" class="com.qin.service.UserServiceImpl">
    <!--使用Spring来创建对象，在Spring这些都称为Bean

类型 变量名 =new 类型（）
Hello hello=new Hello()

id=变量名
class=new 的对象
property 相当于给对象中的属性设置一个值
-->
    <!--ref：引用Spring容器中创建好的对象
        value：具体的值，基本数据类型和String
    name其实就是该类中set方法的名称,ref与value其实就是该类中的set方法的赋值
     -->
    <property name="userDao" ref="mysqlImpl"/>
</bean>
</beans>

<!--前半段在官网复制
官网：https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#spring-core
-->
```

```java
//Test类
public class MyTest {
    public static void main(String[] args) {
        
//获取ApplicationContext,拿到Spring的容器
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
//需要什么就直接get什么
        UserServiceImpl userServiceImpl = (UserServiceImpl) context.getBean("userServiceImpl");//获取该对象
        userServiceImpl.getUser();
    }
}
```

## 问题思考：

* Hello对象是谁创建的？
  * Hello对象是由Spring创建的

* Hello对象的属性是怎么设置的？
  * 由Spring容器设置的（xml文件）

这个过程就叫做控制反转（IOC）：

控制：谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的，使用Spring后，对象由Spring来创建的

反转：程序本身不创建对象，而变成被动的接受对象

依赖注入：就是利用set方法来进行注入

IOC是一种编程思想，由主动的编程变成被动的接收

**到现在，我们彻底不用再去程序中去改动了，要实现不同的操作，只需要在xml配置文件中进行修改，所谓的IOC，一句话搞定：对象由Spring创建、管理、装配！**



---

### 4、IOC创建对象的方式

1.使用无参构造创建对象，默认

2.假设我们要使用有参构造创建对象

​      1.下标赋值

```xml
   <!--第一种，下标赋值！-->
    <bean id="user" class="com.qin.pojo.User">
      <constructor-arg index="0" value="夏雪星是猪"/>
    </bean>
```

​      2.类型创建

```xml
   <!--第二种，通过类型创建，此方式不建议使用！-->
 <!--   <bean id="user" class="com.qin.pojo.User">
        <constructor-arg type="java.lang.String" value="夏雪星是猪"/>
    </bean>-->
```

​     3.参数名

```xml
    <!--第三种，直接通过参数名来设置-->
<bean id="user" class="com.qin.pojo.User">
    <constructor-arg name="name" value="夏雪星是猪"/>
</bean>
```

总结：在配置文件加载的时候，容器中管理的对象已经初始化了



---

### 5、Spring配置

**5.1、别名**

```xml
<!--别名，如果添加了别名，可以通过别名获取到这个对象-->
    <alias name="user" alias="abcdefg"/>
```

**5.2、Bean的配置**

  ```xml
    <!--
      id： bean的唯一标识符，也就是相当于对象名
      class：bean对象对应的全限定名：包名 + 类型
      name：别名，并且name可以同时取多个别名，用逗号或者空格分隔
      -->
      <bean id="user" class="com.qin.pojo.User" name="user2,user3,user4,user5"/>
  ```

**5.3、import**

一般用于团队开发使用，它可以将多个配置文件，导入合并为一个

假设现在项目中有多个人开发

```xml
 <import resource="beans.xml"/>
    <import resource="beans1.xml"/>
```

每个人负责不同的类开发，不同的类需要注册在不同的bean中，我们可以利用在beans,xml中配置import标签，将所有的beans,xml合并为一个总和，使用时直接使用总配置即可



---

### 6、依赖注入（DI）

**6.1、构造器注入：前面已经说过了**

**6.2、Set方式注入（重点）：**

* 依赖注入：Set注入
  * 依赖：bean对象的创建依赖于容器
  * 注入：bean对象中的所有属性，由容器来注入！

【环境搭建】

1.复杂类型

````java
public class Address {
    private String address;

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}
````

2.真实测试对象

```java
public class Student {
    private String name;
    private Address address;
    private  String[] books;
    private List<String> hobbys;
    private Map<String,String> card;
    private Set<String> games;
    private Properties info;
    private  String wife;
}
//以及其get set toString方法
```

3.XML配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="student" class="com.qin.pojo.Student">
        <!--第一种，普通值注入-->
    <property name="name" value="夏雪星"/>
</bean>
</beans>
```

4.测试类

```java
public class MyTest {
    public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Student student = (Student) context.getBean("student");
        System.out.println(student.getName());
    }
}
```

5.完善注入信息

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="address" class="com.qin.pojo.Address">
        <property name="address" value="重庆邮电大学"/>
    </bean>

    <bean id="student" class="com.qin.pojo.Student">
        <!--第一种，普通值注入-->
    <property name="name" value="夏雪星"/>
        <!--第二种，bean注入，ref-->
        <property name="address" ref="address"/>
        <!--第三种，数组注入-->
        <property name="books">
            <array>
                <value>红楼梦</value>
                <value>西游记</value>
                <value>三国演义</value>
                <value>水浒传</value>
            </array>
        </property>
        <!--第四种，List注入-->
        <property name="hobbys">
            <list>
                <value>听歌</value>
                <value>唱歌</value>
                <value>看电影</value>
                <value>敲代码</value>
            </list>
        </property>
        <!--第五种，Map注入-->
        <property name="card">
            <map>
                <entry key="学号" value="1234"/>
                <entry key="银行卡" value="123"/>
                <entry key="身份证" value="12345678"/>
            </map>
        </property>
        <!--第六种，Set注入-->
      <property name="games">
          <set>
              <value>吃鸡</value>
              <value>LOL</value>
              <value>王者</value>
          </set>
      </property>
        <!--第七种，null值注入-->
        <property name="wife">
            <null/>
        </property>
        <!--第八种，Properties-->
        <property name="info">
            <props>
                <prop key="driver">1234</prop>
                <prop key="url">女</prop>
                <prop key="username">root</prop>
                <prop key="password">123456</prop>
            </props>
        </property>
</bean>
</beans>
```



**6.3、拓展方式注入：**

1.p命名空间注入

```xml
<!--
  配置文件中需要加入：xmlns:p="http://www.springframework.org/schema/p"
p命名空间注入，可以直接注入属性的值：p即为properties-->
    <bean id="user" class="com.qin.pojo.User" p:age="20" p:name="夏雪星"/>
```

2.c命名空间注入

```xml
<!--
配置文件中需导入：xmlns:c="http://www.springframework.org/schema/c"
c命名空间注入，通过有参构造器注入：constructor-->
    <bean id="user2" class="com.qin.pojo.User" c:age="20" c:name="夏雪星"/>
```



**6.4、Bean的作用域（Bean scopes）**

![image-20210725142924201](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210725142924201.png)

**1.单例模式（Spring默认机制，共享一个对象）**

![image-20210725143142998](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210725143142998.png)

```xml
 <bean id="user2" class="com.qin.pojo.User" c:age="20" c:name="夏雪星" scope="singleton"/>
```

2.原型模式（每次从容器中get的时候都会产生一个新对象）

![image-20210725143204948](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210725143204948.png)

```xml
 <bean id="user2" class="com.qin.pojo.User" c:age="20" c:name="夏雪星" scope="prototype"/>
```

3.其余的request、session、application这些只能在web开发中使用到



---

### 7、Bean自动装配

* 自动装配是Spring满足bean依赖的一种方式
* Spring会在上下文中自动寻找，并自动给bean装配属性



在Spring中有三种装配的方式

​	1.在xml中显示的配置

​	2.在Java中显示配置

​	3.隐式的自动装配bean【重要】



**7.1、测试**

环境搭建：一个人有两个宠物



**7.2、ByName自动装配**

```xml
    <!--byName：会自动在容器上下文中查找，和自己对象set方法后面的值对应的bean id-->
    <bean id="people" class="com.qin.pojo.People" autowire="byName">
        <property name="name" value="夏雪星"/>
```



**7.3、ByType自动装配**

```xml
<!--byType:会自动在容器上下文中查找，和自己对象属性类型相同的bean
    -->
    <bean id="people" class="com.qin.pojo.People" autowire="byType">
        <property name="name" value="夏雪星"/>
```



小结：

+ byname时，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致
+ bytype时，需要保证所有的bean的class唯一，并且这个bean需要和自动注入的属性类型一致



**7.4、注解实现自动装配**

jdk1.5开始支持注解，Spring2.5就支持注解了

The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML. 

要使用的注解须知：

**1.导入约束（context约束）**

**2.配置注解的支持（<context:annotation-config/>）**

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

**@Autowired**

直接在属性上使用即可！可以在set方式上使用！

```java
  @Autowired
    private Cat cat;
```



使用Autowaired就可以不用编写set方法了，前提是这个自动装配的属性在IOC（Spring）容器中存在，且类型符合bytype!

如果Autowaired环境比较复杂，自动装配午饭通过一个注解【@Autowaired】完成的时候，我们可以通过使用@Qualifier(value="xxx")配合使用

```java
  @Qualifier(value ="dog222")
    private Dog dog;
```

```xml
    <bean id="dog222" class="com.qin.pojo.Dog"/>
```



**@Resource注解**

```java
public class People{
    @Resource(name="cat2")
    private Cat cat;
    
    @Resource
    private Dog dog;
}
```



小结：

@Resource和@Autowaired的区别：

* 两个注解都是用来自动装配的，都可以放在属性字段上
* @Autowaired 通过bytype的方式实现，而且必须要求这个对象存在【常用】
* @Resource 通过byname方式实现，如果找不到名字，则通过bytype方式实现，若两者都找不到则报错【常用】
* 执行顺序不同：@Autowaired 通过bytype的方式实现，@Resource 通过byname方式实现



---

### 8、使用注解开发

在Spring4之后，要使用注解开发，必须要保证AOP的包导入了

![image-20210725172645512](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210725172645512.png)

在使用注解需要导入context约束，增加注解支持

1.bean

2.属性如何注入

```java
//等价于<bean id="user" class="com.qin.pojo.User"/>
//@Component：组件
@Component
public class User {
    public String name;
    //相当于 <property name="name" value="夏雪星"/>
    @Value("夏雪猪")
    public void setName(String name) {
        this.name = name;
    }
}
```

3.衍生的注解

@Component由几个衍生注解，我们在web开发中，会按照mvc三层架构分层

* dao  【@Repository】

* service   【@Service】

* controller  【@Controller】

  这四个注解功能都是一样的，都是代表某个类注册到Spring中，装配Bean

4.自动装配

```java
-@Autowired：自动装配通过类型，名字
    如果Autowired不能唯一自动装配上属性，则需要通过@Qualifier(value="xxx")
-@Nullable   字段标记了这个注解，说明这个字段可以为null
-@Resource : 自动装配通过名字，类型
```

5.作用域

```java
//等价于<bean id="user" class="com.qin.pojo.User"/>
//@Component：组件
@Component
@Scope("singleton")  //设置作用域
public class User {
    public String name;
    //相当于 <property name="name" value="夏雪星"/>
    @Value("夏雪猪")
    public void setName(String name) {
        this.name = name;
    }
}

```

6.小结

xml与注解：

* xml更加万能，适用于任何场合！维护简单方便
* 注解不是自己的类使用不了，维护相对复杂

最佳实践：

* xml用来管理bean

* 注解只负责完成属性的注入

* 我们在使用的过程中，只需要注意一个问题：必须让注解生效就需要开启注解的支持

* ```xml
      <!-- 指定要扫描的包，这个包下的注解就会生效-->
      <context:component-scan base-package="com.qin"/>
      <context:annotation-config/>
  ```



---

### 9、使用Java的方式配置Spring

我们现在要完全不使用Spring的xml配置了，全权交给Java来做

JavaConfig是Spring的一个子项目，在Spring4之后他成为了一个核心功能

```java
//实体类
package com.qin.pojo;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

//这个注解的意思，就是说明这个类被Spring接管了，注册到了容器中
@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }
@Value("夏雪猪")//属性注入值
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

```java
//配置文件
package com.qin.config;

import com.qin.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

//这个也会被Spring容器托管，注册到容器中，因为它本来就是一个@Component
//@Configuration代表这是一个配置类，就和之前看的beans.xml是一样的
@Configuration
@ComponentScan("com.qin.pojo")
@Import(MyConfig2.class)
public class MyConfig {

    //注册一个bean就相当于我们之前写的一个bean标签
    //这个方法的名字就相当于bean标签中的id属性
    //这个方法的返回值，就相当于bean标签的class属性
    @Bean
    public User user(){
        return  new User();//返回要注入到bean的对象
    }
}
```

```java
//测试类
import com.qin.config.MyConfig;
import com.qin.pojo.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MyTest {
    public static void main(String[] args) {

      //如果完全使用了配置类方式去做就只能通过AnnotationConfig上下文来获取容器，通过配置类的class对象加载
       ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
        User getUser = (User) context.getBean("user");
        System.out.println(getUser.getName());
    }
}
```

这种纯Java的配置方式，在SpringBoot中随处可见！



---

### 10、代理模式

为什么要学习代理模式？因为这就是SpringAOP的底层！【SpringAOP和SpringMVC】

代理模式的分类：

* 静态代理
* 动态代理

![image-20210726092112474](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210726092112474.png)

**10.1、静态代理**

角色分析：

* 抽象角色：一般会使用接口或者抽象类来解决
* 真实角色：被代理的角色
* 代理角色：代理真实角色，代理真实角色后，我们会做一些附属操作
* 客户：访问代理对象的人



代码步骤：

1.接口(抽象角色)

```java
package com.qin.demo01;
//租房
public interface Rent {
    public void rent();
}

```

2.真实角色

```java
package com.qin.demo01;
//房东
public class Host implements Rent{
    public void rent() {
        System.out.println("房东要出租房子");
    }
}

```

3.代理角色

```java
package com.qin.demo01;

public class Proxy implements Rent{
    private Host host;

    public Proxy() {
    }

    public Proxy(Host host) {
        this.host = host;
    }

    public void rent() {
        host.rent();
    }

    //看房子
    public void seeHouse() {
        System.out.println("中介带你看房");
    }
        //收中介费
        public void fare(){
            System.out.println("中介费");
        }


    }


```

4.客户端访问代理角色

```java
package com.qin.demo01;

public class Client {
    public static void main(String[] args) {
       //房东要租房子
        Host host=new Host();
        //代理，中介帮房东租房子，但是代理角色一般会有一些附属操作（收费）
        Proxy proxy = new Proxy(host);

        //客户不用面对房东，直接租房即可
        proxy.rent();
    }
}
```





代理模式的好处：

* 可以使真实角色的操作更加存粹！不用去关注一些公共的业务
* 公共业务就交给了代理角色，实现了业务的分工
* 公共业务发生扩展的时候，方便集中管理（不能修改原有的代码）

缺点：

* 一个真实角色就会产生一个代理角色，代码量会翻倍，开发效率会变低~



**10.2、加深理解**

* 代码对应08-demo02



**10.3、动态代理**

* 动态代理和静态代理角色一样
* 动态代理类是动态生成的，不是直接写好的
* 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  * 基于接口---JDK动态代理【我们在这里使用】
  * 基于类：cglib
  * Java字节码实现：Javasist



需要了解两个类：Proxy：代理、InvocationHandler：调用处理程序





动态代理的好处：

* 可以使真实角色的操作更加存粹！不用去关注一些公共的业务
* 公共业务就交给了代理角色，实现了业务的分工
* 公共业务发生扩展的时候，方便集中管理（不能修改原有的代码）
* 一个动态代理类代理的是一个接口，一般就是对应的一类业务
* 一个动态代理类可以代理多个类，只要实现了同一个接口即可



---

### 11、AOP（面向切面编程）

**11.1、什么是AOP**

AOP（Aspect  Oriented  Programming）意为：面向切面编程，通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。



**11.2、AOP在Spring中的作用**

**提供声明式事务，允许用户自定义切面**

* ![image-20210726165229116](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210726165229116.png)

**11.3、使用Spring实现AOP**

【重点】使用AOP植入，需要导入一个依赖包

```xml
   <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.4</version>
    </dependency>
```



方式一：使用Spring的API接口【主要是SpringAPI接口实现】

```xml
    <!--方式一：使用原生API接口-->
    <!--配置aop：需要导入aop的约束-->
<aop:config>
    <!--切入点:expression：表达式，execution(要执行的位置)-->
    <aop:pointcut id="pointcut" expression="execution(* com.qin.service.UserServiceImpl.*(..))"/>

    <!--执行环绕增强-->
<aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
<aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
</aop:config>
```

```java
public class Log implements MethodBeforeAdvice {

   //method：要执行的目标对象的方法
    //objects：参数
    //target：目标对象
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的"+method.getName()+"被执行了");
    }
}
```

```java
public class AfterLog implements AfterReturningAdvice {
    // returnValue：返回值
    public void afterReturning(Object returnValue, Method method, Object[] objects, Object o1) throws Throwable {
        System.out.println("执行了"+method.getName()+"方法，返回结果为"+returnValue);
    }
}
```

```java
public class MyTest {
    public static void main(String[] args) {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
       //动态代理代理的是接口
        UerService userService = (UerService) context.getBean("userService");
        userService.add();

    }
}
```



方式二：自定义来实现AOP【主要是切面定义】

```xml
    <!--方式二：自定义类-->
<bean id="diy" class="com.qin.diy.DiyPointCut"/>

    <aop:config>
        <!--自定义切面，ref要引用的类-->
        <aop:aspect ref="diy">
          <!--切入点-->
<aop:pointcut id="point" expression="execution(* com.qin.service.UserServiceImpl.*(..))"/>
            <!--通知-->
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>
```

```java
public class DiyPointCut {
    public void before(){
        System.out.println("方法执行前");
    }
    public void after(){
        System.out.println("方法执行后");
    }
}

```

```java
public class MyTest {
    public static void main(String[] args) {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
       //动态代理代理的是接口
        UerService userService = (UerService) context.getBean("userService");
        userService.add();

    }
}

```



方式三：使用注解实现

```xml
    <!--方式三-->
<bean id="annotationPointCut" class="com.qin.diy.AnnotationPointCut"/>
    <!--开启注解支持    JDK（默认proxy-target-class="false"） cglib(proxy-target-class="true")   -->
<aop:aspectj-autoproxy proxy-target-class="false"/>
```

```java
@Aspect//标注这个类是一个切面
public class AnnotationPointCut  {
    @Before("execution(* com.qin.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("方法执行前");
    }
@After("execution(* com.qin.service.UserServiceImpl.*(..))")
    public void after(){
        System.out.println("方法执行后");
    }
    //在环绕增强中，我们可以给定一个参数，代表我们要获取处理切入的点
    @Around("execution(* com.qin.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");
        Signature signature=jp.getSignature();//获得签名
        System.out.println("signature"+signature);
        //执行方法
        Object proceed = jp.proceed();
        System.out.println("环绕后");
    }
}
```

```java
public class MyTest {
    public static void main(String[] args) {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
       //动态代理代理的是接口
        UerService userService = (UerService) context.getBean("userService");
        userService.add();

    }
}
```



---

### 12、事务

12.1、回顾事务

* 把一组业务当成一个业务来做，要么都成功，要么都失败
* 事务在项目开发中，十分重要，涉及到数据的一致性问题，不能马虎
* 确保完整性和一致性



事务ACID原则：

* 原子性
* 一致性
* 隔离性
  * 多个业务可能操作同一个资源，防止数据损坏
* 持久性
  * 事务一旦提交，无论系统发生什么问题，结果都不会再被影响，被持久化的写到存储器中



12.2、Spring中的事务管理

* 声明式事务：AOP
* 编程式事务：需要在代码中进行事务的管理













































