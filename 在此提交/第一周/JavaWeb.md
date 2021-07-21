# JavaWeb

## DAY1

**1.1、XML**

* 定义：xml是可扩展的标记性语言
* 作用：(1)用来保存数据，而且这些数据具有自我描述性
* (2)可以用来作为项目或者模块的配置文件
* (3)还可以作为网络传输数据的格式（现在以json为主）



**1.2、XML语法**

* 文档声明
* 元素（标签）
* xml属性
* xml注释
* 文本区域（CDATA区）



**1.3、创建示例**

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!--
以上表示xml文件的声明
version="1.0"    version表示xml的版本
encoding="utf-8"   encoding表示xml文件本身的编码
-->
<books>  <!--books表示多个图书信息-->
    <book sn="SN1234234"> <!--book表示一个图书信息  sn属性表示图书序列号-->
        <name>时间简史</name> <!--name属性表示书名-->
        <author>霍金</author> <!--author属性表示作者-->
        <price>75</price> <!--price属性表示价格-->
    </book>
    <book sn="SN1234235"> <!--book表示一个图书信息  sn属性表示图书序列号-->
        <name>Java</name> <!--name属性表示书名-->
        <author>夏雪星</author> <!--author属性表示作者-->
        <price>9.9</price> <!--price属性表示价格-->
    </book>
</books>
```



**1.4、通过dom4j提取xml中的属性**

```java
//前提：需要导入单元测试junit.jar、dom4j.jar、harmcrest-core.jar
package com.qin.pojo;

import java.math.BigDecimal;

public class Book {
    private String sn;
    private String name;
    private String author;
    private double price;

    public String getSn() {
        return sn;
    }

    public void setSn(String sn) {
        this.sn = sn;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "Book{" +
                "sn='" + sn + '\'' +
                ", name='" + name + '\'' +
                ", author='" + author + '\'' +
                ", price=" + price +
                '}';
    }

    public Book() {
    }

    public Book(String sn, String name, String author, double price) {
        this.sn = sn;
        this.name = name;
        this.author = author;
        this.price = price;
    }
}

