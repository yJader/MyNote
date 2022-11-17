# 第一章 Web基础概念简介

## 1.1 服务器与客户端

1. 线下的服务器与客户端

<img src="media/img001.88bb762e.jpg" alt="img001.88bb762e" style="zoom:50%;" />

2. 线上的服务器与客户端

<img src="media/img002.d54befb9.jpg" alt="./images" style="zoom:67%;" />

3. 客户端的各种形式

   1. PC端网页<img src="media/img003.442e866a.jpg" alt="./images" style="zoom:33%;" />

   2. 移动端<img src="media/img004.e5a347b7.jpg" alt="./images" style="zoom: 20%;" />

   3. Iot设备<img src="media/img005.1461eb93.jpg" alt="./images" style="zoom: 33%;" />

   4. 服务器的各种形式: [点我查看完整内容](https://heavy_code_industry.gitee.io/code_heavy_industry/pro000-dev-story/chapter11/content.html)

## 1.2 服务器端应用程序

我们要开发的就是**服务器端应用程序**。

<img src="media/img008.efc85bce.jpg" alt="./images" style="zoom: 50%;" />

## 1.3 业务

项目中的功能就是业务。

## 1.4 请求和响应

1. 发生在饭馆的请求和响应

   <img src="media/img006.101afb76.jpg" alt="./images" style="zoom: 50%;" align = "left"/>

2. 项目中的请求和响应

   <img src="media/img007.4bc9f5ad.jpg" alt="./images" style="zoom:50%;" align = "left"/>

## 1.5 项目的逻辑构成

- <font color='#66ccff'>请求</font>：请求是项目中==最基本==的逻辑单元，就像万事万物都由原子构成

  > 举例：点超链接跳转到注册页面

- <font color='#66ccff'>功能</font>：一个功能包含很==多个请求==

  > 举例：注册用户功能
  >
  > - 请求1：点超链接跳转到注册页面
  > - 请求2：发送请求获取短信验证码
  > - 请求3：检查用户名是否可用
  > - 请求4：提交表单完成注册

- <font color='#66ccff'>模块</font>：一个模块包含很==多功能==

  > 举例：用户信息管理模块
  >
  > - 功能1：用户注册功能
  > - 功能2：用户登录功能
  > - 功能3：个人中心——账户安全功能
  > - 功能4：个人中心——账户绑定功能
  > - 功能5：个人中心——收货地址功能
  > - 功能6：个人中心——我的银行卡功能

- <font color='#66ccff'>子系统</font>：根据项目规模的不同，子系统这层逻辑概念可能有也可能没有。如果设置了子系统，那么子系统中也必然包含很==多模块==。其实庞大项目的子系统已经相当于一个项目了，甚至比小型项目整个都大。

  > 举例：认证中心子系统
  >
  > - 模块1：用户信息管理模块
  > - 模块2：权限管理模块
  > - 模块3：授权管理模块
  > - 模块4：权限检查模块

- <font color='#66ccff'>项目</font>：为了解决现实生活中的实际问题开发一个项目，这个项目就是为这个需求提供的一整套解决方案。

  > 举例：电商项目
  >
  > - 子系统1：认证中心子系统
  > - 子系统2：商品管理子系统
  > - 子系统3：购物车子系统
  > - 子系统4：仓储子系统
  > - 子系统5：物流子系统
  > - 子系统6：订单子系统

<img src="media/img009.ec3f116f.jpg" alt="./images" style="zoom:50%;" align="left"/>

## 1.6 架构

1. 概念: **『架构』其实就是项目的『结构』**。只不过『结构』这个词太小了，不适合用来描述项目这么大的东西，所以换了另一个更大的词：架构。所以当我们聊一个项目的架构时，我们聊的是项目是由哪些部分组成的。

2. 发展演变历程

   1. 单一架构: 一个项目就是一个工程，这样的结构就是单一架构，也叫all in one。我们现在的JavaWeb阶段. SSM阶段都是学习单一架构开发技术。

   2. 分布式架构: 一个项目中包含很多工程，每个工程作为一个模块。模块之间存在调用关系。分布式架构阶段的技术分为两类：

      - Java框架：`SpringBoot`, `SpringCloud`, `Dubbo`等等。

      - 中间件：`Redis`,  `ElasticSearch`, `FastDFS`. `Nginx`. `Zookeeper`. `RabbitMQ`等等。![./images](media/img010.49c05d88.jpg)


3. 单一架构技术体系

   - 视图：用户的操作界面+数据的动态显示
     - 前端技术：HTML/CSS/JavaScript
     - 服务器端页面模板技术：Thymeleaf
     
     - 控制层：处理请求+跳转页面
       - 服务器：Tomcat
       - 控制器：Servlet
       - 域对象：request. session. servletContext
       - 过滤器：Filter
       - 监听器：Listener
       - 异步交互：Ajax
     
- 业务逻辑层：业务逻辑计算
  
- 持久化层：操作数据库


<img src="media/img011.679a73a9.jpg" alt="./images" style="zoom:50%;" align="left"/>

## 1.7 本阶段技术体系

![./images](media/img012.f516db8e.jpg)

## 1.8 本阶段案例简介

<img src="media/img013.14f712a9.jpg" alt="./images" style="zoom:50%;" />



# 第二章 HTML&CSS

## 1. 单一架构回顾

我们从现在的JavaWeb阶段到后面学习SSM框架阶段都是在学习单一架构项目开发的技术。而在JavaWeb阶段由于重点是探讨如何实现Web开发，所以必须学习一部分前端开发的技术。本节就是让大家明确我们现在要学习的内容在整个架构体系中处于什么位置。

### 1.1 单一架构技术体系

<img src="media/img001.679a73a9.jpg" alt="./images" style="zoom:50%;" />

### 1.2 视图层

严格来说视图分成两层：

- 前端技术：HTML/CSS/JavaScript
- 服务器端页面模板技术：Thymeleaf

其中HTML. CSS. JavaScript都是工作在浏览器上的，所以它们都属于前端技术。而Thymeleaf是在服务器上把动态内容计算出具体数据，所以严格来说Thymeleaf是后端技术。

<img src="media/img002.9ee16606.jpg" alt="./images" style="zoom:50%;" />

> 这里大家会有个疑问：为什么在『视图』这个地方已经有HTML. CSS. JavaScript这些前端技术了，能够生成用户可以操作的界面，那为什么还需要Thymeleaf这样一个后端技术呢？
>
> 简单来说原因是Thymeleaf=HTML+动态数据，而HTML不支持动态数据，这部分需要借助Thymeleaf来完成。
>
> 更进一步的细节咱们讲到那再说啦！

### 1.3 Web2.0

Web2.0是相对于更早的网页开发规范而提出的新规范。Web2.0规范之前的网页开发并没有明确的将HTML. CSS. JavaScript代码分开，而是互相之间纠缠在一起，导致代码维护困难，开发效率很低。

> 在开发中我们把这样彼此纠缠. 互相影响的现象称为<font color='#66ccff'>『耦合』</font>。而把耦合在一起的东西拆解开，让他们彼此独立出来称为<font color='#66ccff'>『解耦』</font>。各个组成部分独立完成自己负责的功能，和其他模块无关称为<font color='#66ccff'>『内聚』</font>。
>
> 将来大家经常会听到一句话：软件开发提倡<font color='#66ccff'>『 高内聚，低耦合』</font>。
>
> 一个软件项目只有做到了高内聚. 低耦合才能算得上结构严谨，模块化程度高，有利于开发和维护。

所以Web2.0规范主张将网页代码分成下面三个部分：

- **结构**：由HTML实现，负责管理网页的==内容==。将来网页上不管是静态还是动态的数据都是填写到HTML的标签里。
- **表现**：由CSS实现，负责管理网页内容的==表现形式==。比如：颜色. 尺寸. 位置. 层级等等。也就是给数据穿上一身漂亮的衣服。
- **行为**：由JavaScript实现，负责实现网页的==动态交互效果==。比如：轮播图. 表单验证. 鼠标滑过显示下拉菜单. 鼠标滑过改变背景颜色等等。

## 2. HTML简介

### 2.1 名词解释

HTML是<font color='#EE0000'>H</font>yper <font color='#EE0000'>T</font>ext <font color='#EE0000'>M</font>arkup <font color='#EE0000'>L</font>anguage的缩写。意思是**『超文本标记语言』**。

### 2.2 超文本

HTML文件本质上是==文本文件==，而普通的文本文件只能显示字符。但是HTML技术则通过<font color='#66ccff'>HTML标签</font>把其他网页. 图片. 音频. 视频等各种多媒体资源引入到当前网页中，让网页有了非常丰富的呈现方式，这就是超文本的含义——本身是文本，但是呈现出来的最终效果超越了文本。

<img src="media/img003.d30e6c24.jpg" alt="./images" style="zoom:50%;" />

### 2.3 标记语言

说HTML是一种<font color='#66ccff'>『标记语言』</font>是因为它不是向Java这样的『编程语言』，因为它是由一系列『标签』组成的，没有常量. 变量. 流程控制. 异常处理. IO等等这些功能。HTML很简单，每个标签都有它固定的含义和确定的页面显示效果。

* <font color='#66ccff'>标签</font>是通过一组尖括号+标签名的方式来定义的：

    ```html
    <p>HTML is a very popular fore-end technology.</p>
    ```
    
    * 这个例子中使用了一个p标签来定义一个段落，`<p>`叫<font color='#66ccff'>『开始标签』</font>，`</p>`叫<font color='#66ccff'>『结束标签』</font>。开始标签和结束标签一起构成了一个完整的标签。
    * 开始标签和结束标签之间的部分叫<font color='#66ccff'>『文本标签体』</font>，也简称<font color='#66ccff'>『标签体』</font>。

* 有的时候标签内部还带有<font color='#66ccff'>『属性』</font>：

    ```html
    <a href="http://www.xxx.com">show detail</a>
    ```

    * `href="http://www.xxx.com"`就是属性

      `href`是<font color='#66ccff'>『属性名』</font>，`"http://www.xxx.com"`是<font color='#66ccff'>『属性值』</font>。

* 还有一种标签是<font color='#66ccff'>『单标签』</font>：

    ```html
    <input type="text" name="username" />
    ```



### 2.4 HelloWorld

![./images](media/img004.27509f9e.jpg)

### 2.5 HTML文件结构

1.  文档类型声明
   * HTML文件中第一行的内容，用来告诉浏览器当前HTML文档的基本信息，其中最重要的就是当前HTML文档遵循的语法标准。
   
   * 这里我们只需要知道HTML有4和5这两个大的版本
   
        * HTML4版本的文档类型声明是：
        
            ```html
            <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
            "http://www.w3.org/TR/html4/loose.dtd">
            ```
        
        * HTML5版本的文档类型声明是：
        
            ```html
            <!DOCTYPE html>
            ```
        
        >  现在==主流的技术选型都是使用HTML5==，之前的版本基本不用了。
   
   * 历史上HTML的各个版本：

        | 版本名称 | 年份 |
        | -------- | ---- |
        | HTML     | 1991 |
        | HTML+    | 1993 |
        | HTML2.0  | 1995 |
        | HTML3.2  | 1997 |
        | HTML4.01 | 1999 |
        | XHTML1.0 | 2000 |
        | HTML5    | 2012 |
        | XHTML5   | 2013 |

2. <font color='#66ccff'>根标签</font>: 

   * `html标签`是整个文档的根标签，所有其他标签都必须放在html标签里面。上面的文档类型不能当做普通标签看待。

     > 所谓『根』其实是『树根』的意思。在一个树形结构中，根节点只能有一个。

3. <font color='#66ccff'>头部</font>

   *  `head标签`用于定义文档的头部，其他==头部元素==都放在head标签里。

   * 头部元素包括`title标签`, `script标签`, `style标签`, `link标签`, `meta标签`等等。

4. `主体`: 
   * `body标签`定义网页的主体内容，在浏览器窗口内==显示的内容==都定义到body标签内。

5. 注释
   * HTML注释的写法是：

        ```html
        <!-- 注释内容 -->
        ```
   
   * 注释的内容不会显示到浏览器窗口内，是开发人员用来对代码内容进行解释说明。

6. HTML语法规则

   - 根标签(`html标签`)有且只能有一个

   - 无论是双标签还是单标签都必须正确关闭

   - 标签可以嵌套但==不能交叉==嵌套

   - 注释不能嵌套

   - 属性必须有值，值必须加引号，单引号或双引号均可

   - 标签名不区分大小写但建议使用小写

# 3. HTML基础标签

以文章的组织形式展示数据是HTML最基本的功能了，网页上显示的文章在没有做任何CSS样式设定的情况下如下图所示：

<img src="media/img005.e6101bab.png" alt="./images" style="zoom:50%;" />

本节我们要学习的HTML标签如下表：

| 标签名称 | 功能                   |
| -------- | ---------------------- |
| h1~h6    | 1级标题~6级标题        |
| p        | 段落                   |
| a        | 超链接                 |
| ul/li    | 无序列表               |
| img      | 图片                   |
| div      | 定义一个前后有换行的块 |
| span     | 定义一个前后无换行的块 |

为了方便编写代码，我们在IDEA中创建一个静态Web工程来操作：

<img src="media/img006.e8277f2a.png" alt="./images" style="zoom: 67%;" />

## 3.1标题标签

1. 代码

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>

        <h1>这是一级标题</h1>
        <h2>这是二级标题</h2>
        <h3>这是三级标题</h3>
        <h4>这是四级标题</h4>
        <h5>这是五级标题</h5>
        <h6>这是六级标题</h6>

    </body>
    </html>
    ```

2. 页面显示效果

   <img src="media/img007.d99982ba.png" alt="./images" style="zoom:80%;" align="left"/>

**注意**：标题标签前后有换行

## 3.2 段落标签

1. 代码

```html
<p>There is clearly a need for CSS to be taken seriously by graphic artists. The Zen Garden aims to excite, inspire, and encourage participation. To begin, view some of the existing designs in the list. Clicking on any one will load the style sheet into this very page. The code remains the same, the only thing that has changed is the external .css file. Yes, really.</p>
```

2. 页面显示效果

![./images](media/img008.02928c12.png)

**注意**：段落标签前后有换行。

## 3.3超链接

1. 代码

    ```html
    <a href="page02-anchor-target.html">点我跳转到下一个页面</a>
    ```

2. 页面显示效果

    <img src="media/image-20220905215919017.png" alt="image-20220905215919017" style="zoom:67%;" align="left"/>

​	点击后跳转到href属性指定的页面

## 3.4 路径

在我们整个Web开发技术体系中，<font color='#66ccff'>『路径』</font>是一个贯穿始终的重要概念。凡是需要获取另外一个资源的时候都需要用到路径。要想理解路径这个概念，我们首先要认识一个概念：<font color='#66ccff'>『文件系统』</font>。

### 3.4.1 文件系统

* 我们写代码的时候通常都是在Windows系统来操作，而一个项目开发完成后想要让所有人都能够访问到就必须『部署』到服务器上，也叫『发布』。而服务器通常是Linux系统。
* Windows系统和Linux系统的文件系统有很大差别，为了让我们编写的代码不会因为从Windows系统部署到了Linux系统而出现故障，实际开发时不允许使用**物理路径**。

     > 物理路径举例：
     >
     > D:\aaa\pro01-HTML\page01-article-tag.html
     >
     > D:\aaa\pro01-HTML\page02-anchor-target.html

* 幸运的是不管是Windows系统还是Linux系统环境下，目录结构都是**树形结构**，编写路径的规则是一样的。

  ![路径](media/路径.jpg)

所以我们**以项目的树形目录结构为依据**来编写路径就不用担心操作系统平台发生变化之后路径错误的问题了。有了这个大前提，我们具体编写路径时有两种具体写法：

- 相对路径
- 绝对路径（建议使用）

### 3.4.2相对路径

* 相对路径都是以==当前位置==为基准来编写的。假设我们现在正在浏览a页面，想在a页面内通过超链接跳转到z页面。

  <img src="media/相对路径1.jpg" alt="相对路径1" style="zoom:50%;" />

  ①那么按照相对路径的规则，我们现在所在的位置是a.html所在的b目录：

  <img src="media/相对路径2.jpg" alt="相对路径2" style="zoom:50%;" />

  ②z.html并不在b目录下，所以我们要从b目录出发，向上走，进入b的父目录——c目录：

  <img src="media/相对路径3.jpg" alt="相对路径3" style="zoom:50%;" />

  ③c目录还是不行，继续向上走，进入c的父目录——d目录：

  <img src="media/相对路径4.jpg" alt="相对路径4" style="zoom:50%;" />

  ④在从d目录向下经过两级子目录——e目录、f目录才能找到z.html：

  <img src="media/相对路径5.jpg" alt="相对路径5" style="zoom:50%;" />

* 所以整个路径的写法是：

     ```html
     <a href="../../e/f/z.html">To z.html</a>
     ```

>  可以看到使用相对路径有可能会很繁琐，而且在后面我们结合了在服务器上运行的Java程序后，相对路径的基准是有可能发生变化的，所以**不建议使用相对路径**。

### 3.4.3 绝对路径

1. 通过IDEA服务器打开HTML文件
   * 测试绝对路径的前提是通过IDEA的内置服务器访问我们编写的HTML页面——这样访问地址的组成结构才能和我们以后在服务器上运行的Java程序一致。![./images](media/img017.bebe5e65.png)

2. 服务器访问地址的组成

   ![./images](media/img018.1f565e83.jpg)

3. 绝对路径的写法

   * 绝对路径要求必须是==以正斜线开头==。这个开头的正斜线在整个服务器访问地址中对应的位置如下图所示：

     ![绝对路径写法](media/绝对路径写法.jpg)

   * 这里标注出的这个位置代表的是**『服务器根目录』**，从这里开始我们就是在服务器的内部查找一个具体的Web应用。

   * 所以我们编写绝对路径时就从这个位置开始，**按照目录结构找到目标文件**即可。拿前面相对路径中的例子来说，我们想在a.html页面中通过超链接访问z.html。此时路径从正斜线开始，和a.html自身所在位置没有任何关系：

     <img src="media/绝对路径写法 (2).jpg" alt="绝对路径写法 (2)" style="zoom:67%;" />

        ```html
        <a href="/d/e/f/z.html">To z.html</a>
        ```

4. 具体例子

* 编写超链接访问下面的页面：

  ![,具体例子](media/,具体例子.png)

    ```html
    <a href="/aaa/pro01-HTML/animal/cat/miao.html">Cat Page</a>
    ```

5. 小结

   为了和我们后面学习的内容和正确的编码方式保持一致，建议大家从现在开始就使用绝对路径。

## 3.5 换行

1. 代码

```html
We would like to see as much CSS1 as possible. CSS2 should be limited to widely-supported elements only. The css Zen Garden is about functional, practical CSS and not the latest bleeding-edge tricks viewable by 2% of the browsing public. <br/>The only real requirement we have is that your CSS validates.
```

2. 页面显示效果

![./images](data:image/png;base64,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)

## 3.6 无序列表

1. 代码

```html
<ul>
    <li>Apple</li>
    <li>Banana</li>
    <li>Grape</li>
</ul>
```

2. 页面显示效果

   <img src="media/image-20220905233344370.png" alt="image-20220905233344370" style="zoom:67%;" />

## 3.7 图片

1. 准备图片文件

   <img src="media/image-20220905233848188.png" alt="image-20220905233848188" style="zoom:67%;" />

2. 代码

   src属性用来指定图片文件的路径，这里同样按我们前面说的使用**『绝对路径』**。

    ```html
    <img src="/src/images/mio.png" style="zoom: 10%;"/>
    ```

3. 页面显示效果

   ![image-20220905233939293](media/image-20220905233939293.png)

## 3.8 块

**『块』**并不是为了显示文章内容的，而是为了方便结合CSS对页面进行布局。块有两种，div是前后有换行的块，span是前后没有换行的块。

把下面代码粘贴到HTML文件中查看他们的区别：

```html
<div style="border: 1px solid black;width: 100px;height: 100px;">This is a div block</div>
<div style="border: 1px solid black;width: 100px;height: 100px;">This is a div block</div>

<span style="border: 1px solid black;width: 100px;height: 100px;">This is a span block</span>
<span style="border: 1px solid black;width: 100px;height: 100px;">This is a span block</span>
```

页面显示效果为：

<img src="media/image-20220905234039285.png" alt="image-20220905234039285" style="zoom:50%;" />

## 3.9 HTML实体

在HTML文件中，<、>等等这样的符号已经被赋予了特定含义，不会作为符号本身显示到页面上，此时如果我们想使用符号本身怎么办呢？那就是使用HTML实体来转义。

![./images](media/img027.8ca23282.png)

# 4. 使用HTML表格展示数据

