### Servlet 体系结构概述

##### 1、servlet 的概念：

servlet 是用于处理请求并动态生成响应内容返回给客户端的一种接口规范，只是定义了接口，并没有去实现，有些类似于JDBC的样子，它也有许多不同的实现类。



##### 2、体系结构

顶级接口：Servelt 接口	ServeltConfig 接口

继承自 Servelt 的接口和实现类：

- 顶级父接口：Servlet。
  - 次级实现类或接口：GenericServlet 实现类、ServletRequest 接口、ServletResponse 接口。
    - Http请求处理：HttpServlet 实现类、HttpServletRequest 接口、HttpServletResponse 接口

![image-20230418212329756](D:/macdown/图片/Servlet体系结构/image-20230418212329756.png)



一般处理请求比较常用的是带Http请求的类，对于Http相关的请求支持和方法都比较全面，推荐使用。