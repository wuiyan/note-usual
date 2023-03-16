## 关于Java中注解的使用

#### 一、注解的概述

注解(Annotation)，也叫元数据，是一种代码级别的说明，可以在程序中设定标记，这些标记可以用于检查代码、生成文档、使用反射等。

简单来说,注解是一种将meta标记(meta-tag)与程序元素关联的机制,允许编译器(compiler)或JVM从有注解的元素提取程序行为,必要时生成相互依赖的代码。

注解可以在包、类、方法、字段、局部变量、方法参数等几乎所有的东西前面声明，声明方法是 ‘@注解名’ 。后面还可添加参数。

###### 1.1、注解的种类

注解分为两种，一种是预设注解，由Java原生提供的注解类型，另一种是元注解，用来定义注解的注解，常常用来自定义其他注解

#### 二、预设注解

- `@Deprecated`：表示某个类或方法已过时，有更好的解决方案。
- `@Override`：提示子类要复写父类中被 @Override 修饰的方法。
- `@SuppressWarnings`：指示编译器忽略注解中声明的警告。
- `@SafeVarargs`：提醒开发者不要用参数做一些不安全的操作。
- `@Functionallnterface`：用翔实的注释类型来表示一个接口类型声明的目的是空功能接口由java语言规范定义。

#### 三、元注解

- `@Retention`：指定了注解的保留范围。
- `@Documented`：指定了注解将被 Javadoc 工具提取成文档，包含到 Javadoc 中去。
- `@Target`：指定了注解运用的地方。
- `@Inherited`：指定了注解将具有继承性。
- `@Repeatable`：指定了注解是可重复的。



###### 3.1、@Retention

当 @Retention 应用到一个注解上的时候，它解释说明了这个注解的的存活时间。

- RetentionPolicy.SOURCE 注解只在源码阶段保留，在编译器进行编译时它将被丢弃忽视。(存活的时间是在源码和编译中)
- RetentionPolicy.CLASS 注解只被保留到编译进行的时候，它并不会被加载到 JVM 中。
- RetentionPolicy.RUNTIME 注解可以保留到程序运行的时候，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们。

```Java
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
}
// 指定 TestAnnotation 可以在程序运行周期被获取到，因此它的生命周期非常的长。
```

###### 3.2、@Target

用于给定义的注解限制其生效范围。

- ElementType.ANNOTATION_TYPE 可以给一个注解进行注解
- ElementType.CONSTRUCTOR 可以给构造方法进行注解
- ElementType.FIELD 可以给属性进行注解
- ElementType.LOCAL_VARIABLE 可以给局部变量进行注解
- ElementType.METHOD 可以给方法进行注解
- ElementType.PACKAGE 可以给一个包进行注解
- ElementType.PARAMETER 可以给一个方法内的参数进行注解
- ElementType.TYPE 可以给一个类型进行注解，比如类、接口、枚举
  

例子：

```Java
@Target(ElementType.TYPE)
public @interface TestAnnotation {
}
```



#### 四、自定义注解

###### 1、自定义注解的定义

使用 @interface 关键字进行定义，内部只可以放属性，不可以有方法，属性也可以设置默认值，使用关键字default定义，

```java
public @interface Test{
    String str();
    String id() default "123456";
}
//定义一个名为Test的注解，内部有两个个String类型的成员变量，id的默认值被设定为“123456”。
```

###### 2、使用元注解修饰自定义注解

```java
@Retention(RetentionPolicy.RUNTIME)		//表示该注解的存在时间是从开始到运行时
@Target(ElementType.METHOD)		//表示该注解只能在方法上使用
public @interface Test{
    String str();
}
```



#### 五、注解的作用

注解虽然不直接影响代码的语义，但可以通过使用注解可以在程序的不同阶段获得注解上的信息，从而通过使用反射等方式动态的对程序做出一定的干预。

