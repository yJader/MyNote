# 7. Servlet



## 7.1 什么是Servlet?

1. Servlet是JavaEE规范之一。规范就是接口 
2. Servlet就是JavaWeb三大组件之一。三大组件分别是：`Servlet程序`，`Filter过滤器`，`Listener监听器`。 
3. Servlet是运行在服务器上的一个java程序，它可以接收客户端发送过来的请求，并响应数据给客户端。

## 7.2 Tomcat整合servlet中容易出现的问题

1.  部署了tomcat之后实现Servlet接口报错，提示找不到接口，导入import javax.servlet.*;也没有用。 解决方法： file----->Project Structure----->Libraries ------>点击➕选择java-------->选择tomcat安装目录------->选择lib文件夹 ------->选择servlet-api.jar和jsp-api.jsp---->点击ok------->点击apply ------->选择Artifacts ----->选择WEB-INF点击创建文件夹的按钮创建文件夹lib ------->点击lib点击➕-------->选择Library Files ------>选择刚刚在Libraries中添加的jar文件----->点击apply  
2.  注意：将tomcat部署在web项目之后 file----->Project Structure----->Project Project SDK和Project language level要选择对应的版本 SDK选1.8Project language level就要选8，否则tomcat就会启动失败。 

## 7.3 手动实现Servlet程序

1. 编写一个类去实现Servlet接口
2. 实现service方法, 处理请求, 并响应数据
3. 到web.xml中去配置servlet程序的访问地址

**实现Servlet接口**

```java
package com.company.test;

import javax.servlet.*;
import java.io.IOException;

public class servletTest implements Servlet {
   
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {}

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    /*
		service方法是专门用来处理请求和响应的
    */
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("Servlet成功运行！");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {}
}
```

**web.xml**

```html
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!-- servlet标签给Tomcat配置Servlet程序-->
    <servlet>
        <!-- servlet-name标签: Servlet程序起一个别名-->
        <servlet-name>HelloServlet</servlet-name>
        <!-- servlet-class标签: Servlet程序的全类名-->
        <servlet-class>servlets.HelloServlet</servlet-class>
    </servlet>

    <!-- servlet-mapping标签: 给servlet程序配置访问地址-->
    <servlet-mapping>
        <!--servlet-name标签: 告诉服务器, 当前配置的地址是给哪一个Servlet程序使用-->
        <servlet-name>HelloServlet</servlet-name>
        <!-- url-pattern标签: 配置访问地址 (分配访问地址)
            /: 斜杠在服务器解析的时候, 表示地址为: http://ip:port/工程路径 <br/>
            /hello: 表示地址为: http://ip:port/工程路径/hello
        -->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

* servlet标签: 给Tomcat配置Servlet程序
  * servlet-name标签: Servlet程序起一个**别名**
  * servlet-class标签: Servlet程序的**全类名**
* servlet-mapping标签: 给servlet程序配置访问地址
  * servlet-name标签: 告诉服务器, 当前配置的地址是给哪一个Servlet程序使用
  * **url-pattern标签**: 配置访问地址 <font color='orange'>(自己分配访问地址, 尽量和servlet程序有联系)</font>
    * /: 斜杠在服务器解析的时候, 表示地址为: http://ip:port/工程路径 <br/>
      /hello: 表示地址为: http://ip:port/工程路径/hello

**常见错误:**

1. url-pattern 中配置的路径没有==以斜杠打头== 
2. servlet-name 配置的值不存在 
3. servlet-class 标签的全类名配置错误

## 7.4 url地址到Servlet程序的访问

![image-20220913213558355](media/image-20220913213558355.png)

## 7.5 Servlet的生命周期

1.  执行Servlet构造器方法  
2.  执行init初始化方法 第一 二步，是在第一次访问的时候创建Servlet程序会调用，说明这个程序是单例的。  
3.  执行service方法 第三步：每次访问都会调用  
4.  执行destory销毁方法 第四步：在web工程停止的时候调用 

## 7.6 Servlet的请求分发处理

GET和POST请求的分发处理

```java
package com.company.test;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

/**
 * ClassName: servletTest
 * Description:
 * date: 2021/12/1 9:12
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class servletTest implements Servlet {
   
    public servletTest() {
        System.out.println("1 构造器方法");
    }

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("2 初始化方法");
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }


    /*
    * @Description: service方法是专门用来处理请求和响应的
    * @author: zhangyingying
    * @date: 2021/12/1 10:42
    * @Param: servletRequest
    * @Param: servletResponse
    * @return:void
    */
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
   

        //类型转换（因为它有getMethod（）方法）
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        System.out.println("Servlet成功运行！");
        System.out.println("3 service请求");
        String method = httpServletRequest.getMethod();
        if("GET".equals(method)){
   
            doGet();
        }else if("POST".equals(method)){
   
            doPost();
        }
    }

    private void doPost() {
        System.out.println("get请求");
    }

    private void doGet() {
        System.out.println("post请求");
    }

    @Override
    public String getServletInfo() {
   
        return null;
    }

    @Override
    public void destroy() {
   
        System.out.println("4 destroy销毁");
    }
}
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
<form action="http://localhost:8080/p2_servlet/hello" method="post">
    <!-- 或
	<form action="hello" method="get">
    -->
  <input type="submit"/>
</form>
</body>
</html>
```

*  注: 路径要么写全, 要么使用相对路径, 使用绝对路径"/hello"将会404

## 7.7 通过继承HttpServlet实现Servlet程序

一般在实际项目开发中，都是使用继承 HttpServlet 类的方式去实现 Servlet 程序。

1 编写一个类去继承 HttpServlet 类

2 根据业务需要重写 doGet 或 doPost 方法

3 到 web.xml 中的配置 Servlet 程序的访问地址

**编写一个类去继承 HttpServlet 类 **

```java
package com.company.test;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * ClassName: HttpServletTest
 * Description:
 * date: 2021/12/1 14:08
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class HttpServletTest extends HttpServlet {
    /*
    * @Description:doGet:在get请求的时候调用
    * @author: zhangyingying
    * @date: 2021/12/1 14:11
    * @param:
    * @Param: req
    * @Param: resp
    * @return:void
    */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        super.doGet(req, resp);
        System.out.println("HttpServletTest的doGet方法");
    }

    /*
    * @Description: doPost:在post请求的时候调用
    * @author: zhangyingying
    * @date: 2021/12/1 14:13
    * @param:
    * @Param: req
    * @Param: resp
    * @return:void
    */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        super.doPost(req, resp);
        System.out.println("HttpServletTest的doPost方法");
    }
}
```

**页面**

```java
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
<form action="hello2" method="post">
  <input type="submit"/>
</form>
</body>
</html>
```

**配置HttpServlet程序访问地址**

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    
    <servlet>
        <servlet-name>HttpServletTest</servlet-name>
        <servlet-class>com.company.test.HttpServletTest</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HttpServletTest</servlet-name>
        <url-pattern>/hello2</url-pattern>
    </servlet-mapping>
</web-app>
```

## 7.8 IDEA菜单生成Servlet程序

* 右键要创建Servlet包类的包，选择new选择Servlet，不要勾选Create Java EE 6+ annotated class，填写类名之后点击确定

* 如果右键没有Servlet选项：file-----&gt;Project Structure-----&gt;Facets-----&gt;SourceRoots下的路径前打勾-----&gt;apply-----&gt;ok

  ![image-20220913225408817](media/image-20220913225408817.png)

```java
package servlets; /**
 * @Author yJade
 * @Date 2022-09-13 22:58
 * @Package ${PACKAGE_NAME}
 */

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.IOException;

@WebServlet(name = "IdeaCreateServletTest", value = "/IdeaCreateServletTest")
public class IdeaCreateServletTest extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }
}
```

* Servlet 3.0 (即Tomcat 7.0)后，官方推荐使用@WebServlet方式配置, 而不是使用web.xml

## 7.9 Servlet的继承体系

<img src="media/image-20220913232644615.png" alt="image-20220913232644615" style="zoom:50%;" />

* **javax.servlet: `Interface Servlet`**：Servlet接口，只是负责定义Servlet程序的访问规范。

* **javax.servlet: `Class GenericServlet`（实现上面的接口）**： GenericServlet类实现了Servlet接口，做了很多空实现。并有一个ServletConfig类的引用，并对**ServletConfig**的使用做了一些方法。

* **javax.servlet.http:`Class HttpServlet`（继承上面的类）**：HttpServlet抽象类实现了service()方法，并实现了请求的分发处理

  * String method = req.getMethod(); 

  * doGet(), doPost() **只负责抛异常**，并不支持GET/POST请求

* **自定义的Servlet程序（继承上面的类）**：我们只需要根据自己的业务需要重写doGet()和doPost()方法即可。

## 7.10 ServletConfig类

ServletConfig类从类名上看，就知道是Servlet程序的配置信息类。

`Servlet程序`和`ServletConfig对象`都是由Tomcat负责创建，我们负责使用

Servlet程序默认是第一次访问的时候创建的，ServletConfig是每个Servlet程序创建时就创建一个ServletConfig对象。

### 7.10.1 ServletConfig类的三大作用

1. 可以获取Servlet程序的别名servlet-name的值 
2. 获取初始化参数`init-param` 
3. 获取ServletContext对象

**web.xml配置信息**

```html
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
             
    <!--servlet标签给Tomcat配置Servlet程序-->
    <servlet>
        <!--servlet-name标签 Servlet程序起一个别名（一般是类名）-->
        <servlet-name>servletTest</servlet-name>
        <!--servlet-class是Servlet程序的全类名-->
        <servlet-class>com.company.test.servletTest</servlet-class>
            
        <!--init-param是初始化参数-->
        <init-param>
            <param-name>username</param-name>
            <param-value>root</param-value>
        </init-param>
        <init-param>
            <!--参数名-->
            <param-name>url</param-name>
            <!--参数值-->
            <param-value>jdbc:mysql://localhost:3306/test</param-value>
        </init-param>
    </servlet>
            
    <!--servlet-mapping标签给servlet程序配置访问地址-->
    <servlet-mapping>
        <!--servlet-name标签的作用是告诉服务器，我当前配置的地址给哪个Servlet程序使用-->
        <servlet-name>servletTest</servlet-name>
        <!--
            url-pattern标签配置访问地址
            /斜杠在服务器解析的时候，表示地址为：http://ip:port/工程路径
            /hello表示地址为：http://ip:port/工程路径/hello
        -->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

```java
package com.company.test;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

