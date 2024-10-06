[分布式存储工程师的自我修炼 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/605385509)

重学面向对象 探索框架级别的代码组织结构

1 从JVM层面看对象 ——  看懂一行代码 Interface i = new Class(); 
2 面向对象 组合、封装与管理

使用对象的功能 还是维护对象的状态？

封装的核心理念：海哥视频4:30 https://www.bilibili.com/video/BV1uv4y1S7tE/?spm_id_from=333.999.0.0&vd_source=820cf03df770fe8d1c4e011511bd34bc


3 面向对象 至顶向下看继承链
4 面向对象 接口=协议 接口的继承=协议功能的拼接(搭积木)
5 从面向对象看EventLoop体系与Spring的BeanFactory体系
6 从Tomcat Connector和Container相互依赖出发 学习Spring ApplicationContext对各个相互依赖组件的封装管理


框架中的 数据结构与并发
1 Disruptor并发队列 - 环形数组的极致优化
2 Netty中的数据结构 Map、SelectKeySet与FastThreadLocal
3 Netty的channel pipeline体系
4 Netty的 pipeline vs Tomcat pipeline
5 对象池的并发回收 - 并发问题的终结
6 Netty内存池 —— Devil is in the details
7 对比xxl-job、Spring、Netty的 时间轮以及定时任务

初探框架设计
框架的本质：
    公共逻辑的高度抽象
    灵活易用的接口开放 
    安全性与异常定位 异常是框架能力的边界 了解能力的边界 非常重要
    
1 从Mybatis出发 浅谈框架的设计目标与设计原则
2 从Spring FactoryBean出发 浅谈Spring扩展点与容器感知
3 从返回值出发看方法层级的抽象 Future与Promise  到Mono/CompletabeFuture
4 对比Spring的事件和Netty的事件
3 从Runbable接口出发 探索更高层面的抽象：函数式接口
5 从Netty Handler、xxl-job Handler、Disruptor Handler出发 函数式接口的一般应用
6 函数式接口的终极应用：reactor/netty、webflux 中的Bootstrap配置
7 回到Mybatis 看看框架的兜底策略
以及Netty pipeline最开始的出站处理器 Unsafe

分布式理论
1. 从xxl-job 初探分布式中遇到的问题以及解决方案
深入xxl-job 谈谈框架级别的优化与兜底策略
2. 从zk的线性一致性 看分布式问题的一般解决方案 以及取舍
3. GFS 线性写与并发写
4. CAP与BASE 悲观与乐观https://juejin.cn/post/7345821800880422975 这篇文章下的评论很有意思
5. jRaft 分布式算法实践
6. 从ACID到 BASE理论  Hbase分片集群实践。BASE理论中，软状态的定义允许存在中间态，最终一致性的定义接受延迟性，所以这也是为什么BASE理论的论文中说：BASE是分布式场景中ACID理论代替品的本质原因！ 对可用性的定义也不同：如果用户数据跨五个数据库服务器存储，根据BASE理论，一台数据库出现故障，仅仅只会影响到20%的用户
7. CAP到ACID ----- 分布式事务 分布式事务/多线程事务问题中的 乐观与悲观https://www.hollischuang.com/archives/1580
8. 事物与分布式数据库 https://www.bilibili.com/video/BV1ss4y1R7FB/?spm_id_from=333.337.search-card.all.click&vd_source=820cf03df770fe8d1c4e011511bd34bc
9. Rocket MQ —— 集Raft与分布式事务 之大成之作！
https://mp.weixin.qq.com/s/TT_NpT3xV9Yhmva73E85Hg
2022rmq OSPP
RocketMQ 实现高可用多副本架构的关键：基于 Raft 协议的 commitlog 存储库 DLedger
https://www.jianshu.com/p/05b64a6242f6
https://www.cnblogs.com/bigcoder84/p/18327276
https://www.infoq.cn/article/7xeJrpDZBa9v*GDZOFS6



事务专题
http://47.103.216.138/archives/tag/%E4%BA%8B%E5%8A%A1


响应式reactor/netty  vert.x  WebFlux
这个是真他妈的难 真难 真难 操

java函数式编程 流式编程