public class Dom4jTest {
    @Test
    public void test1() throws Exception {
        SAXReader saxReader=new SAXReader();
        try {
            Document document= saxReader.read("src/books.xml");
            System.out.println(document);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
//读取books.xml文件生成book类
    @Test
    public void test2() throws Exception {
//1.读取books.xml文件
        SAXReader saxReader=new SAXReader();
        Document document = saxReader.read("src/books.xml");
//2.通过Document对象获取根元素
        Element rootElement = document.getRootElement();
      //  System.out.println(rootElement);
//3.通过根元素获取标签对象
        //element方法和elements都是通过标签名查找子元素
        List<Element> books = rootElement.elements("book");
//4.遍历，处理每个book标签转换为Book类
        for (Element book:books
             ) {
            //asXML是把标签对象转化为标签字符串
          // System.out.println(book.asXML());
            Element nameElement=book.element("name");
          //  System.out.println(nameElement.asXML());
            //getText()方法可以获取标签中的文本内容
            String nameTest=nameElement.getText();
            System.out.println(nameTest);
            //直接获取标签文本的指定内容
            String priceText=book.elementText("price");
            System.out.println(priceText);
            String author=book.elementText("author");
            System.out.println(author);
            String snValue=book.attributeValue("sn");
            System.out.println(snValue);

            System.out.println(new Book(snValue,nameTest,author,Double.parseDouble(priceText)));
            System.out.println("===================================");
        }
    }

}
```



**2、JavaWeb**

* 概念：JavaWeb是指所有通过Java语言编写的可以通过浏览器访问的程序的总称，JavaWeb是基于请求和响应来开发的
* 请求：指客户端(浏览器)给服务器(Tomcat)发送数据，Request
* 响应：指服务端给客户端回传数据，Response
* 请求和响应是成对出现的，有请求就有响应



**2.1、Web资源的分类**

* web资源按实现的技术和呈现效果的不同分为静态资源和动态资源
* 静态资源：html ( 页面 ) 、css（样式）、js（代码）、txt（文本）、mp4（视频）·······
* 动态资源：jsp页面、Servlet程序



**2.2、常用的Web服务器**

* Tomcat、Jboss、GlassFish（商业服务器）、Resin（收费）、WebLogic（收费最广泛）



**2.3、Tomcat服务器和Servelet版本的对应关系**

* Servlet程序从2.5版本是现在世面使用最多的版本（XML配置）
* Servlet3.0之后，就是注解版本的Servlet使用



**3、Tomcat的使用**

* 安装：找到需要的Tomcat版本对应的zip压缩包，解压到需要安装的目录即可
* 目录：bin：存放Tomcat服务器的可执行程序
* conf：存放Tomcat的配置文件
* lib：存放Tomcat服务器的jar包
* logs：存放Tomcat服务器运行时输出的日记信息
* temp：存放Tomcat运行时产生的临时数据
* webapps：存放部署的Web工程
* work：存放Tomcat运行时jsp翻译为Servlet的源码，和Session钝化目录



**3.1、启动Tomcat服务器**

* 找到Tomcat目录下bin目录下的startup.bat文件，双击
* 测试是否启动成功：1、打开浏览器地址栏输入：http://localhost:8080
* 2、当出现如下界面说明Tomcat启动成功![image-20210718151236379](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718151236379.png)



**3.2、另一种Tomcat启动方式**

* 打开命令行cd进入Tomcat 的 bin 目录下
* ![image-20210718151935712](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718151935712.png)

* 输入命令：catalina run
* ![image-20210718152001421](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718152001421.png)
* 网页搜索
* ![image-20210718152026949](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718152026949.png)



**3.3、Tomcat的停止方式**

* 点击tomcat服务器窗口的关闭
* 把Tomcat服务器窗口置为当前窗口然后Crt+c
* **找到Tomcat的bin目录下的shutdown.bat双击停止（主要方式）**



**3.4、如何修改Tomcat的端口号**

* Mysql默认的端口号：3306
* Tomcat默认的端口号：8080
* 找到Tomcat目录下的conf目录找到server.xml配置文件
* ![image-20210718152743178](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718152743178.png)
* 修改范围 1~65535（修改完端口号一定要重启Tomcat才能生效）
* 日常上百度：http://www.baidu.com:80
* HTTP协议默认的端口号是：80



**3.5、如何把web工程部署到Tomcat中**

* 方法一：1、把web工程的目录拷贝到Tomcat的webapps目录下即可
* 2、浏览器地址栏输入localhost:8080相当于访问到Tomcat地址下的webapps里面
* ![image-20210718154855670](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718154855670.png)

* ![image-20210718154911579](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718154911579.png)

* ![image-20210718154929119](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718154929119.png)

* 方法二：在Tomcat下的的conf目录中找到Catalina中的localhost文件，然后在其中建立一个xml文件

* ```xml
  <!--Contex表示一个工程上下文
  path表示工程的访问路径：abc
  docBase表示工程目录在哪里
  -->
  
  <Context path="/abc" docBase="C:\Users\QHD\Desktop\Java\JavaWeb\xxx" />
  ```

* ![image-20210718161628717](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718161628717.png)



**3.6、ROOT工程的访问，以及默认indxex.html页面的访问**

* 当我们在浏览器地址栏中输入访问地址如下：
* http://ip:port/   ====>>>  没有工程名的时候，默认访问的是在webapps下的ROOT工程
* 当在浏览器地址栏中输入的地址如下：
* http://ip:port/工程名/   ====>>> 没有资源名，默认访问的是index.html页面



**4、IDEA整合Tomcat服务器**

* File | Settings | Build, Execution, Deployment | Application Servers
* 选择Tomcat所在地址
* ![image-20210718225824180](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718225824180.png)



**4.1、IDEA中动态Web的操作**

* 创建一个新模块
* 勾选上Web Application(4.0)，下方Create web.xml默认勾选上
* ![image-20210718230039043](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718230039043.png)
* 输入工程名，finish
* ![image-20210718230933085](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210718230933085.png)
* src：存放在自己编写的Java代码
* web目录：存放web工程的资源文件（html、css、js文件等）
* WEB-INF目录是一个服务器保护的目录，浏览器无法直接访问到此目录的内容
* web.xml它是整个动态web工程的配置部署描述文件可以在这些配置很多web工程的组件，比如：Servlet程序、Filter过滤器、Listener监听器、Session超时等等



**4.2、如何在IDEA中部署工程到Tomcat上运行**

* 建议修改web工程对应的Tomcat实例名称，与工程名文字一致
* ![image-20210719002811553](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210719002811553.png)

---

## DAY2

**1.1、Servlet**

* 概念：Servlet是是JavaEE规范之一，规范就是接口
* Servlet是JavaWeb三大组件之一，三大组件分别是Servlet程序、Filter过滤器、Listener监听器
* Servlet是运行在服务器的一个Java小程序，它可以**接受客户端发送过来的请求并响应数据给客户端**



**1.2、手动实现Servlet程序**

* 编写一个类去是西安Servlet接口

* 实现Servlet方法，处理请求，并响应数据

* 到web.xml中去配置servlet程序的访问地址

* ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
  
  <!--servlet标签给Tomcat配置servlet程序-->
  
  <servlet>
      <!--servlet-name给servlet程序起一个别名（一般是类名）-->
      <servlet-name>HelloServlet</servlet-name>
  <!--servlet-class是servlet程序的全类名-->
      <servlet-class>com.qin.servlet.HelloServlet</servlet-class>
  </servlet>
      <!--servlet-mapping给servlet程序配置访问地址-->
      <servlet-mapping>
          <!--servlet-name是告诉服务器，当前配置的地址给哪个servlet程序使用-->
          <servlet-name>HelloServlet</servlet-name>
          <!--url-pattern配置访问地址
             /hello
             /  表示地址：http://ip:port/工程路径（在Edit configurations中设置，通常设置为工程名）
             /hello 表示地址：http://ip:port/工程路径/hello
  
          -->
          <url-pattern>/hello</url-pattern>
      </servlet-mapping>
  
  
  
  </web-app>
  ```

  **http://localhost:8080/servlet-01/hello（客户端，浏览器）**
  
* http:// 表示http协议

* local host是服务器ip

* ：8080是服务器的端口号

* /servlet-01是工程路径

* /hello是资源路径



服务器（硬件 电脑）

* ip地址唯一：通过ip地址定位服务器
* 8080端口：通过端口号定位Tomcat
* 通过工程路径确定访问哪个工程
* web.xml配置文件会优先检查url-pattern==><servlet-mapping>中的servlet-name==>外面的servlet-name==>servlet-class最终确定到具体类的具体功能 



**1.3、Servlet的生命周期**

* (1) 执行Servlet构造器方法
* (2) 执行init初始化方法
* 第一、二步是在第一次访问的时候创建Servlet程序会调用
* (3) 执行service方法
* 第三步每次访问都会调用
* (4) 执行destroy销毁方法
* 第四步在Web工程停止的时候调用

 

**1.4、GET和POST请求的分发处理**

```java
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println(" 3  hello servlet 被访问了");
//类型转换，HttpServletRequest含有Method()方法
        HttpServletRequest httpServletRequest=(HttpServletRequest) servletRequest;
        //获取请求方式：post||get
String method=httpServletRequest.getMethod();
if ("GET".equals(method)){
    doGet();
}
else if ("POST".equals(method)){
    doPost();
}
    }

/*
做get请求的操作
 */
public void doGet(){
            System.out.println("get请求");
            System.out.println("get请求");
            System.out.println("get请求");
        }
public void doPost() {
    System.out.println("post请求");
    System.out.println("post请求");
    System.out.println("post请求");
}
```



**1.5、通过继承HttpServlet实现Servlet**

* 一般在实际项目开发中都是使用HttpServlet类的方式去实现Servlet程序
* 1、编写一个类去继承HttpServlet类
* 2、根据业务需要重写doGEt或者doPost方法
* 3、到web.xml中的配置Servlet程序的访问地址

```java
//1、2
public class HelloServlet2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       // super.doGet(req, resp);
        System.out.println("HelloServlet2的doGet方法");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //super.doPost(req, resp);
        System.out.println("HelloServlet2的doPost方法");
    }
}
```

```xml
//3
   <servlet>
        <servlet-name>HelloServlet2</servlet-name>
        <servlet-class>com.qin.servlet.HelloServlet2</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloServlet2</servlet-name>
        <url-pattern>/hello2</url-pattern>
    </servlet-mapping>
```



**1.6、使用IDEA创建Servlet程序**

* ![image-20210720110034158](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210720110034158.png)

* ![image-20210720105720780](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210720105720780.png)

* 配置好web.xml中的<servlet-mapping>即可，IDEA会自动生成类实现post或者get的功能即可



**1.7、Servlet类的继承体系**

* ![image-20210720112520852](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210720112520852.png)



**2.1、ServletConfig类**

*  从类名来看就知道是Servlet程序的配置信息类

*  Servlet程序和ServletConfig对象都是由Tomcat负责创建，我们负责使用

*  Servlet程序默认是第一次访问的时候创建，ServletConfig是每个Servlet程序创建时，就创建一个ServletConfig对象

* 三大作用：(1)可以获取Servlet程序的别名（servlet-name）的值

* (2)获取初始化参数init-param

* (3)获取ServletContext对象

* ```java
   public void init(ServletConfig servletConfig) throws ServletException {
          System.out.println("2 init初始化方法");
          //(1)
          System.out.println("HelloServlet程序别名是" + servletConfig.getServletName());
          //(2)
          System.out.println("初始化参数username的值是" + servletConfig.getInitParameter("username"));
          System.out.println("初始化参数url的值是" + servletConfig.getInitParameter("url"));
          //(3)
          System.out.println("ServletContext的对象是：" + servletConfig.getServletContext());
      }
  ```

* web.xml配置中加入<init-param>初始化

* ```xml
  <servlet>
      <!--servlet-name给servlet程序起一个别名（一般是类名）-->
      <servlet-name>HelloServlet</servlet-name>
  <!--servlet-class是servlet程序的全类名-->
      <servlet-class>com.qin.servlet.HelloServlet</servlet-class>
  <!--init-param是初始化参数-->
      <init-param>
      <!--param-name是参数名-->
      <param-name>username</param-name>
      <!--param-value是参数值-->
      <param-value>root</param-value>
  </init-param>
      <init-param>
          <param-name>url</param-name>
          <param-value>jdbc:mysql://localhost:3306/test</param-value>
      </init-param>
  </servlet>
  ```



**2.2、ServletConfig的补充说明**

* 如果重写init方法一点更要在首行加上super.init(config)方法



**3.1、ServletContext类**

* ServletContext是一个接口，它表示Servlet上下文对象

* 一个web工程，只有一个ServletContext对象实例

* ServletContext对象是一个**域对象**（可以像Map一样存取数据的对象，域指的是存取数据的操作范围，整个web工程）

* |            | 存数据       | 取数据       | 删除数据        |
  | ---------- | ------------ | ------------ | --------------- |
  | **Map**    | **put**      | **get**      | **remove**      |
  | **域对象** | setAttribute | getAttribute | removeAttribute |

* ServletContext是在web工程部署启动的时候创建。在web工程停止的时候销毁



**3.2、ServletContext类的四个作用**

* 获取web.xml中配置的上下文参数context-param

* 获取当前的工程路径，格式：/工程路径

* 获取工程部署后在服务器磁盘上的绝对路径

* 像Map一样存取数据

* ```java
   protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
         //(1)获取web.xml中配置的上下文参数context-param
          ServletContext  context=getServletConfig().getServletContext();
          String username = context.getInitParameter("username");
          System.out.println("context-param参数的username的值是"+username);
          System.out.println("context-param参数的password的值是"+context.getInitParameter("password"));
          //(2)获取当前的工程路径，格式：/工程路径
          System.out.println("当前工程路径："+context.getContextPath());
          //(3)获取工程部署后在服务器磁盘上的绝对路径
          /*
                    /  ====> 表示 http://ip:port/工程名/
           */
          System.out.println("工程部署路径是"+context.getRealPath("/"));
      }
  ```



**4.1、HTTP协议**

* 协议：双方或多方相互约定好大家都需要遵守的规则
* HTTP协议：指客户端和服务器之间通信时发送的数据，遵守的规则，HTTP协议中的数据叫做报文



**4.2、请求的HTTP协议格式**

* 客户端给服务器发送数据叫请求
* 服务器给客户端回传数据叫响应



**4.3、GET请求**

* 1、请求行：
* (1) 请求的方式  (2) 请求的资源路径[+？+请求参数] (3)请求的协议的版本号 HTTP/1.1
* 2、请求头：
* key：value 组成  不同的键值对，表示不同的含义
* Accept：告诉服务器客户端可以接受的服务数据类型
* Accept-Language：告诉服务器客户端可以接受的语言类型（en_US  英文 zh_CN 中文）
* User-Agent：浏览器的信息
* Accept-Encoding：告诉服务器客户端可以接受的数据编码/压缩格式
* Host：请求的服务器ip和端口号
* Connection：告诉服务器请求连接如何处理（两种）
* 1、Keep-Alive ：告诉服务器回传数据不要马上关闭，保持一小时的链接
* 2、Closed：马上关闭



**4.4、POST请求**

* 1、请求行：
* (1) 请求的方式  (2) 请求的资源路径[+？+请求参数] (3)请求的协议的版本号 HTTP/1.1
* 2、请求头：
* key：value 组成  不同的键值对，表示不同的含义
* Accept：告诉服务器客户端可以接受的服务数据类型
* Accept-Language：告诉服务器客户端可以接受的语言类型（en_US  英文 zh_CN 中文）
* User-Agent：浏览器的信息
* Referer：表示请求发起时，浏览器地址栏中的地址（从哪来）
* Content-Type：表示发送的数据类型
* Content-Length：表示发送的数据的长度
* Cache-Control：表示如何控制缓存 no-cache表示不缓存
* **请求体到请求体之间存在：空行**
* 3、请求体：发送给服务器的数据



**4.5、哪些是GET请求，哪些是POST请求**

* **GET请求：**
* 1、form标签 method=get
* 2、a标签
* 3、link标签引入css
* 4、Script标签引入js文件
* 5、img标签引入图片
* 6、iframe引入html页面
* 7、在浏览器地址栏中输入地址后气敲回车
* **POST请求：**
* 1、form标签 method=post



**4.6、响应的HTTP协议格式**

* 1、响应行
* （1）响应的协议和版本号（2）响应状态码（3）响应状态描述符

* 2、响应头：key：value 不同的响应头有不同的含义
* **空行**
* 3、响应体：回传给客户端的数据



**4.7：常见的响应码说明**

* 200：表示请求成功
* 302：表示请求重定向
* 404：表示请求服务器已经收到，但是你要的数据不存在（请求地址错误）
* 500：表示服务器已经收到，但是服务器内部错误（代码错误）



**4.8：MIME请求**

* 概念：HTTP协议中的数据类型
* 类型格式：大类型/小类型 



**4.9、浏览器如何让查看HTTP协议**

* F12



---

## DAY3

**5.1、HttpServletRequest类**

* 作用：每次只要有请求进入Tomcat服务器，Tomcat服务器就会把请求过来的协议信息解析好封装到Request对象中，然后传递到service方法（doGet和doPost）中给我们使用，我们可以通过HttpServletReques对象，获取到所有请求的信息
* 常用方法：
* getRequestURI() ==>获取请求的资源路径
* getRequestURL() ==>获取请求的统一资源定位符（绝对路径）
* getRemoteHost()==>获取客户端的ip地址
* getHeader()==>获取请求头
* getParameter()==>获取请求的参数
* getParameterValues()==>获取请求的参数（多个值时使用）
* getMethod()==>获取请求的方式
* setAttribute(key,value)==>设置域数据
* getAttribute(key)==>获取域数据
* gerRequestDispatcher()==>获取请求转发对象

* ```java
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
                  // getRequestURI() ==>获取请求的资源路径
          System.out.println("URI："+req.getRequestURI());
  
                 // getRequestURL() ==>获取请求的统一资源定位符（绝对路径）
          System.out.println("URL: "+req.getRequestURL());
  
                 // getRemoteHost()==>获取客户端的ip地址
          /*
          在IDEA中，使用localhost访问时，得到的客户端ip地址是====>127.0.0.1
          在IDEA中，使用127.0.0.1访问时，得到的客户端ip地址是====>127.0.0.1
          在IDEA中，使用本机ip访问时，得到的客户端ip地址是====>本机ip地址
           */
          System.out.println("ip地址："+req.getRemoteHost());
  
                 // getHeader()==>获取请求头
          System.out.println("请求头："+req.getHeader("User-Agent"));
  
          //getMethod()==>获取请求的方式
          System.out.println("请求方式："+req.getMethod());
      }
  ```




**5.2、Servlet获取请求的参数值（获取客户端发送过来的参数）**

* ```java
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       //必须在获取请求前调用才有效，设置请求体的字符集为UTF-8，解决post请求的中文乱码问题    
          req.setCharacterEncoding("UTF-8");
          //获取请求参数
          String username = req.getParameter("username");
          String password = req.getParameter("password");
          String hobby[] = req.getParameterValues("hobby");
          System.out.println("用户名:"+username);
          System.out.println("密码:"+password);
          System.out.println("兴趣爱好:"+ Arrays.asList(hobby));
      }
  ```

* ```xml
  <!--配置form.xml文-->
  <form action="http://localhost:8080/Servlet-02/parameterServlet" method="get">
  用户名：<input type="text" name="username"> <br/>
  密码：<input type="password" name="password"> <br/>
      兴趣爱好：<input type="checkbox" name="hobby" value="cpp">C++ <br/>
      兴趣爱好：<input type="checkbox" name="hobby" value="java">Java  <br/>
      兴趣爱好：<input type="checkbox" name="hobby" value="js">JavaScript  <br/>
  <input type="submit">
  </form>
  ```



**5.3、请求转发**

* 概念：服务器收到请求后，从一个资源跳转到另外一个资源的操作叫做请求转发

*  ![image-20210721101516591](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210721101516591.png)

* ```java
  public class Servlet1 extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          //获取请求的参数
          String username = req.getParameter("username");
          System.out.println("servlet1中的参数："+username);
  
          //给材料盖章并传递到servlet2去查看
          req.setAttribute("key1","servlet1的章");
  
          //问路，servlet2怎么走
          /*
          请求转发必须以斜杠打头  / ====> http://ip:port/工程名，映射到IDEA的web目录
           */
          RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");
  
          //走向柜台二
                  requestDispatcher.forward(req,resp);
  
      }
  }
  
  public class Servlet2 extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          //获取请求的参数
          String username = req.getParameter("username");
          System.out.println("servlet2中的参数："+username);
  
          //查看柜台1是否盖章
          Object key1 = req.getAttribute("key1");
          System.out.println("柜台一是否有章 ："+key1);
  
          //处理自己的业务
          System.out.println("servlet2处理自己的业务");
      }
  }
  ```

* 

* **特点：**
* 浏览器地址栏没有变化
* 他们是一次请求
*  他们共享Request域中的数据（由servlet1传到servlet2）
* 可以转发到WEB-INF
* 不可以访问工程以外的资源



5.4、base标签的作用