/**
 * ClassName: servletTest
 * Description:
 * date: 2021/12/1 9:12
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class servletTest implements Servlet {
   
    public servletTest() {
   
        System.out.println("1 构造器方法");
    }

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
   
        System.out.println("2 初始化方法");
        //1. 可以获取Servlet程序的别名servlet-name的值
        System.out.println("servletTest程序的别名是：" + servletConfig.getServletName());
        //2. 获取初始化参数init-param
        System.out.println("初始化参数username的值是：" + servletConfig.getInitParameter("username"));
        //3. 获取ServletContext对象
        System.out.println(servletConfig.getServletContext());

    }

    @Override
    public ServletConfig getServletConfig() {
   
        return null;
    }


    /*
    * @Description: service方法是专门用来处理请求和响应的
    * @author: zhangyingying
    * @date: 2021/12/1 10:42
    * @Param: servletRequest
    * @Param: servletResponse
    * @return:void
    */
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
   

        //类型转换（因为它有getMethod（）方法）
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        System.out.println("Servlet成功运行！");
        System.out.println("3 service请求");
        String method = httpServletRequest.getMethod();
        if("GET".equals(method)){
   
            doGet();
        }else if("POST".equals(method)){
   
            doPost();
        }
    }

    private void doPost() {
        System.out.println("get请求");
    }

    private void doGet() {
        System.out.println("post请求");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("4 destroy销毁");
    }
}
```

重写init方法里面一定要调用父类的init(ServletConfig)操作：super.init(config);

## 7.11 ServletContext类

### 7.11.1 什么是ServletContext?

1. ServletContext是一个接口，它表示Servlet上下文对象 
2. 一个web工程，只有一个ServletContext对象实例 
3. ServletContext对象是一个域对象

**域对象：**是可以像map一样存取数据的对象，这里的域指的是存取数据的操作范围。整个web工程

 存数据 取数据 删除数据

Map put() get() remove()

域对象 setAttribute() getAttribute() removeAttribute()

### 7.11.2 ServletContext类的四个作用

1.  获取web.xml中配置的上下文参数context-param  
2.  获取当前的工程路径，格式：/工程路径  
3.  获取工程部署后在服务器硬盘上的绝对路径  
4.  Servlet在wbe工程部署启动的时候创建。在web程停止的时候销毁。 <pre><code class="prism language-java"><span class="token keyword">package</span> com<span class="token punctuation">.</span>company<span class="token punctuation">.</span>test<span class="token punctuation">;</span> <span class="token comment">/**
 * ClassName: ${NAME}
 * Description:
 * date: 2021/12/1 17:16
 *
 * @author zhangyingying
 * @version
 * @since JDK 1.8
 */</span>

<span class="token keyword">import</span> javax<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>*<span class="token punctuation">;</span>
<span class="token keyword">import</span> javax<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>http<span class="token punctuation">.</span>*<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>io<span class="token punctuation">.</span>IOException<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ContextServlet</span> <span class="token keyword">extends</span> <span class="token class-name">HttpServlet</span> <span class="token punctuation">{
     
    <!-- --></span>
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">protected</span> <span class="token keyword">void</span> <span class="token function">doGet</span><span class="token punctuation">(</span>HttpServletRequest request<span class="token punctuation">,</span> HttpServletResponse response<span class="token punctuation">)</span> <span class="token keyword">throws</span> ServletException<span class="token punctuation">,</span> IOException <span class="token punctuation">{
     
    <!-- --></span>
        <span class="token comment">//获取web.xml中配置的上下文参数context-param</span>
        ServletContext servletContext <span class="token operator">=</span> <span class="token function">getServletConfig</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getServletContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        String username <span class="token operator">=</span> servletContext<span class="token punctuation">.</span><span class="token function">getInitParameter</span><span class="token punctuation">(</span><span class="token string">"username"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>username<span class="token punctuation">)</span><span class="token punctuation">;</span>
    
        <span class="token comment">//获取当前的工程路径，格式：/工程路径</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"当前工程路径："</span> <span class="token operator">+</span> servletContext<span class="token punctuation">.</span><span class="token function">getContextPath</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//获取工程部署后在服务器硬盘上的绝对路径</span>
        <span class="token comment">//斜杠被服务器解析地址为:http://ip:port/工程名/    映射到IDEA代码的web目录</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"工程部署的绝对路径："</span> <span class="token operator">+</span> servletContext<span class="token punctuation">.</span><span class="token function">getRealPath</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"工程下CSS目录的绝对路径："</span> <span class="token operator">+</span> servletContext<span class="token punctuation">.</span><span class="token function">getRealPath</span><span class="token punctuation">(</span><span class="token string">"/css"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"工程下imgs目录1.jpg的绝对路径："</span> <span class="token operator">+</span> servletContext<span class="token punctuation">.</span><span class="token function">getRealPath</span><span class="token punctuation">(</span><span class="token string">"/imgs/1.jpg"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">protected</span> <span class="token keyword">void</span> <span class="token function">doPost</span><span class="token punctuation">(</span>HttpServletRequest request<span class="token punctuation">,</span> HttpServletResponse response<span class="token punctuation">)</span> <span class="token keyword">throws</span> ServletException<span class="token punctuation">,</span> IOException <span class="token punctuation">{
     
    <!-- --></span>
    
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre> web.xml文件 <!--context-param是上下文参数（它属于整个web工程）-->
    <context-param>
        <param-name>username</param-name>
        <param-value>root</param-value>
    </context-param>
    <!--context-param是上下文参数（它属于整个web工程）-->
    <context-param>
        <param-name>password</param-name>
        <param-value>123456</param-value>
    </context-param>  
5.  像Map一样存取数据 

```java
package com.company.test; 
/**
 * ClassName: ${NAME}
 * Description:
 * date: 2021/12/1 18:19
 *
 * @author zhangyingying
 * @version
 * @since JDK 1.8
 */

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class ContextServlet2 extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        ServletContext servletContext = getServletContext();

        System.out.println("Context1中获取域数据key1值是：" + servletContext.getAttribute("key1"));
    }
}
```

```java
package com.company.test; /**
 * ClassName: ${NAME}
 * Description:
 * date: 2021/12/1 18:19
 *
 * @author zhangyingying
 * @version
 * @since JDK 1.8
 */

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class ContextServlet1 extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        ServletContext servletContext = getServletContext();
        System.out.println("保存之前：Context1获取key1的值是：" + servletContext.getAttribute("key1"));
        servletContext.setAttribute("key1","value1");
        System.out.println("Context1中获取域数据key1值是：" + servletContext.getAttribute("key1"));
        System.out.println("Context1中获取域数据key1值是：" + servletContext.getAttribute("key1"));
        System.out.println("Context1中获取域数据key1值是：" + servletContext.getAttribute("key1"));
    }

}
```

## 7.12 Http协议

**什么是http协议？**

什么是协议？

 协议是指双方或多方相互约定好，大家都要遵守的规则

HTTP协议，是指客户端和服务器之间通信时，发送的数据和需要遵守的规则

HTTP协议中的数据又叫报文。

### 7.12.1 请求的HTTP协议格式

客户端给服务器发送数据叫请求

服务器给客户端回传数据叫响应

请求又分为GET和POST两种

**GET请求**

1.  请求行 
 <ul> 
  1. 请求方式 GET 
  1. 请求的资源路径[+?+请求参数] 
  1. 请求的协议版本号 HTTP/1.1 
 </ul>  
5.  请求头 key : value组成，不同的键值对，表示不同的含义。 


Accept:表示 客户端可以接收的数据类型

Accept-Language:表示客户端可以接收的语言类型

 zh_CN 中文

 en_US 英文

User-Agent:表示浏览器的信息

Accept-Encoding:告诉服务器，客户端可以接收的数据编码（压缩）格式

Host:表示请求的服务器ip和端口号

Connection:告诉服务器请求连接如何处理

 Keep-Alive 告诉服务器回传数据不要马上关闭，保持一小段时间的连接

 Closed 马上关闭

**POST请求**

1.  请求行 
 <ul> 
  1. 请求的方式 POST 
  1. 请求的资源路径[+?+请求参数] 
  1. 请求的协议版本号 HTTP/1.1 
 </ul>  
5.  请求头 key : value 不同的请求头，有不同的含义 空行  
6.  请求体====》就是发送给服务器的数据 


Accept:表示 客户端可以接收的数据类型

Accept-Language:表示客户端可以接收的语言类型

Referer:表示请求发起时，浏览器地址栏中的地址（从哪来）

User-Agent:表示浏览器的信息

Content-Type:发送的数据的类型

 application/x-www-form-urlencoded：表示提交的数据格式是name=value&amp;name=value，然后对其进行url编码，url编码是把非英文内容转换为：%xxx%xxx

 multipart/form-data：表示以多段的形式提交数据给服务器（以流的形式提交，用于上传）

Content-Length：表示发送的数据的长度

Connection:告诉服务器请求连接如何处理

 Keep-Alive 告诉服务器回传数据不要马上关闭，保持一小段时间的连接

 Closed 马上关闭

Cache-Control:表示如何控制缓存no-cache不缓存

### 7.12.2 常用的请求头说明

**Accept：表示客户端可以接收的数据类型**

**Accept-Language：表示客户端可以接收的语言类型**

**User-Agent：表示客户端浏览器的信息**

**Host：表示请求时的服务器ip和端口号**

### 7.12.3 哪些时GET请求，哪些是POST请求

**GET请求：**

- form标签method=get 
- a标签 
- link标签引入css 
- Script标签引入js文件 
- img标签引入图片 
- iframe引入html页面 
- 在浏览器地址栏中输入地址后敲回车

**POST请求**

- form标签 method=post

### 7.12.4 响应的HTTP协议格式

1.  响应行 
 <ul> 
  1. 响应的协议和版本号 HTTP/1.1 
  1. 响应状态码 200 
  1. 响应状态描述符 OK 
 </ul>  
5.  响应头 key:value 不同的响应头，有其不同的含义  
6.  空行  
7.  响应体 ----------->就是回传给客户端的数据 

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-WXMKQ4W0-1647830664385)(file:///C:/Users/ZHANGY~1/AppData/Local/Temp/企业微信截图_16384115578355.png)]

Server:表示服务器的信息

Content-Type:表示响应体的数据类型

Context-Length:响应体的长度

Data:请求响应的时间（格林时间）

### 7.12.5 常用的响应码说明

200 表示请求成功

302 表示请求重定向

404 表示服务器已经收到了请求，但是数据不存在（请求地址需错误）

500 表示服务器已经收到了请求，但是服务器内部错误（代码错误）

### 7.12.6 MIME类型说明

MIME是HTTP协议中数据类型

MIME的英文全称是”[ Multipurpose Internet Mail Extension]“意为多用途网际邮件扩充服务。MIME类型是"大类型/小类型"，并于某一种文件的扩展名相对应。


## 7.13 HttpServletRequest类

### 7.13.1 HttpServletRequest类有什么作用？

每次只要有请求进入Tomcat服务器，Tomcat服务器就会把请求过来的HTTP协议信息解析封装到Request对象中。然后传递到service方法（doGet和doPost）中给我们使用。我们可以通过HttpServletRequest对象，获取到所有请求的信息。

### 7.13.2 HttpServletRequest类的常用方法


**测试代码**

```java
package com.company.httpServletRequestTest;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;