设计模式背后的思想 —— 面向对象三大特性的最佳实践
1 模版方法与策略背后的思想
模版方法 抽象类与封装类 传进来的实例不同 或是自己本身实例不同
2 工厂与策略的最佳实践
3 责任链模式：多级容器的多级责任链实现(结合工厂+模版方法)



杂
1 软硬结合 利用cpu和磁盘的性质 —— Kafka初探 顺序与软硬结合
2 细看ClassLoader，重新认识双亲委派，从Spring Resource/SPI/Tomcat 体会java的动态扩展性与伸缩性
到这里才知道Tomcat处理多请求时 不同的Servlet的Class对象就是通过ClassLoader加载的 之后再通过反射实例化对象
WebappClassLoader实现 与 ShenyuClassLoader实现

这才意识到 reflect 反射 这个命名的含义 就是对java程序设定的某种指令触发时，java程序会对其做出反应，这也是java扩展性和灵活性的基石

顺便复习一下创建java对象的5种方法


3 事务专题 从数据库事务 到多线程事务 再到分布式事务

横评事务 —— 事务在不同层面的实现
    1 MySQL Mybatis Spring
    2 纵向整合 明确整个调用流程Spring —> Mybatis —> MySQL
    3 分布式事务解决方案 —— 乐观与悲观 不同条件下的 trade-off
实战应用 海哥专题
    1 单线程事务与RPC/MQ调用的最佳实践
    2 多线程事务
    3 分布式事务
    4 多事务并发可见性问题 结合MySQL事务隔离级别分析(可重复读)
幂等设计 防止重复insert 使用分布式锁防并发
https://www.bilibili.com/video/BV1uv4y1S7tE/?spm_id_from=333.999.0.0&vd_source=820cf03df770fe8d1c4e011511bd34bc

4 一个请求到响应的过程中到底发生了什么？
Web服务器层面的探索 服务的不同层级
Tomcat —— filter / valve         服务器层面的通用拦截
Spring MVC —— interceptor      web服务编码层面的拦截
Spring —— AOP         方法层面的拦截

抽象层面不同 粒度不同





重学面向对象 探索框架级别的代码组织结构

1 从JVM层面看对象 ——  看懂一行代码 Interface i = new Class(); 
2 面向对象 组合、封装与管理

使用对象的功能 还是维护对象的状态？

封装的核心理念：海哥视频4:30 https://www.bilibili.com/video/BV1uv4y1S7tE/?spm_id_from=333.999.0.0&vd_source=820cf03df770fe8d1c4e011511bd34bc


3 面向对象 至顶向下看继承链
4 面向对象 接口=协议 接口的继承=协议功能的拼接(搭积木)
5 从面向对象看EventLoop体系与Spring的BeanFactory体系
6 从Tomcat Connector和Container相互依赖出发 学习Spring ApplicationContext对各个相互依赖组件的封装管理


框架中的 数据结构与并发
1 Disruptor并发队列 - 环形数组的极致优化
2 Netty中的数据结构 Map、SelectKeySet与FastThreadLocal
3 Netty的channel pipeline体系
4 Netty的 pipeline vs Tomcat pipeline
5 对象池的并发回收 - 并发问题的终结
6 Netty内存池 —— Devil is in the details
7 对比xxl-job、Spring、Netty的 时间轮以及定时任务

初探框架设计
框架的本质：
    公共逻辑的高度抽象
    灵活易用的接口开放 
    安全性与异常定位 异常是框架能力的边界 了解能力的边界 非常重要
    
1 从Mybatis出发 浅谈框架的设计目标与设计原则
2 从Spring FactoryBean出发 浅谈Spring扩展点与容器感知
3 从返回值出发看方法层级的抽象 Future与Promise  到Mono/CompletabeFuture
4 对比Spring的事件和Netty的事件
3 从Runbable接口出发 探索更高层面的抽象：函数式接口
5 从Netty Handler、xxl-job Handler、Disruptor Handler出发 函数式接口的一般应用
6 函数式接口的终极应用：reactor/netty、webflux 中的Bootstrap配置
7 回到Mybatis 看看框架的兜底策略
以及Netty pipeline最开始的出站处理器 Unsafe

