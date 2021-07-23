**1.1、Session**

* 概念：服务器端会话技术，在一次会话的多次请求间，共享数据，将数据保存在服务器端的对象中（HttpSession）

* 快速入门：HttpSession对象：Object  getAttribute(String name)

* void setAttribute(String name , Object value)

* void  removeAttribute(String name)

* ```java
  //SessionDemo1 
  protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                  //使用session共享数据
  
          //1.获取session
          HttpSession session=request.getSession();
  
          //2.存储数据
          session.setAttribute("msg","hello session");
          
      }
  //SessionDemo2
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //使用session共享数据
  
          //1.获取Httpsession对象
          HttpSession session=request.getSession();
  
          //2.存储数据
          Object msg = session.getAttribute("msg");
          System.out.println(msg);
    }
  ```



**1.1、Session原理**

* Session的是实现是依赖于cookie的
* ![image-20210722195726312](C:\Users\QHD\AppData\Roaming\Typora\typora-user-images\image-20210722195726312.png)



**1.2、Session的细节**

* 当客户端关闭后，服务器不关闭，两次获取的session默认情况下不是同一个，如果需要相同则可以创建cookie，键为JSESSION，设置最大存活时间，让cookie持久化保存

* ```java
  //期待客户端关闭后，session也能相同
          Cookie c=new Cookie("JSESSIONID",session.getId());
           c.setMaxAge(60*60);
             response.addCookie(c);
  ```

* 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？

* 不是同一个，但是要确保数据不丢失

* session的钝化：在服务器正常关闭之前将session对象系列化到硬盘上（序列化的过程）

* session的活化：在服务器启动后将session文件转化为内存中的session对象即可（反序列化的过程）

* session什么时候被销毁？

* 1.服务器关闭

* 2.session对象调用方法： invalidate()

* 3.session的默认失效时间为30分钟，可以选择性的配置修改< /session-config  >中的< / session-timeout>



**1.3、Session的特点**

* session用于存储一次会话的多次请求的数据，存在服务器端
* session可以存储任意类型，任意大小的数据
* session与cookie的区别：
* session存储数据在服务器端，cookie在客户端
* session没有数据大小限制，cookie有
* session数据安全，cookie相对不安全



---

**2.1、Filter ( 过滤器 ) **

* 概述：当访问服务器的资源时，过滤器可以将请求拦截下来，完成一些特殊的功能
* 作用：一般用于完成通用的操作。例如：登录验证、统一编码处理、敏感字符过滤......



**2.2、快速入门**

* 步骤：1.定义一个类实现Filter接口

* 2.复写方法

* 3.配置拦截路径

* ```java
  @WebFilter("/*") //访问所有资源之前，都会执行该过滤器
  public class FilterDemo1 implements Filter {
      @Override
      public void init(FilterConfig filterConfig) throws ServletException {
  
      }
      @Override
      public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
          System.out.println("FilterDemo1被执行了");
          //放行
          filterChain.doFilter(servletRequest,servletResponse);
      }
  
      @Override
      public void destroy() {
      }
  }
  ```



**2.3、过滤器细节**

* web,xml配置方式

* ```xml
  <filter>
      <filter-name>FilterDemo1</filter-name>
      <fileter-class>com.qin.fiter.FilterDemo1</fileter-class>
  </filter>
  <filter-mapping>
   <filter-name>FilterDemo1</filter-name> 
      <url-pattern>/*</url-pattern><!--此路径为拦截路径-->
  </filter-mapping>
  ```

* 过滤器执行流程

* ```java
  /*
  1.执行过滤器
  2.执行放行后的资源
  3.回来后执行放行代码后的代码
  */
  
  public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
          //对request对象的请求消息增强
  
          System.out.println("FilterDemo2执行了...");
  
         //放行
          chain.doFilter(req, resp);
          //对response对象的响应消息增强
          System.out.println("FilterDemo2回来了...");
      }
  ```

* 过滤器生命周期方法

* ```java
  @WebFilter("/*")
  public class FilterDemo3 implements Filter {
      /*
      在服务器关闭后，Filter对象被销毁，如果服务器是正常关闭，则会执行destroy方法，只执行一次，一般用于释放资源
       */
      public void destroy() {
          System.out.println("destroy....");
      }
      /*
      每一次请求被拦截资源时，会执行，执行多次
       */
      public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
          System.out.println("doFilter......");
          //放行
          chain.doFilter(req, resp);
      }
  /*
  在服务器启动后，会创建Filter对象然后调用init方法，只执行一次，一般用于加载资源
   */
      public void init(FilterConfig config) throws ServletException {
          System.out.println("init....");
      }
  }
  ```

* **过滤器配置详解**

* **拦截路径的配置：**