/**
 * ClassName: HttpServletRequestTest
 * Description:
 * date: 2021/12/2 11:45
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class HttpServletRequestTest extends HttpServlet {
   

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        //getRequestURI()获取请求的资源路径
        System.out.println("URI====>" + req.getRequestURI());
        //getRequestURL()获取统一资源定位符（绝对路径）
        System.out.println("URL=====>" + req.getRequestURL());
        //getRemoteHost()获取客户端的ip地址
        /*
         * 在 IDEA 中，使用 localhost 访问时，得到的客户端 ip 地址是 ===>>> 127.0.0.1<br/>
         * 在 IDEA 中，使用 127.0.0.1 访问时，得到的客户端 ip 地址是 ===>>> 127.0.0.1<br/>
         * 在 IDEA 中，使用 真实 ip 访问时，得到的客户端 ip 地址是 ===>>> 真实的客户端 ip 地址<br/>
         */
        System.out.println("客户端的ip地址" + req.getRemoteHost());
        //getHeader()获取请求头
        System.out.println("获取请求头" +req.getHeader("User-Agent") );
        //getMethod()获取请求方式GET或POST
        System.out.println("获取请求方式" + req.getMethod());
    }
}
```

**运行结果**

```java
URI====>/httpServletRequest
URL=====>http://localhost:8080/httpServletRequest
客户端的ip地址0:0:0:0:0:0:0:1
获取请求头Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
获取请求方式GET
```

**获取请求的参数值**

```java
//getParameter()获取请求的参数
//getParameterValues()获取请求的参数（多个值的时候使用）
package com.company.httpServletRequestTest;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException
import java.util.Arrays;
/**
 * ClassName: ParameterServlet
 * Description:
 * date: 2021/12/2 16:23
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class ParameterServlet extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        //获取请求参数
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        String[] hobby = req.getParameterValues("hobby");
        System.out.println(username + "的密码是：" + password);
        System.out.println("兴趣爱好：" + Arrays.asList(hobby));
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        super.doPost(req, resp);
    }
}
```

**前端页面**

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>表单</title>
</head>
<body>
<form action="http://localhost:8080/parameterServlet" method="get">
    用户名：<input type="text" name="username"/><br/>
    密码：<input type="password" name="password"><br/>
    兴趣爱好：
    <input type="checkbox" name="hobby" value="cpp">C++
    <input type="checkbox" name="hobby" value="Python">python
    <input type="checkbox" name="hobby" value="java">java
    <input type="checkbox" name="hobby" value="js">js
    <br/>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

**web.xml配置**

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>ParameterServlet</servlet-name>
        <servlet-class>com.company.httpServletRequestTest.ParameterServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ParameterServlet</servlet-name>
        <url-pattern>/parameterServlet</url-pattern>
    </servlet-mapping>
</web-app>
```

### 7.13.3 doGet请求的中文乱码解决

```java
//获取请求参数
String username = req.getParameter("username");
//先以iso8859-1进行编码
//再以utf-8进行解码
username = new String(username.getBytes(StandardCharsets.UTF_8));
```

### 7.13.4 Post请求的中文乱码解决

```java
//设置请求体的字符集为utf-8，来解决post请求的中文乱码问题
//要在获取请求之前调用才有效，因此这句代码要放在所有获取请求的语句之前。
req.setCharacterEncoding("UTF-8");
```

### 7.13.5 请求的转发

**什么是请求的转发？**

请求的转发是指，服务器收到请求之后，从一次资源跳转到另一个资源的操作。

**servlet1**

```java
package com.company.httpServletRequestTest; /**
 * ClassName: ${NAME}
 * Description:
 * date: 2021/12/2 17:31
 *
 * @author zhangyingying
 * @version
 * @since JDK 1.8
 */

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;


public class Servlet1 extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        String username = request.getParameter("username");
        request.setAttribute("key1","材料带齐了，给个证明");
        RequestDispatcher requestDispatcher = request.getRequestDispatcher("/servlet2");
        requestDispatcher.forward(request,response);
    }

}
```

**servlet2**

```java
package com.company.httpServletRequestTest; /**
 * ClassName: ${NAME}
 * Description:
 * date: 2021/12/2 17:31
 *
 * @author zhangyingying
 * @version
 * @since JDK 1.8
 */

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;


public class Servlet2 extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        String username = request.getParameter("username");
        Object key1 = request.getAttribute("key1");
        System.out.println("servlet1的证明带来了" + key1);
        System.out.println("继续在柜台2办理业务");
    }

}
```

**特点：**

-  浏览器访问地址没有改变  
-  只执行一次请求  
-  两个servlet服务共享Request域中的数据  
-  可以转发到WEB-INF目录下  
-  不可以访问工程以外的资源  
-  浏览器地址不可以直接访问WEB-INF目录下的资源，必须通过转发才可以实现。 [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-TUO1kh7j-1647830664385)(file:///C:/Users/ZHANGY~1/AppData/Local/Temp/企业微信截图_16384395307497.png)] 

### 7.13.6 base标签的作用

**index.html**

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
这是index.html页面<br/>
<!--默认地址会到web目录下，因此页面跳转可以从web目录以下开始写-->
<a href="a/b/a.html">跳转到a.html页面</a><br/>
<!--请求转发的页面要写请求转发web文件中配置的地址-->
<a href="http://localhost:8080/09-servlet/forwardC">请求转发到a.html页面</a>
</body>
</html>
```

**a.html页面**

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
这是a下的b下的a.html<br/>
<a href="../../index.html">跳转到首页</a>
</body>
</html>
```

**请求转发servlet**

```java
package com.company.httpServletRequestTest; /**
 * ClassName: ${NAME}
 * Description:
 * date: 2021/12/3 9:20
 *
 * @author zhangyingying
 * @version
 * @since JDK 1.8
 */

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class ForwardC extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        System.out.println("经过了Servlet程序");

        //请求到新的服务
        RequestDispatcher requestDispatcher = request.getRequestDispatcher("/a/b/a.html");
        //携带数据转发到新的服务
        requestDispatcher.forward(request,response);
    }

}
```

**web.xml配置**

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>ForwardC</servlet-name>
        <servlet-class>com.company.httpServletRequestTest.ForwardC</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ForwardC</servlet-name>
        <url-pattern>/forwardC</url-pattern>
    </servlet-mapping>
</web-app>
```

**当我们点击a标签进行跳转的时候**

```java
浏览器地址栏中的地址是
http://localhost:8080/09_servlet/a/b/a.html
跳转回去的路径是：../../index.html
所有相对路径在工作的时候都会参照当前浏览器地址栏中的地址来进行跳转。
得到的地址是：http://localhost:8080/index.html
==得到的地址是正确的跳转路径==
```

**当我们使用请求转发进行跳转的时候**

```java
浏览器地址栏中的地址是http://localhost:8080/09_servlet/forwardC
跳转回去的路径是：../../index.html
所有相对路径在工作的时候都会参照当前浏览器地址栏中的地址来进行跳转。
得到的地址是：http://localhost:8080/index.html
==得到的地址是错误的跳转路径==
```

base标签可以设置当前页面中所有相对路径工作时，参照哪个路径来进行跳转

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!--
		base标签设置页面相对路径工作时参照的地址
		href属性就是参数的地址值
	-->
    <base href="http://localhost:8080/09-servlet/a/b/a.html">
</head>
<body>
这是a下的b下的a.html<br/>
<a href="../../index.html">跳转到首页</a>
</body>
</html>
```

### 7.13.7 WEB中的相对路径和绝对路径

在javaWeb中路径分为相对路径和绝对路径两种：

相对路径是：

 . 表示当前目录

 … 表示上一级目录

 资源名 表示当前目录/资源名

绝对路径：

 http://ip:port/工程路径/资源路径

在实际开发中路径都使用绝对路径，不简单的使用相对路径。

1 绝对路径

2 base+相对路径

### 7.13.8 web中/（斜杠）的不同意义

在web中/是一种绝对路径。

/斜杠 如果被浏览器解析，得到的地址是：http://ip:port/

 <a href = "/">斜杠</a>

/斜杠 如果被服务器解析，得到的地址是：http://ip:port/工程名

 1 <url-pattern>/servlet</url-apttern>

 2 servletContext.getRealPath("/");

 3 request.getRequestDispatcher("/");

特殊情况：response.sendRediect("/");把斜杠发送给浏览器解析得到http://ip:port/

## 7.14 HttpServletResponse类

### 7.14.1 HttpServletResponse类的作用

HttpServletResponse类和HttpServletRequest类一样。每一次请求进来，Tomcat服务器都会创建一个Response对象传递给Servlet程序去使用。

HttpServletRequest表示请求过来的信息，HttpServletResponese表示所有响应的信息。

我们如果需要设置返回给客户端的信息，都可以通过HttpServletResponse对象来进行设置。

### 7.14.2 两个输出流的说明

 字节流：getOutpurStream(); 常用于下载（传递二进制数据）

 字符流：getWriter(); 常用于回传字符（常用）

两个流同时只能使用一个。

使用了字节流，就不能使用字符流；使用了字符流，就不能使用字节流。否则就会报错。

**以下这种写法就会报错**

```java
package com.company.httpServletRequestTest; /**
 * ClassName: ${NAME}
 * Description:
 * date: 2021/12/3 14:21
 *
 * @author zhangyingying
 * @version
 * @since JDK 1.8
 */

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class ResponseIOServlet extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        //不能同时使用两个流
        response.getWriter();
        response.getOutputStream();
    }
}
```

### 7.14.3 如何往客户端回传数据？

要求：往客户端回传字符串数据

```java
package com.company.httpServletRequestTest;
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class ResponseIOServlet extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        //要求：往客户端回传字符串
        PrintWriter responseWriter = response.getWriter();
        responseWriter.write("response's content!!!");
    }
}
```

### 7.14.4 Servlet解决响应的乱码问题

**第一种方法：**

```java
package com.company.httpServletRequestTest; /**
 * ClassName: ${NAME}
 * Description:
 * date: 2021/12/3 14:21
 *
 * @author zhangyingying
 * @version
 * @since JDK 1.8
 */

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class ResponseIOServlet extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        //获取服务器默认字符集:ISO-8859-1
        String characterEncoding = response.getCharacterEncoding();
        System.out.println(characterEncoding);
        //设置服务器字符集为UTF-8
        response.setCharacterEncoding("UTF-8");
        //设置服务器的字符集不足以解决问题，浏览器的字符集不是utf-8
        response.setHeader("Content-Type","text/html;charset=UTF-8");
        //要求：往客户端回传字符串
        PrintWriter responseWriter = response.getWriter();
        responseWriter.write("张三很牛！");
    }
}
```

**第二个方法：**

 这种方法设置编码类型要在创建流对象之前设置，在流对象创建之后设置无效。

```java
package com.company.httpServletRequestTest; /**
 * ClassName: ${NAME}
 * Description:
 * date: 2021/12/3 14:21
 *
 * @author zhangyingying
 * @version
 * @since JDK 1.8
 */

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class ResponseIOServlet extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        //获取服务器默认字符集:ISO-8859-1
        String characterEncoding = response.getCharacterEncoding();
        System.out.println(characterEncoding);
        //直接设置Content-Type的编码类型
        response.setContentType("text/html; charset=UTF-8");
        //要求：往客户端回传字符串
        PrintWriter responseWriter = response.getWriter();
        responseWriter.write("张三很牛！");
    }
}
```

### 7.14.4 Servlet请求重定向

请求重定向是指，客户端给服务器 发请求，然后服务器告诉客户端，我给你一个新地址，你去新地址访问（之前的地址可能已经废弃）。

**请求重定向的示意图。**

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-TTbCdtSd-1647830664386)(file:///C:/Users/ZHANGY~1/AppData/Local/Temp/企业微信截图_16385189771664.png)]

**请求重定向的特点：**

- 浏览器地址栏会发生变化 
- 两次请求 
- 不共享Request域中的数据 
- 不能访问WEB-INF下的资源 
- 能访问当前工程以外的资源

重定向的第一种方法

**重定向服务1**

```java
package com.company.httpServletRequestTest; 

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.IOException;

