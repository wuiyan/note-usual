## 	Mybatis 学习笔记

##### 一、Mybatis 简介

Mybatis 是Java用来管理数据库的一个半自动的持久层开源框架，相比于JDBC来说可以较为方便的实现复杂查询和动态查询，还可以将查询的结果映射为对象，使数据的处理更加方便。

##### 二、实现流程

1、首先导入相应的 jar 包，放到IDEA中，添加为库，可直接在官网下载。

2、通过xml配置文件，进行数据库基础配置和映射器mapper的设置。

3、之后使用SqlSessionFactoryBuilder 从xml配置文件创建 SqlSessionFactory 工厂，用于生成相应的SqlSession 会话，这个也是整个Mybatis最主要使用的对象，由它执行映射器中设定的SQL语句。

```xml
<!-- Mybatis的主配置文件设定 ，各属性是有先后顺序的，不能打乱放置-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

```xml
<!-- mapper 映射器的配置文件
XML 映射语句的示例，它应该可以满足上个示例中 SqlSession 的调用。 -->

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```



Mybatis 的实现一般还要结合接口去使用，

mapper的配置文件 ——》映射到接口上

mapper的SQL语句 ——》映射成接口上的某个方法

优点：使用起来比较方便，直接调用接口相应的方法就可完成SQL语句的实现。还可以实现对返回值类型的限制。



映射器中可以设定SQL语句的返回值是对应转换对象的那一个属性值，是可以手动设定映射规则的。

可以使用\<typeAlias /\>设定别名,用于简便书写，也可以配置扫描路径，让其自动生成别名，默认是首字母小写。



数据源概述：

在原本的JDBC中是通过 DriverManager.getConnection() 方法去创建连接，但这种方式每一个数据库连接对象都对应着的一个物理数据库连接，这种方式连接的建立和关闭的资源消耗较大。

Mybatis中则是使用了数据源来对数据库连接进行统一管理，一般采用池化技术，在开始的时候就创建好N个连接，再有需求时提供相应的连接信息，然后数据源会根据内部机制，合理的分配连接对象给我们，这样就无需再次连接而是可以直接使用线程的数据库连接对象



事务管理

特性：要么全部成功执行 ，要么全部都不执行

事务特性：原子性、一致性、隔离性、持久性。

隔离级别：

读未提交：其他事务会读取当前事务尚未提交的更改（不是从数据库中读取，是从缓冲区中读取），但如果当前事务回滚事务的话，其他事务会出现”脏读“现象，即读到的是无意义的数据。

读已提交：其他事务会读取当前事务已提交的更改（从数据库中直接读取），但是如果在其他事务读取完后，当前事务恰好进行了事务提交的话，就会导致其他事务两次读取的数据不一致，进而会出现”虚读“现象。

可重复读：当其他事务执行时，不允许修改数据，但是只是禁止了UPDATE操作，如果两个事务同时进行插入操作，则可能使数据库出现表记录数不一样的情况，从而引发“幻读”现象。

串行化：每次只进行一个事务处理，可以有效防止上述问题的出现，但是缺点是效率太低，不是很推荐使用。

| 隔离级别 |  脏读  |  虚读  |  幻读  |
| :------: | :----: | :----: | :----: |
| 读未提交 |  可能  |  可能  |  可能  |
| 读已提交 | 不可能 |  可能  |  可能  |
| 可重复读 | 不可能 | 不可能 |  可能  |
|  串行化  | 不可能 | 不可能 | 不可能 |

默认的事务管理机制就是使用的JDBC的方式，通过默认的Connection对象去完成事务的各种操作。

还有种是使用MANAGED的事务管理机制不提供具体的实现，而是通过其他的程序容器（如Spring）去实现对事务的管理。



##### 动态SQL

可以根据条件的不同，使用不同的SQL语句进行查询。

```xml
<!-- if 
使用动态 SQL 最常见情景是根据条件包含 where 子句的一部分。 -->

<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

```xml
<!-- choose、when、otherwise
有时候，我们不想使用所有的条件，而只是想从多个条件中选择一个使用。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。 -->

<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```







##### 缓存机制

Mybatis 的缓存是用来提高数据访问速度的一种方式，当有数据请求时，Mybatis会优先去缓存中寻找数据，如果没有再去数据库查询，可以节省大量的时间，其流程为： 二级缓存——》一级缓存——》数据库

其缓存可分为一级缓存和二级缓存，一级缓存的作用域是一个SqlSession 会话，二级缓存可以横跨多个SqlSession 会话，作用于一个mapper。

一级缓存特点：不能关闭，只适用于当前SqlSession 会话，当使用插入语句时会自动更新缓存。但当在不同SqlSession 发生数据更新后，缓存不能同步更新，容易出现问题。

二级缓存特点：可以自主选择是否开启，可使用于多个SqlSession 会话，使用DML语句也会自动更新缓存，可适用于多SqlSession 会话更新的情况，但当使用其他方式更新数据库后，也会导致缓存不一致的情况。

共同特点：只有当事务提交后，才会将结果提交到缓存中去。



##### 注解开发

Mybatis支持注解和xml配置文件混合使用的方式，可以方便的切换使用，单使用注解可以比较方便快捷的实现一些简单的SQL语句，其用法是在接口里的方法上，直接使用注解的方式进行设定。

可以通过注解实现查询、插入、更新、联合查询等。

注：若列名中存在关键字则使用反引号包裹。

单表查询返回列表

```java
    @Results({
            @Result(column = "sid",property = "sid",id = true),
            @Result(column = "name",property = "name"),
            @Result(column = "sex",property = "sex"),
            @Result(column = "age",property = "age")
    }
    )
    @Select("select * from student")
    List<Students> getStudents();
```

多表联合查询

```java
    @Results(
            {
                    @Result(column = "id",property = "id",id = true),
                    @Result(column = "sid",property = "students",one = @One(select = "getStudent")),
                    @Result(column = "bid",property = "books",one = @One(select = "getBook"))
            }
    )
    @Select("select * from borrow")
    List<Borrow> getBorrow();
    @Select("select * from student where sid=#{sid}")
    Students getStudent(int sid);
    @Select("select * from book where bid=#{bid}")
    Books getBook(int bid);
```