* 1.具体路径：/index.jsp   只有访问index.jsp资源时，过滤器才会执行           

* 2.拦截目录：/user/*       访问/user下的所有资源时，过滤器都会被执行     

* 3.后缀名拦截：*.jsp    访问所有jsp资源时，过滤器都会被执行  

* 4.拦截所有资源：/ *   访问所有资源时过滤器都会执行

* **拦截方式的配置：资源被访问的方式**

* **1.注解配置**：设置dispatcherTypes属性（5种取值）

* REQUEST：默认值，表示浏览器直接请求资源

* FORWARD：转发访问资源

* INCLUDE：包含访问资源

* ERROR：错误跳转资源

* ASYNC：异步访问资源

* **2.web.xml配置：**设置< dispatcher >< /dispatcher >标签，标签的取值与注解相同

* **过滤器链（配置多个过滤器）**

* **执行顺序：**如果有两个过滤器：过滤器1，过滤器2

* 过滤器1==>过滤器2==> 资源执行==> 过滤器2==>过滤器1

* **过滤器的先后顺序问题：**

* 1.注解配置的：按照类名的字符串比较规则比较，值小的先执行

* 例如：AFilter  和  BFilter ，AFilter就先执行了

* 2.web.xml配置的：< filter-mapping >谁定义在上边，谁就先执行



**2.4、增强对象的功能**：

* 装饰模式
* 代理模式：

1.概念：（1）真实对象，被代理的对象   （2）代理对象   （3）代理对象代理真实对象，达到增强真实对象功能的目的

2.实现方式：（1）静态代理：有一个类文件描述代理模式        （2）动态代理：在内存中形成代理类

3.动态代理实现步骤：

（1）代理对象和真实对象实现相同的接口

（2）使用代理对象=Proxy.newProxyInstance();

（3）使用代理对象调用方法

（4）增强方法

4.增强方式：

（1）：增强参数列表

（2）：增强返回值类型

（3）：增强方法体执行逻辑

```java
package com.qin.proxy;

public interface SaleComputer {
    public String sale(double money);
    public void show();
}
----------------------------
package com.qin.proxy;
/*
真实类
 */
public class Lenovo implements SaleComputer {
    @Override
    public String sale(double money) {
        System.out.println("花了"+money);
        return "电脑";
    }

    @Override
    public void show() {
        System.out.println("展示电脑");
    }
}
-------------------------------
package com.qin.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyText {
    public static void main(String[] args) {
        //创建真实对象
        Lenovo lenovo = new Lenovo();
        //动态代理增强Lenovo对象
        /*
        三个参数
        1.类加载器：真实对象.getClass().getClassLoader()
        2.接口数组：真实对象.getClass().getInterfaces()
        3.处理器：new InvocationHandler()
         */
        SaleComputer proxy_lenovo = (SaleComputer) Proxy.newProxyInstance(lenovo.getClass().getClassLoader(), lenovo.getClass().getInterfaces(), new InvocationHandler() {

            //代理逻辑来编写的方法，代理对象调用的所有方法都会执行触发该方法执行
            /*
            三个参数
            1.proxy：代理对象
            2.method：代理对象调用的方法，被封装为对象
            3.args：代理对象调用方法时，传递的实际参数
             */
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                // System.out.println("该方法执行了");

                //判断是否是sale方法
                if (method.getName().equals("sale")) {
                    //1.增强参数
                    double money = (double) args[0];
                    money = money * 0.85;
                    System.out.println("专车接送");
                    //使用真实对象调用方法
                    String obj = (String) method.invoke(lenovo, money);
                    System.out.println("免费送货");
                    //2.增强返回值类型
                    return obj + "_鼠标垫";
                } else {
                    Object obj = method.invoke(lenovo, args);
                    return obj;
                }


            }
        });
        String computer1 = proxy_lenovo.sale(8000);
        System.out.println(computer1);
        //proxy_lenovo.show();
    }
}    
```



---

**3.1、Listener( 监听器 )**

* 概念：三大组件之一

* 时间监听机制：事件，事件源（事件发生的地方），监听器（一个对象），注册监听（将事件，事件源，监听器绑定在一起，当事件源上发生某个事件后，执行监听器代码）

* ServletContextListener：

* void contextDestroyed(ServletContextEvent sce)：ServletContext对象被销毁之前会调用该方法

* void contextInitialized(ServletContextEvent sce)：ServletContext对象创建后会调用该方法

* 步骤

  ```java
  1.定义一个类实现ServletContextListener接口
  2.复写方法
  3.配置：：注解/web.xml
  web.xml：<listener>
  <listener-class>com.qin.listener.ListenerDemo1</listener-class>
  </listener>
  注解配置：@WebListener即可
  ```