public class Response1 extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        //设置响应状态码302，表示重定向
        response.setStatus(302);
        //设置响应头，说明新的访问地址
        response.setHeader("Location","http://localhost:8080/09-servlet/response2");
    }
}
```

**重定向服务2**

```java
package com.company.httpServletRequestTest; 

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class Response2 extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        response.setContentType("text/html; charset=utf-8");
        String str = "重定向访问到了";
        response.getWriter().write(str);
    }
}
```

重定向的第二种方法(推荐使用)

```java
package com.company.httpServletRequestTest;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.IOException;

public class Response1 extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
        //请求重定向第二种方法
       response.sendRedirect("http://localhost:8080/09-servlet/response2");
    }
}
```

# 8. jsp

## 8.1 什么是jsp？有什么用

jsp的全称是java server pages。 java的服务器页面。

jsp的主要作用是代替servlet程序回传html页面的数据。

因为Servlet程序回传html页面数据是一件非常繁琐的事情。开发成本和维护成本都极高。

```java
package com.company.controller;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

/**
 * ClassName: Pring
 * Description:
 * date: 2021/12/8 16:24
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class PrintHtml extends HttpServlet {
   

    //通过响应的回传流回传html页面数据

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter writer = resp.getWriter();
        writer.write("<!DOCTYPE html>\r\n");
        writer.write("   <html lang=\"en\">\r\n");
        writer.write("   <head>\r\n");
        writer.write("      <meta charset=\"UTF-8\">\r\n");
        writer.write("      <title>Title</title>\r\n");
        writer.write("   </head>\r\n");
        writer.write("   <body>\r\n");
        writer.write("       这是hmtl页面数据\r\n");
        writer.write("   </body>\r\n");
        writer.write("   </html>\r\n");

    }

}
```

**小结**

1.  如何创建jsp的页面？  
2.  jsp页面html一样，都是存放在web目录下。访问也跟访问html页面一样。 比如： 在web目录下有如下文件： web目录 a.html页面 访问地址是--------------------------->http://ip:port/工程路径名/a.html a.jsp页面 访问地址是---------------------------->http://ip:port/工程路径/a.jsp 8.2 jsp的本质是什么？ jsp页面本质上是一个servlet程序 当我们第一次访问jsp页面的时候。Tomcat服务器会帮我们把jsp页面翻译成一个java源文件。并且对它进行编译成为.class字节码程序。我们打开java源文件不难发现HttpJspBase类，直接地继承了HttpServlet类。 也就是说。jsp翻译出来的Java类，间接的继承了HttpServlet类。也就是说翻译出来是一个Servlet程序。 **总结：**通过翻译的java源代码我们就可以得到结果：jsp就是Servlet程序。 

## 8.2 jsp的三种语法

### 8.2.1 jsp头部的page指令

jsp 的 page 指令可以修改 jsp 页面中一些重要的属性，或者行为。

&lt;%@ page contentType=“text/html;charset=UTF-8” language=“java” %&gt;

- **language 属性：**表示 jsp 翻译后是什么语言文件。暂时只支持 java。 
- **contentType 属性：** 表示 jsp 返回的数据类型是什么。也是源码中 response.setContentType()参数值 
- **pageEncoding 属性：** 表示当前 jsp 页面文件本身的字符集。 
- **import 属性：** 跟 java 源代码中一样。用于导包，导类。

两个属性是给 out 输出流使用=================================

-  autoFlush 属性： 设置当 out 输出流缓冲区满了之后，是否自动刷新冲级区。默认值是 true。 [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-jovVy0wn-1647830664390)(C:\Users\zhangyingying\AppData\Roaming\Typora\typora-user-images\image-20211208173714263.png)]  
-  buffer 属性： 设置 out 缓冲区的大小。默认是 8kb 

=两个属性是给 out 输出流使用=====================

- **errorPage 属性：** 设置当 jsp 页面运行时出错，自动跳转去的错误页面路径。 
- **isErrorPage 属性：** 设置当前 jsp 页面是否是错误信息页面。默认是 false。如果是 true 可以 获取异常信息。 
- **session 属性：** 设置访问当前 jsp 页面，是否会创建 HttpSession 对象。默认是 true。 
- **extends 属性：** 设置 jsp 翻译出来的 java 类默认继承谁。

### 8.2.2 jsp中常用脚本

#### 8.2.2.1 声明脚本

 **声明脚本的格式是：**&lt;%! 声明java代码 %&gt;

 作用：可以给jsp翻译出来的java类定义属性和方法，甚至是静态代码块。内部类等。

练习：

1. 声明类属性 
2. 声明static静态代码块 
3. 声明类方法 
4. 声明内部类

```java
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%--
  Created by IntelliJ IDEA.
  User: zhangyingying
  Date: 2021/12/8
  Time: 16:43
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8"
         errorPage="error500.jsp"
         language="java" %>
<html>
<head>
    <title>printJsp</title>
</head>
<body>

<%--1. 声明类属性--%>
<%!
    private int id;
    private String name;
    private static Map<String,Object> map;
%>
<%--2. 声明static静态代码块--%>
<%!
    static {
        map = new HashMap<String,Object>();
        map.put("key1","value1");
        map.put("key2","value2");
        map.put("key3","value3");
        map.put("key4","value4");
    }
%>
<%--3. 声明类方法--%>
<%!
    public int abc(){
        return 12;
    }
%>
<%--4. 声明内部类--%>
<%!
    public static class A{
        private  int id = 12;
        private String str = "abc";
    }
%>
</body>
</html>
```

#### 8.2.2.2 表达式脚本

 **表达式脚本的格式是**：&lt;%=表达式%&gt;

 **表达式脚本的作用是**：在jsp页面上输出数据。

**表达式脚本的特点：**

1. 所有表达式脚本都会被翻译到_jspService方法中 
2. 表达式脚本都会被翻译成为out.print()输出到页面上 
3. 由于表达式脚本翻译的内容都在_jspService()方法中，所以 _jspServcie()方法中的对象都是可以直接使用 
4. 表达式脚本中的表达式不能以分号结束。

练习：

- 输出整型 
- 输出浮点型 
- 输出字符串 
- 输出对象

```java
<%!=12%>
<%!=12.23%>
<%!="abc"%>
<%!=map%>
<%!=request.getParameter("username")%>
```

#### 8.2.2.3 代码脚本

**代码脚本的格式是：**

&lt;%

 java语句

%&gt;

**代码脚本的作用是**：可以在jsp页面中，编写我们自己需要的功能（写的是java语句）。

**代码脚本的特点是：**

1. 代码脚本翻译之后都在_jspServcie方法中 
2. 代码脚本由于翻译到_jspServcie()方法中，所以在 _jspService方法中的现有对象都可以直接使用。 
3. 还可以由多个代码脚本组合完成一个完整的java语句 
4. 代码脚本还可以和表达式脚本一起组合使用，在jsp页面上输出数据

练习：

- 代码脚本——if语句 
- 代码脚本——for循环语句 
- 翻译后java文件中_jspService方法内的代码都可以写

```java
<%--代码脚本——if语句--%>
<%
    int i = 12;
    if(i == 12){
        System.out.println("是真的");
    }else{
        System.out.println("别瞎掰掰");
    }
%>
<%--代码脚本——for循环语句--%>
<%
    for (int j = 0; j < 10; j++) {
        System.out.println(j);
    }
%>
<%--翻译后java文件中_jspService方法内的代码都可以写--%>
<%
    String username = request.getParameter("username");
    System.out.println("username:" + username);
%>

<%--多个代码脚本组合完成一个完整的java语句，并且和表达式脚本组合使用--%>
<%
	int i = 13 ;
	if (i == 12) {
%>
	<h1>国哥好帅</h1>
<%
	} else {
%>
	<h1>国哥又骗人了！</h1>
<%
}
%>
<br>
```

### 8.2.3 jsp中的三种注释

1.  html注释 html注释会被翻译到java源代码中，在_jspService方法里，以out.writer输出到客户端。  
2.  java注释 <% //单行java注释 /* 多行java注释 */ %> java注释会被翻译到java源代码中。  
3.  jsp注释 <%–这是jsp注释–%> jsp注释可以注释掉jsp页面中所有代码。 

## 8.3 jsp九大内置对象

jsp中内置对象，是指Tomcat在翻译jsp页面成为Servlet源代码之后，内部提供的九大对象，叫内置对象。


## 8.4 jsp四大域对象

四个域对象分别是：pageContext,request,session,application


域对象是可以像Map一样存取数据的对象。四个域对象功能一样。不同的是他们对数据的存取范围。

虽然四个域对像都可以存储数据。在使用上还是有优先顺序的。

四个域在使用的时候优先顺序分别是，他们范围从小到大的范围顺序

pageContext---------&gt;request----------&gt;session---------&gt;application

在最短的时间内，在数据不需要用的情况下就得到最快的释放，从而减轻服务器内存的压力。

**scope1.jsp**