分布式理论
1. 从xxl-job 初探分布式中遇到的问题以及解决方案
深入xxl-job 谈谈框架级别的优化与兜底策略
2. 从zk的线性一致性 看分布式问题的一般解决方案 以及取舍
3. GFS 线性写与并发写
4. CAP与BASE 悲观与乐观https://juejin.cn/post/7345821800880422975 这篇文章下的评论很有意思
5. jRaft 分布式算法实践
6. 从ACID到 BASE理论  Hbase分片集群实践。BASE理论中，软状态的定义允许存在中间态，最终一致性的定义接受延迟性，所以这也是为什么BASE理论的论文中说：BASE是分布式场景中ACID理论代替品的本质原因！ 对可用性的定义也不同：如果用户数据跨五个数据库服务器存储，根据BASE理论，一台数据库出现故障，仅仅只会影响到20%的用户
7. CAP到ACID ----- 分布式事务 分布式事务/多线程事务问题中的 乐观与悲观https://www.hollischuang.com/archives/1580
8. 事物与分布式数据库 https://www.bilibili.com/video/BV1ss4y1R7FB/?spm_id_from=333.337.search-card.all.click&vd_source=820cf03df770fe8d1c4e011511bd34bc
9. Rocket MQ —— 集Raft与分布式事务 之大成之作！
https://mp.weixin.qq.com/s/TT_NpT3xV9Yhmva73E85Hg
2022rmq OSPP
RocketMQ 实现高可用多副本架构的关键：基于 Raft 协议的 commitlog 存储库 DLedger
https://www.jianshu.com/p/05b64a6242f6
https://www.cnblogs.com/bigcoder84/p/18327276
https://www.infoq.cn/article/7xeJrpDZBa9v*GDZOFS6



事务专题
http://47.103.216.138/archives/tag/%E4%BA%8B%E5%8A%A1


响应式reactor/netty  vert.x  WebFlux
这个是真他妈的难 真难 真难 操

java函数式编程 流式编程

设计模式背后的思想 —— 面向对象三大特性的最佳实践
1 模版方法与策略背后的思想
模版方法 抽象类与封装类 传进来的实例不同 或是自己本身实例不同
2 工厂与策略的最佳实践
3 责任链模式：多级容器的多级责任链实现(结合工厂+模版方法)



杂
1 软硬结合 利用cpu和磁盘的性质 —— Kafka初探 顺序与软硬结合
2 细看ClassLoader，重新认识双亲委派，从Spring Resource/SPI/Tomcat 体会java的动态扩展性与伸缩性
到这里才知道Tomcat处理多请求时 不同的Servlet的Class对象就是通过ClassLoader加载的 之后再通过反射实例化对象
WebappClassLoader实现 与 ShenyuClassLoader实现

这才意识到 reflect 反射 这个命名的含义 就是对java程序设定的某种指令触发时，java程序会对其做出反应，这也是java扩展性和灵活性的基石

顺便复习一下创建java对象的5种方法


3 事务专题 从数据库事务 到多线程事务 再到分布式事务

横评事务 —— 事务在不同层面的实现
    1 MySQL Mybatis Spring
    2 纵向整合 明确整个调用流程Spring —> Mybatis —> MySQL
    3 分布式事务解决方案 —— 乐观与悲观 不同条件下的 trade-off
实战应用 海哥专题
    1 单线程事务与RPC/MQ调用的最佳实践
    2 多线程事务
    3 分布式事务
    4 多事务并发可见性问题 结合MySQL事务隔离级别分析(可重复读)
幂等设计 防止重复insert 使用分布式锁防并发
https://www.bilibili.com/video/BV1uv4y1S7tE/?spm_id_from=333.999.0.0&vd_source=820cf03df770fe8d1c4e011511bd34bc

4 一个请求到响应的过程中到底发生了什么？
Web服务器层面的探索 服务的不同层级
Tomcat —— filter / valve         服务器层面的通用拦截
Spring MVC —— interceptor      web服务编码层面的拦截
Spring —— AOP         方法层面的拦截

抽象层面不同 粒度不同