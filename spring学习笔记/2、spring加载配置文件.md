###	2、spring加载配置文件

####	1、spring 单配置文件加载

*1.1 根据相对路径加载资源*

```java
ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
```

1.2 *根据绝对路径加载资源*

```java
ApplicationContext context = new ClassPathXmlApplicationContext("C:\存储文件\Java_file\spring\src\main\resources\spring.xml");
```



####	2、spring 多配置文件加载

2.1 *方法一：直接将配置文件的名称添加进来*

```java
ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml", "daos.xml");
```

*2.2 方法二：设置总配置文件将多个配置文件合并成一个*

```xml
<!-- 主配置文件 -->

<import resource = "spring.xml"/>
<import resource = "dao.xml"/>
```

设置完主配置文件后将其按照导入单个配置文件的方法导入就好。





<u>*注意使用相对路径导入文件时记得将*xml文件所在的目录设置成为资源目录，以免无法查找到文件。</u>