```java
<%--
  Created by IntelliJ IDEA.
  User: zhangyingying
  Date: 2021/12/9
  Time: 11:34
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>scope.jsp页面</h1>
<%
    //往四个域中都分别保存了数据
    pageContext.setAttribute("key1","pageContext");
    request.setAttribute("key1","request");
    session.setAttribute("key1","session");
    application.setAttribute("key1","application");
%>
    
pageContext域是否有值：<%=pageContext.getAttribute("key1")%><br/>
request域是否有值：<%=request.getAttribute("key1")%><br/>
session域是否有值：<%=session.getAttribute("key1")%><br/>
application域是否有值：<%=application.getAttribute("key1")%><br/>
<%
    request.getRequestDispatcher("/scope2.sp").forward(request,response);
%>
</body>
</html>
```

**scope2.jsp**

```java
<%--
  Created by IntelliJ IDEA.
  User: zhangyingying
  Date: 2021/12/9
  Time: 11:52
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
pageContext域是否有值：<%=pageContext.getAttribute("key1")%><br/>
request域是否有值：<%=request.getAttribute("key1")%><br/>
session域是否有值：<%=session.getAttribute("key1")%><br/>
application域是否有值：<%=application.getAttribute("key1")%><br/>
</body>
</html>
```

## 8.5 jsp中的out输出和response.getWriter的区别

response表示响应，我们经常用于设置返回给客户端的内容（输出）

out也是给用户做输出使用的。

分别有两个缓冲区：out缓冲区 response缓冲区

当jsp页面中所有代码执行完成后会做以下两个操作：

- 执行out.flush()操作，会把out缓冲区中的数据追加写入到response缓冲区末尾 
- 会执行response的刷新操作。把全部数据写给客户端

由于jsp翻译之后，底层源代码都是使用out来进行输出，所以一般情况下。我们在jsp页面中统一使用out来进行输出。避免打乱页面输出顺序。

 out.write()：输出字符串没有问题（只适合输出字符串）将输出的值直接转成字符放到out字符缓冲区数组中，对应的值是ASCII码表中的值。

 out.print()：输出任意数据都没有问题（都转换成为字符串类型后调用的write输出）

深入源码，结论：在jsp页面中，可以统一使用out.print()来进行输出

以下代码输出结果是：

response输出1

response输出2

out输出1

out输出2

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    out.write("out输出1<br/>");
    out.write("out输出2<br/>");
    
    response.getWriter().write("response输出1 <br/>");
    response.getWriter().write("response输出2 <br/>");
%>
</body>
</html>
```

## 8.6 jsp的常用标签

### 8.6.1 jsp静态包含

 一个网站的页面，总会有一块展示信息相同，且每个或者大多数页面里包含这些信息。

 我们希望实现这部分信息是一份单独的jsp页面，只维护一份或只改一处其它页面对应的信息也都被修改。

**main.jsp**

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
头部信息<br/>
主体信息<br/>
<%--
    <%@ include file=""%>就是静态包含
    file 属性指定要包含的jsp页面路径

    地址中第一个斜杠  /  表示http://ip:port/工程路径/  映射到代码的web目录

    静态包含的特点：
    1 静态包含不会翻译被包含的jsp页面
    2 静态包含其实就是把被包含的jsp页面的代码拷贝到包含的位置执行输出
--%>
<%@ include file="/include/footer.jsp"%>
</body>
</html>
```

**footer.jsp**

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>页脚信息</title>
</head>
<body>
页脚信息
</body>
</html>
```

### 8.6.2 jsp动态包含

**动态包含的底层原理：**

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-M7kgRVIA-1647830664394)(file:///C:/Users/ZHANGY~1/AppData/Local/Temp/企业微信截图_16390341785228.png)]

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
头部信息<br/>
主体信息<br/>
<%--
    <jsp:include page="/include/footer.jsp"></jsp:include>这是动态包含
    page属性：是指定要包含的jsp页面的路径
    动态包含也可以像静态包含一样，把被包含的内容执行输出到包含位置。
    特点：
        1 动态包含会把包含的jsp页面也翻译成为java代码
        2 动态包含底层代码使用如下代码去调用被包含的jsp页面执行输出。
            JspRuntimeLibrary.include(request,response,"/include/footer.jsp",out.false);
        3 动态包含，还可以传递参数
--%>
<jsp:include page="/include/footer.jsp">
    <jsp:param name="username" value="bbj"/>
    <jsp:param name="password" value="root"/>
</jsp:include>
</body>
</html>
```

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>页脚信息</title>
</head>
<body>
页脚信息
<%=request.getParameter("username")%>
</body>
</html>
```

### 8.6.3 jsp标签-转发

```java
<%--
    <jsp:forword page=""></jsp:forword>是请求转发标签，它的功能就是请求转发
    page属性设置请求转发的路径
--%>
<jsp:forward page="/scope2.jsp"></jsp:forward>
```

## 练习

### 练习一：jsp页面中输出九九乘法表

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>口诀表</title>
</head>
<body>
<h1 align="center">九九乘法口诀表</h1>
<table align="center">
    <% for (int i = 1; i < 10; i++) { %>
    <tr>
        <% for (int j = 1; j <= i; j++) { %>
            <td> <%= j + "*" + i + "=" + i*j%> </td>
        <% } %>
    </tr>
    <% } %>
</table>
</body>
</html>
```

### 练习二：jsp输出一个表格，里面有十个学生信息

```java
<%@ page import="com.company.bean.Student" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %><%--
  Created by IntelliJ IDEA.
  User: zhangyingying
  Date: 2021/12/9
  Time: 15:26
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>学生信息表</title>
    <style>
        table{
            border:1px black solid;
            width:600px;
            border-collapse: collapse;
        }
        td,th{
            border:1px black solid;
        }
    </style>
</head>
<body>
<%
    List<Student> studentList = new ArrayList<Student>();
    for (int i = 0; i < 10; i++) {
        int t = i+1;
        studentList.add(new Student(t,"name"+t,"phone"+t,18+t));
    }
%>
<table>
    <tr>
        <td>编号</td>
        <td>姓名</td>
        <td>年龄</td>
        <td>电话</td>
        <td>操作</td>
    </tr>
<%for (Student student : studentList) {%>
<tr>
    <td><%=student.getId()%></td>
    <td><%=student.getName()%></td>
    <td><%=student.getAge()%></td>
    <td><%=student.getPhone()%></td>
    <td>删除 修改</td>
</tr>
<% } %>
</table>
</body>
</html>
```

**请求转发的使用说明**

**SearchStudentServlet**

```java
package com.company.controller;

import com.company.bean.Student;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

/**
 * ClassName: SearchStudentServlet
 * Description:
 * date: 2021/12/9 16:20
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class SearchStudentServlet extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        //获取前端发送的参数

        //发sql语句查询学生的信息

        //使用for循环生成到的数据做模拟
        List<Student> studentList = new ArrayList<Student>();
        for (int i = 0; i < 10; i++) {
   
            int t = i+1;
            studentList.add(new Student(t,"name"+t,"phone"+t,18+t));
        }
        //保存查询到的结果（学生信息）到request域中
        req.setAttribute("studentlist",studentList);
        //请求转发到showStudent.jsp页面
        req.getRequestDispatcher("/test/showStudent.jsp").forward(req,resp);
    }
}
```

**showStudent.jsp**

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

</body>
</html>
<%@ page import="com.company.bean.Student" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>学生信息表</title>
    <style>
        table{
            border:1px black solid;
            width:600px;
            border-collapse: collapse;
            align-content: center;
        }
        td,th{
            border:1px black solid;
        }
    </style>
</head>
<body>
<%
    List<Student> studentlist = (List<Student>) request.getAttribute("studentlist");
%>

<table>
    <tr>
        <td>编号</td>
        <td>姓名</td>
        <td>年龄</td>
        <td>电话</td>
        <td>操作</td>
    </tr>
    <%for (Student student : studentlist) {%>
    <tr>
        <td><%=student.getId()%></td>
        <td><%=student.getName()%></td>
        <td><%=student.getAge()%></td>
        <td><%=student.getPhone()%></td>
        <td>删除 修改</td>
    </tr>
    <% } %>
</table>
</body>
</html>
```

## 8.7 什么是Listener监听器？

1. Listener监听器它是JavaWeb的三大组件之一。JavaWeb的三大组件分别是：Servlet程序 Filter过滤器 Lintener监听器 
2. Listener它是JavaEE的规范，就是接口 
3. 监听的作用是，监听某种事务的变化。然后通过回调函数，反馈给用户（程序）去做一些相应的处理。

### 8.7.1 ServletContextListener监听器

 ServletContextListener它可以监听ServletContext对象的创建和销毁。

 ServletContext对象在web工程启动的时候创建，在web工程停止的时候销毁。

 监听到创建和销毁之后都会分别调用ServletContextListener监听器的方法反馈。

两个方法是：

```java
public interface ServletContextListener extends EventListener {
   
    //在ServletContext对象创建之后马上调用，做初始化
    public void contextInitialized(ServletContextEvent var1);
    //在ServletContext对象销毁之后调用
    public void contextDestroyed(ServletContextEvent var1);
}
```

实现监听器

```java
package com.company.listener;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

public class MyServletContextListener implements ServletContextListener {
   
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
   
        System.out.println("ServletContext对象被创建了");
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
   
        System.out.println("ServletContext对象被销毁了");
    }
}
```

web.xml中配置监听器

```java
<!--配置监听器-->
<listener>
    <listener-class>com.company.listener.MyServletContextListener</listener-class>
</listener>
```


## 9.1 什么是EL表达式？EL表达式有什么作用？

EL表达式的全称是：ExPression Language 是表达式语言。

EL表达式的作用：EL表达式主要代替JSP页面中的表达式脚本在jsp页面中进行数据的输出。

因为EL表达式在输出数据的时候要比jsp的表达式脚本要简洁很多

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    request.setAttribute("key","value");
%>
表达式脚本输出key的值是：<%=request.getAttribute("key")%><br/>
EL表达式输出key值是:${key}
</body>
</html>
```

EL表达式的格式是：${表达式}

EL表达式在输出null值的时候，输出的是空串。jsp表达式脚本输null值的时候，输出的是null字符串。

## 9.2 EL表达式搜索域数据的顺序

EL表达式主要是在jsp页面中输入数据

主要是输出域对象中的数据。

当四个域中都有相同的key的数据时，EL表达式会按照四个域的从小到大的顺序去进行搜索，找到就输出

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    pageContext.setAttribute("key","pageContext");
    request.setAttribute("key","request");
    session.setAttribute("key","session");
    application.setAttribute("key","application");
%>
${key}
</body>
</html>
```

## 9.3 EL表达式输出Bean的普通属性，数组属性。List集合属性，map集合属性需求——输出Person类中普通属性，数组属性，list集合和map集合属

**Person实体类**

```java
package com.company.Bean;

import java.util.Arrays;
import java.util.List;
import java.util.Map;

public class Person {
   
    private String name;
    private String[] phones;
    private List<String> cities;
    private Map<String,Object> map;
    private int age=18;

    public int getAge() {
   
        return 18;
    }

    public Person() {
   
    }

    public String getName() {
   
        return name;
    }

    public void setName(String name) {
   
        this.name = name;
    }

    public String[] getPhones() {
   
        return phones;
    }

    public void setPhones(String[] phones) {
   
        this.phones = phones;
    }

    public List<String> getCities() {
   
        return cities;
    }

    public void setCities(List<String> cities) {
   
        this.cities = cities;
    }

    public Map<String, Object> getMap() {
   
        return map;
    }

    public void setMap(Map<String, Object> map) {
   
        this.map = map;
    }

    @Override
    public String toString() {
   
        return "Person{" +
                "name='" + name + '\'' +
                ", phones=" + Arrays.toString(phones) +
                ", cities=" + cities +
                ", map=" + map +
                '}';
    }
}
```

**b.jsp**

```java
<%@ page import="com.company.Bean.Person" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %><%--
  Created by IntelliJ IDEA.
  User: zhangyingying
  Date: 2021/12/9
  Time: 18:19
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    Person person = new Person();
    person.setName("张三yyds");
    person.setPhones(new String[]{"18600264640","45645612333","13344567895"});
    
    List<String> cities = new ArrayList<String>();
    cities.add("北京");
    cities.add("上海");
    cities.add("广州");
    person.setCities(cities);
    
    Map<String,Object> map = new HashMap<>();
    map.put("key1","value1");
    map.put("key2","value2");
    map.put("key3","value3");
    person.setMap(map);
    pageContext.setAttribute("p",person);
%>

输出Person:${p}<br/>
    
输出Person的name属性:${p.name}<br/>
    
输出Person的phones属性：${p.phones}<br/>
    
输出Person的cities集合总的元素值：${p.cities}<br/>
    
输出Person的List集合中的个别值：${p.cities[1]}<br/>
    
输出Person的Map集合：${p.map}<br/>
    
输出Person的Map集合中某个key的值：${p.map.key2}<br/>
    
输出Person的age属性:${p.age}<br/>

</body>
</html>
```

通过获取age的值，我们可以了解到：

1. 当age定义时被赋值，没有提供getAge的情况下，jsp页面不能通过p.age获取到age的值。 
2. 当age定义时赋值和getAge返回的值不同时，p.age获取到的值是getAge的return值。 
3. 以上情况可知EL表达式获取对象值的时候不是寻找属性本身，而是寻找要查询的属性的get方法。

## 9.4 EL表达式运算

语法：${ 运算表达式 }，EL表达式支持如下运算符：

### 9.4.1 关系运算


### 9.4.2 逻辑运算


### 9.4.3 算数运算


### 9.4.4 empty运算

empty运算可以判断一个数据是否为空，如果为空，则输出true，不空输出false

以下几种情况为空：

1.  值为null值的时候为空  
2.  值为空串的时候为空  
3.  值是Object类型数组，长度为零的时候  
4.  list集合，元素个数为0  
5.  map集合，元素个数为0 <%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %><%--
    Created by IntelliJ IDEA.
    User: zhangyingying
    Date: 2021/12/10
    Time: 10:18
    To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    //1. 值为null值的时候为空
    request.setAttribute("emptyNull",null);
    //2. 值为空串的时候为空
    request.setAttribute("emptyStr","");
    //3. 值是Object类型数组，长度为零的时候
    request.setAttribute("emptyArr",new Object[]{});
    //4. list集合，元素个数为0
    List<String> list = new ArrayList<String>();
    list.add("empty");
    request.setAttribute("emptyList",list);
    //5. map集合，元素个数为0
    Map<String,Object> map = new HashMap<String,Object>();
    map.put("key","value");
    request.setAttribute("emptyMap",map);
%>
${empty emptyNull}
${empty emptyStr}
${empty emptyArr}
${empty emptyList}
${empty emptyMap}
</body>
</html> 

#### 9.4.4.1 三元运算

表达式1？表达式2：表达式3

如果表达式1的值为true，返回表达式2的值，如果表达式1的值为假，返回表达式3的值

```java
${12 == 12?"是真的":"别瞎掰掰"}
```

#### 9.4.4.2 "."点运算和[]中括号运算符

**.**点运算，可以输出Bean对象中的某个属性值。

[]中括号运算，可以输出有序集合中某个元素的值

并且[]中括号运算，还可以输出map集合中key里含有特殊字符的key的值，这时key值要用单引号或双引号包括。

```java
<%@ page import="java.util.Map" %>
<%@ page import="org.omg.PortableInterceptor.ObjectReferenceFactory" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    Map<String, Object> map = new HashMap<String,Object>;
    map.put("a.a.a","aaavalue");
    map.put("b+b+b","bbbvalue");
    map.put("c-c-c","cccvalue");
    request.setAttribute("map",map);
%>
${map["a.a.a"]}
${map["b+b+b"]}
${map['c-c-c']}
</body>
</html>
```

## 9.5 EL表达式中11个隐含对象

EL表达式中11个隐含对象，是EL表达式中自己定义的可以直接使用


### 9.5.1 EL获取四个特定域中的属性


```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>scope</title>
</head>
<body>
<%
    pageContext.setAttribute("key1","pageContext");
    request.setAttribute("key1","request");
    session.setAttribute("key1","session");
    application.setAttribute("key1","application");
%>
${pageScope.key1}
${requestScope.key1}
${sessionScope.key1}
${applicationScope.key1}
</body>
</html>
```

### 9.5.2 pageContext对象的使用

1.  协议：  
2.  服务器ip：  
3.  服务器端口：  
4.  获取工程路径：  
5.  获取请求方法：  
6.  获取客户端ip地址：  
7.  获取会话的id编号： <%--
    Created by IntelliJ IDEA.
    User: zhangyingying
    Date: 2021/12/10
    Time: 11:33
    To change this template use File | Settings | File Templates.
    --%>
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
<head>
    <title>Title</title>
</head>
<body>
${ pageContext }<br/>
1. 协议：<%=request.getScheme()%><br/>
2. 服务器ip：<%=request.getServerName()%><br/>
3. 服务器端口：<%=request.getServerPort()%><br/>
4. 获取工程路径：<%=request.getContextPath()%><br/>
5. 获取请求方法：<%=request.getMethod()%><br/>
6. 获取客户端ip地址：<%=request.getRemoteHost()%><br/>
7. 获取会话的id编号：<%=session.getId()%><br/>


1. 协议：${pageContext.request.scheme}<br/>
2. 服务器ip：${pageContext.request.serverName}<br/>
3. 服务器端口：${pageContext.request.serverPort}<br/>
4. 获取工程路径：${pageContext.request.contextPath}<br/>
5. 获取请求方法：${pageContext.request.method}<br/>
6. 获取客户端ip地址：${pageContext.request.remoteHost}<br/>
7. 获取会话的id编号：${pageContext.session.id}<br/>

</body>
</html> 

### 9.5.3 EL表达式其他隐含对象的使用


**param和paramValues**

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>other_el_obj</title>
</head>
<body>
输出请求参数username的值${param.username}<br/>
输出请求参数password的值${param.password}<br/>

输出请求参数username的值：${paramValues.username[0]}<br/>
输出请求参数hobby的值：${paramValues.hobby[0]}<br/>
输出请求参数hobby的值：${paramValues.hobby[1]}<br/>
</body>
</html>
```

请求地址：(http://localhost:8080/09_EL_JSTL/other_el_obj.jsp?username=wzg168&amp;password=666666&amp;hobby=java&amp;hobby=cpp)

**header和headerValues**

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>other_el_obj</title>
</head>
<body>
由于User-Agent中含有特殊字符，因此在取值的时候不能直接用点运算，要用[]运算，在中括号中要带引号<br/>
    
输出请求头[User-Agent]的值：${header["User-Agent"]}<br/>
输出请求头[Connection]的值：${header.Connection}<br/>
    
使用headerValues输出[User-Agent]是数组的地址值，要取值需要加上下标。<br/>
    
输出请求头[User-Agent]的值：${headerValues["User-Agent"][0]}<br/>

</body>
</html>
```

**cookie和initParam**

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>other_el_obj</title>
</head>
<body>
    
获取Cookie的名称:${cookie.JSESSIONID.name}<br>
获取Cookie的值：${cookie.JSESSIONID.value}<br>
    
<hr/>
    
输出&lt;Context-param&gt;username的值：${initParam.username}<br>
输出&lt;Context-param&gt;password的值：${initParam.password}<br>
    
</body>
</html>
```

**web.xml配置信息**

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <context-param>
        <param-name>username</param-name>
        <param-value>root</param-value>
    </context-param>
    <context-param>
        <param-name>password</param-name>
        <param-value>123456</param-value>
    </context-param>
</web-app>
```


 JSTL标签库全称是指 JSP Standard Tag Library （JSP标准标签库）。是一个不断完善的开放源代码的JSP标签库。

 EL表达式主要是为了替换jsp中的表达式脚本，而标签库是为了替换代码脚本。这样使得整个jsp页面变得更加整洁。

 J**STL由五个不同功能的标签库组成**


**在jsp标签库中使用taglib指令引入标签库**

CORE 标签库

&lt;%@ taglib prefix=“c” uri=“http://java.sun.com/jsp/jstl/core” %&gt;

XML 标签库

&lt;%@ taglib prefix=“x” uri=“http://java.sun.com/jsp/jstl/xml” %&gt;

FMT 标签库

&lt;%@ taglib prefix=“fmt” uri=“http://java.sun.com/jsp/jstl/fmt” %&gt;

SQL 标签库

&lt;%@ taglib prefix=“sql” uri=“http://java.sun.com/jsp/jstl/sql” %&gt;

FUNCTIONS 标签库

&lt;%@ taglib prefix=“fn” uri=“http://java.sun.com/jsp/jstl/functions” %&gt;

## 10.1 JSTL标签库的使用步骤

1.  先导入jstl标签库jar包。 taglibs-standard-impl-1.2.1.jar taglibs-standard-spec-1.2.1.jar  
2.  使用taglib指令引入标签库。 <%@ taglib prefix=“c” uri=“http://java.sun.com/jsp/jstl/core” %> 

## 10.2 core核心库使用

### 10.2.1 <c:set/>(很少使用)

作用：set标签可以往域中保存数据

```java
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%--
  Created by IntelliJ IDEA.
  User: zhangyingying
  Date: 2021/12/10
  Time: 15:40
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>core</title>
</head>
<body>
<%--
i.<c:set />
作用：set 标签可以往域中保存数据

域对象.setAttribute(key,value);
scope 属性设置保存到哪个域
    page 表示 PageContext 域（默认值）
    request 表示 Request 域
    session 表示 Session 域
    application 表示 ServletContext 域
var 属性设置 key 是多少
value 属性设置值
--%>
保存之前：${ requestScope.abc } <br>
<c:set scope="request" var="abc" value="abcValue"/>
保存之后：${ requestScope.abc } <br>
</body>
</html>
```

### 10.2.2 <c:if/>

作用：if标签用来做if判断

```java
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>core</title>
</head>
<body>

<%--
ii.<c:if></c:if>
    if标签用来做if判断
    test属性表示判断条件（使用EL表达式输出）
--%>
<%--用两个if条件相反来实现if   else--%>
<c:if test="${12 == 12}">
    <h1>这是真的!</h1>
</c:if>

<c:if test="${12 != 12}">
    <h1>这不是真的!</h1>
</c:if>
</body>
</html>
```

### 10.2.3 <c:choose/><c:when/><c:otherwise/>标签

作用：多路判断。跟switch…case…default非常接近

choose标签开始选择判断

when标签表示每一种判断情况

 test属性表示当前这种判断情况的值

otherwise标签表示剩下的情况

&lt;c:choose&gt;&lt;c:when&gt;&lt;c:otherwise&gt;标签使用时需要注意的点：

1. 标签里不能用html注释，要使用jsp注释 
2. when标签的父标签一定要是choose标签

```java
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>core</title>
</head>
<body>
<%--
iii.<c:choose> <c:when> <c:otherwise>标签
作用：多路判断。跟 switch ... case .... default 非常接近

choose 标签开始选择判断
when 标签表示每一种判断情况
    test 属性表示当前这种判断情况的值
otherwise 标签表示剩下的情况
<c:choose> <c:when> <c:otherwise>标签使用时需要注意的点：
    1 标签里不能使用 html 注释，要使用 jsp 注释
    2 when 标签的父标签一定要是 choose 标签
--%>
<%
    request.setAttribute("height",181);
%>
<c:choose>
    <%--这是html注释--%>
    <c:when test="${ requestScope.height > 190}">
        <h1>小巨人</h1>
    </c:when>
    <c:when test="${requestScope.height > 180}">
        <h2>挺高的</h2>
    </c:when>
    <c:when test="${requestScope.height > 170}">
        <h3>嗯嗯，好的</h3>
    </c:when>
    <c:when test="${requestScope.height > 160}">
        <h4>女生吧</h4>
    </c:when>
    <c:otherwise>
        <c:choose>
            <c:when test="${requestScope.height >150}">
                <h5>大于150</h5>
            </c:when>
            <c:otherwise>
                <h6>其他小于150的</h6>
            </c:otherwise>
        </c:choose>
    </c:otherwise>
</c:choose>
</body>
</html>
```

### 10.2.4 <c:forEach/>

作用：遍历循环使用

**练习1：从1遍历到10，输出**

```java
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>forEach标签</title>
</head>
<body>
<%--
    1 遍历1到10，输出
    begin 属性设置开始的索引
    end  属性设置结束的索引
    var 属性表示循环的变量（也是当前正在遍历到的数据）
--%>
<table border="1">
<c:forEach begin="1" end="10" var="i">
    <tr>
        <td>第${i}行</td>
    </tr>
</c:forEach>
</table>
</body>
</html>
```

**遍历对象类型的数组**

```java
<%--
    2 遍历Object数组
    for(Object item:arr)
    items 表示遍历的数据源（遍历集合）
    var 表示当前遍历到的数据
--%>
<%
    request.setAttribute("name",new String[]{"zhangsan","lisi","wnagwu"});
%>
<c:forEach items="${requestScope.name}" var="itme">
    ${ itme } <br>
</c:forEach>
```

**遍历Map集合**

```java
<%--
    3 遍历Map集合
--%>
<%
    Map<String,Object> map = new HashMap<String,Object>();
    map.put("key1","value1");
    map.put("key2","value2");
    map.put("key3","value3");
    map.put("key4","value4");
/*    for(Map.Entry<String,Object> entry: map.entrySet()){
        
    }*/
    request.setAttribute("map",map);
%>

<c:forEach items="${requestScope.map}" var="entry">
    ${entry.key} = ${entry.value}
</c:forEach>
```

**遍历list集合——list中存放Student类，有属性：编号，用户名，密码，年龄，电话信息**

**Student**

```java
package com.company.Bean;

/**
 * ClassName: Student
 * Description:
 * date: 2021/12/10 17:13
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class Student {
   
    private int id;
    private String username;
    private String password;
    private int age;
    private String phone;

    public Student() {
   
    }

    public Student(int id, String username, String password, int age, String phone) {
   
        this.id = id;
        this.username = username;
        this.password = password;
        this.age = age;
        this.phone = phone;
    }

    public int getId() {
   
        return id;
    }

    public void setId(int id) {
   
        this.id = id;
    }

    public String getUsername() {
   
        return username;
    }

    public void setUsername(String username) {
   
        this.username = username;
    }

    public String getPassword() {
   
        return password;
    }

    public void setPassword(String password) {
   
        this.password = password;
    }

    public int getAge() {
   
        return age;
    }

    public void setAge(int age) {
   
        this.age = age;
    }

    public String getPhone() {
   
        return phone;
    }

    public void setPhone(String phone) {
   
        this.phone = phone;
    }

    @Override
    public String toString() {
   
        return "Student{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", age=" + age +
                ", phone='" + phone + '\'' +
                '}';
    }
}
```

**jsp**

```java
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page import="java.util.List" %>
<%@ page import="com.company.Bean.Student" %>
<%@ page import="java.util.ArrayList" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>forEach标签</title>
    <style type="text/css">
        table{
            width: 500px;
            border:1px solid black;
        }
        th,td{
            border: 1px solid black;
        }
    </style>
</head>
<body>

<%--4 遍历list集合--%>
<%
    List<Student> studnetList = new ArrayList<Student>();
    for (int i = 0; i <= 10 ; i++) {
        studnetList.add(new Student(i,"username"+i,"pass"+i,18 + i,"phone" + i));
    }
    request.setAttribute("list",studnetList);
%>
<table border="1" align="center">
    <tr>
        <td>编号</td>
        <td>姓名</td>
        <td>密码</td>
        <td>年龄</td>
        <td>电话</td>
    </tr>

    <c:forEach items="${requestScope.list}" var="student">
        <tr>
            <td>${student.id}</td>
            <td>${student.username}</td>
            <td>${student.password}</td>
            <td>${student.age}</td>
            <td>${student.phone}</td>
        </tr>

    </c:forEach>
</table>
</body>
</html>
```

### 10.2.5 JSTL标签库forEach标签所有组合使用介绍

**status方法的属性值**

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-PPCAmem9-1647830664395)(C:\Users\zhangyingying\AppData\Roaming\Typora\typora-user-images\image-20211210175544337.png)]

```java
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page import="java.util.List" %>
<%@ page import="com.company.Bean.Student" %>
<%@ page import="java.util.ArrayList" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>forEach标签</title>
    <style type="text/css">
        table{
            width: 500px;
            border:1px solid black;
        }
        th,td{
            border: 1px solid black;
        }
    </style>
</head>
<body>

<%--4 遍历list集合--%>
<%
    List<Student> studnetList = new ArrayList<Student>();
    for (int i = 0; i <= 10 ; i++) {
        studnetList.add(new Student(i,"username"+i,"pass"+i,18 + i,"phone" + i));
    }
    request.setAttribute("list",studnetList);
%>
<table border="1" align="center">
    <tr>
        <td>编号</td>
        <td>姓名</td>
        <td>密码</td>
        <td>年龄</td>
        <td>电话</td>
    </tr>

    <c:forEach begin="2" end="9" step="2" varStatus="status" items="${requestScope.list}" var="student">
        <tr>
            <td>${student.id}</td>
            <td>${student.username}</td>
            <td>${student.password}</td>
            <td>${student.age}</td>
            <td>${student.phone}</td>
            <td>${status.begin}</td>
            <td>${status.end}</td>
            <td>${status.current}</td>
            <td>${status.index}</td>
            <td>${status.count}</td>
            <%--读方法
            生成getXxxx(){}或isXxxx(){}属性是boolean类型时，生成的读方法就是isXxx(){},set方法都一样
            --%>
            <td>${status.first}</td>
            <td>${status.last}</td>
            <td>${status.step}</td>
        </tr>

    </c:forEach>
</table>
</body>
</html>
```


文件的上传和下载是非常常见的功能。很多系统中都经常使用文件的上传和下载。

比如：QQ头像，就使用了上传。

邮箱中也有附件的上传和下载功能

OA系统中审批有附件材料的上传。

## 11.1 文件的上传介绍

1. 要有一个form标签，method=**post**请求 
2. form标签的encType属性值必须为mutipart/form-data值 
3. 在form标签中使用input type=file添加上传文件 
4. 编写服务器代码（Servlet程序）接收，处理上传的文件数据

 encType = multipart/form-data表示提交数据的时候，以多段（每一个个表单项一个数据段）的形式进行拼接，然后以二进制流的形式发送给服务器。

## 11.2 文件上传，HTTP协议的说明

请求头 POST /12_file/upload HTTP/1.1 Host: localhost:8080 Connection: keep-alive Content-Length: 287672 Cache-Control: max-age=0 sec-ch-ua: “Google Chrome”;v=“95”, “Chromium”;v=“95”, “;Not A Brand”;v=“99” sec-ch-ua-mobile: ?0 sec-ch-ua-platform: “Windows” Upgrade-Insecure-Requests: 1 Origin: http://localhost:8080 Content-Type: multipart/form-data; boundary=----WebKitFormBoundarybk4cAePF3swpuqTFContent-Type表示提交的数据类型；multipart/form-data表示提交的数据，以多段（每一个表单项一个数据段）的形式发送给服务器；boundary表示每段数据的分隔符，是由浏览器每次都随机生成，它就是每段数据的分界符。 User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36 Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng, */* ;q=0.8,application/signed-exchange;v=b3;q=0.9 Sec-Fetch-Site: same-origin Sec-Fetch-Mode: navigate Sec-Fetch-User: ?1 Sec-Fetch-Dest: document Referer: http://localhost:8080/12_file/upload.jsp Accept-Encoding: gzip, deflate, br Accept-Language: zh-CN,zh;q=0.9 Cookie: JSESSIONID=5C414FE1FF66ECB683901D51C805F8AC

空行

----WebKitFormBoundarybk4cAePF3swpuqTF表示一段数据的开始 Content-Disposition:form-data;name=“username”

空行

admin表示当前表单值 ----WebKitFormBoundarybk4cAePF3swpuqTF表示另一段数据的开始 Content-Disposition:form-data;name=“photo”;filename=“d.jsp” Content-Type:image/jpeg

空行

上传文件的数据

----WebKitFormBoundarybk4cAePF3swpuqTF–多了两个减号的分隔符，表示数据的结束标记

**接收客户端上传的文件**

```java
package com.company.controller;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class UploadServlet extends HttpServlet {
   

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        //文件是由二进制流上传的，接收也要用二进制流接收。
        byte[] buffer = new byte[102400];
        int read = req.getInputStream().read(buffer);
        System.out.println(new String(buffer,0,read));
    }
}
```

## 11.3 commons-fileupload.jar常用API介绍说明

commons-fileupload.jar 需要依赖 commons-io.jar 这个包，所以两个包我们都要引入。

第一步，就是需要导入两个 jar 包：

 commons-fileupload-1.2.1.jar

 commons-io-1.4.jar

commons-fileupload.jar 和 commons-io.jar 包中，我们常用的类有哪些？

**ServletFileUpload 类，用于解析上传的数据。**

**FileItem 类，表示每一个表单项。**

boolean ServletFileUpload.isMultipartContent(HttpServletRequest request);

 判断当前上传的数据格式是否是多段的格式。

public List parseRequest(HttpServletRequest request)

 解析上传的数据

boolean FileItem.isFormField()

 判断当前这个表单项，是否是普通的表单项。还是上传的文件类型。

 true 表示普通类型的表单项

 false 表示上传的文件类型

String FileItem.getFieldName()

 获取表单项的 name 属性值

String FileItem.getString()

 获取当前表单项的值。

String FileItem.getName();

 获取上传的文件名

void FileItem.write( file );

 将上传的文件写到参数 file所指向的磁盘位置。

## 11.4 fileupload类库的使用：

**上传文件的表单：**

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="http://localhost:8080/12_file/upload" method="post" enctype="multipart/form-data">
    用户名：<input type="text" name="username"/><br>
    头像：<input type="file" name="photo" value="上传图片"/><br>
    <input type="submit" value="上传">
</form>
</body>
</html>
```

**解析上传的数据的代码**

```java
package com.company.controller;

import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.FileUploadException;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.IOException;
import java.util.List;

/**
 * ClassName: UploadServlet
 * Description:
 * date: 2021/12/13 13:16
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class UploadServlet extends HttpServlet {
   
    /**
     * @Description:
     * @author: zhangyingying
     * @date: 2021/12/13 13:53
     * @param:
     * @Param: req
     * @Param: resp
     * @return:void
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        //判断上传的数据是不是多段数据（只有多段数据才是文件上传的）
        if(ServletFileUpload.isMultipartContent(req)){
   
            //创建FileItemFactory工厂实现类
            FileItemFactory fileItemFactory = new DiskFileItemFactory();
            //创建用于解析上传数据的工具类ServletFileUpload类
            ServletFileUpload servletFileUpload = new ServletFileUpload(fileItemFactory);
            try {
   
                //解析上传数据，得到每一个表单项FileItem
                List<FileItem> list = servletFileUpload.parseRequest(req);
                //循环判断，每一个表单项，是普通类型，还是上传文件
                for (FileItem fileItem: list) {
   
                    if(fileItem.isFormField()){
   
                        //普通表单项
                        System.out.println("表单项的name属性值" + fileItem.getFieldName());
                        System.out.println("表单项的value属性值" + fileItem.getString("UTF-8"));
                    }else {
   
                        //是上传的文件
                        System.out.println("上传文件的name属性名" + fileItem.getFieldName());
                        System.out.println("上传文件名" + fileItem.getName());
                        fileItem.write(new File("e:\\" + fileItem.getName()));
                    }
                }
            } catch (Exception e) {
   
                e.printStackTrace();
            }
        }
    }
}
```


**下载的常用API说明：**

response.getOutputStream()

servletContext.getResourAStream()

servletContext.getMimeType()

response.setContentType()

response.setHeader(“Content-Disposition”,“attachment;fileName=1.png”)

 这个响应头告诉浏览器，这是需要下载的。而attachment表示附件，也就是下载的一个文件。fileName=后面，表示下载的文件名。

 完成上面两个步骤，下载文件是没问题了，但是如果我们现在文件是中文名的话，你会发现，下载无法正确显示出正确的中文名。

 原因是在响应头中，不能包含有中文字符，只能包含ASCII码。

**文件下载实例：**

```java
package com.company.controller;

import org.apache.commons.io.IOUtils;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

public class DownloadServlet extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        //1 获取要下载的文件名
        String downloadFileName = "2.png";
        
        //2 读取要下载的文件内容（通过ServletContext对象可以读取）
        ServletContext servletContext = getServletContext();
        
        //4 在回传前，通过响应头告诉客户端返回的数据类型
            //获取要下载的文件类型
        String mimeType1 = servletContext.getMimeType("/file/" + downloadFileName);
        resp.setContentType(mimeType1);
        
        //5 还要告诉客户端手袋的数据是用于下载使用（还是使用响应头）
        //Content-Disposition响应头，表示收到的数据怎么处理
        //attachment表示附件，表示下载使用
        //filename=表示指定下载的文件名
        resp.setHeader("Content-Disposition","attachment;filename=" + downloadFileName);
        
        /**
         * /斜杠被服务器解析表示地址为 http://ip:prot/工程名/ 映射 到代码的 Web 目录
         */
        InputStream resourceAsStream1 = servletContext.getResourceAsStream("/file/" + downloadFileName);
        
        //获取响应的输出流
        OutputStream outputStream1 = resp.getOutputStream();
        
        //3 把下载的文件内容回传给客户端。
        //读取输入流中全部的数据，复制给输出流，输出给客户端
        IOUtils.copy(resourceAsStream1,outputStream1);
    }
}
```

## 12.1 附件中文乱码问题解决方法：

### 12.1.1 方案一：URLEncoder解决IE和谷歌浏览器的附件中文名问题。

如果客户端浏览器是IE浏览器或者谷歌浏览器。我们需要使用URLEconder类先对中文名进行UTF-8的编码操作。

因为IE和谷歌浏览器收到含有编码后的字符串后会以UTF-8字符集进行解码显示。

```java
//url编码是把汉字转换成为%xx%xx的格式,xx表示十六进制。
//把中文名进行UTF-8编码操作
String str = "attachment;fileName=" + URLEncoder.encode("中文文件名.png","UTF-8");
//然后把编码后的字符串设置到响应头中
response.setHeader("Content-Disposition",str);
```

### 12.1.2 BASE64编解码，火狐浏览器的附件中文名问题

如果客户端浏览器是火狐浏览器。那么我们需要对中文名进行BASE64的编码操作。

这时候需要把请求头Content-Disposition:attachment;filename=中文名

编码成为：Content-Disposition:attachment;filename= =?charset?B?xxxx?=====

=?charset?B?xxxx?=现在我们对这段内容进行一下说明。

=？表示编码内容的开始

charset表示字符集

B表示BASE64编码

xxxx表示编码后的内容

?=表示编码内容的结束

**BASE64编码解码示例：**

```java
package com.company.base;

import sun.misc.BASE64Decoder;
import sun.misc.BASE64Encoder;
/**
 * ClassName: BaseTest
 * Description:
 * date: 2021/12/14 9:38
 *
 * @author zhangyingying
 * @since JDK 1.8
 */
public class BaseTest {
   
    public static void main(String[] args) throws Exception {
   
        String str = "这是一串需要base64编码的字符串";
        //创建一个BASE64编码器
        BASE64Encoder base64Encoder = new BASE64Encoder();
        //执行BASE64编码操作
        String encode = base64Encoder.encode(str.getBytes("UTF-8"));
        System.out.println(encode);

        //创建BASE64解码器
        BASE64Decoder base64Decoder = new BASE64Decoder();
        //解码操作
        byte[] bytes = base64Decoder.decodeBuffer(encode);
        String string = new String(bytes, "UTF-8");
        System.out.println(string);
    }
}
```

因为火狐使用的是BASE64的编码方式还原响应中的汉字。所以需要使用BASE64Ecoder类进行编码操作。

```java
//火狐浏览器设置响应头
resp.setHeader("Content-Disposition","attachment;filename==?utf-8?B?" + new BASE64Encoder().encode("中文文件名.png".getBytes("UTF-8")) + "?=");
```

## 12.2 实现不同浏览器下载中文文件名的兼容

```java
package com.company.controller;

import org.apache.commons.io.IOUtils;
import sun.misc.BASE64Encoder;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.URLEncoder;

public class DownloadServlet extends HttpServlet {
   
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
        //1 获取要下载的文件名
        String downloadFileName = "2.png";
        //2 读取要下载的文件内容（通过ServletContext对象可以读取）
        ServletContext servletContext = getServletContext();
        //4 在回传前，通过响应头告诉客户端返回的数据类型
            //获取要下载的文件类型
        String mimeType1 = servletContext.getMimeType("/file/" + downloadFileName);
        resp.setContentType(mimeType1);
        
        //5 还要告诉客户端手袋的数据是用于下载使用（还是使用响应头）
        //Content-Disposition响应头，表示收到的数据怎么处理
        //attachment表示附件，表示下载使用
        //filename=表示指定下载的文件名
        //判断是否为火狐浏览器
        if(req.getHeader("User-Agent").contains("Firefox")){
   
            //使用BASE64编码后
            resp.setHeader("Content-Disposition","attachment;filename==?UTF-8?B?" + new BASE64Encoder().encode("中文文件名.png".getBytes("UTF-8")) + "?=");
        } else {
   
            //把中文名进行utf-8编码操作
            resp.setHeader("Content-Disposition","attachment;filename=" + URLEncoder.encode("中文文件名.png","utf-8"));
        }
        
        //resp.setHeader("Content-Disposition","attachment;filename=" + downloadFileName);
        //IE和谷歌浏览器url编码是把汉字转换成为%xx%xx的格式,xx表示十六进制。
        //resp.setHeader("Content-Disposition","attachment;filename=" + URLEncoder.encode(downloadFileName,"UTF-8"));
        //火狐浏览器
        resp.setHeader("Content-Disposition","attachment;filename==?UTF-8?B?" + new BASE64Encoder().encode("中文文件名.png".getBytes("UTF-8")) + "?=");
        /**
         * /斜杠被服务器解析表示地址为 http://ip:prot/工程名/ 映射 到代码的 Web 目录
         */
        InputStream resourceAsStream1 = servletContext.getResourceAsStream("/file/" + downloadFileName);
        //获取响应的输出流
        OutputStream outputStream1 = resp.getOutputStream();
        //3 把下载的文件内容回传给客户端。
        //读取输入流中全部的数据，复制给输出流，输出给客户端
        IOUtils.copy(resourceAsStream1,outputStream1);
    }
}
```

