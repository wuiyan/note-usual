## LomBok-小辣椒插件学习笔记

#### 一、LomBok插件概述

LomBok插件是一款插件化注解API，可以在Java程序编译时根据注解为程序添加一些必需的通用代码，如常用的Getter、Setter、有参和无参构造方法等。

使用这款插件可以在很大程度上简化程序员所需要书写的代码量，但也有可能会带来一些意想不到的小问题，使程序的可读性变差。



#### 二、LomBok插件配置方法

1、导入依赖包，可以通过maven或者手动导入的方式完成。

2、在IDEA中安装LomBok插件(在IDEA的新版本中默认是绑定安装的)

插件的作用：在java文件编译之后的class文件中确实会包含get、set方法，但在书写源码时是没有这些方法的定义的，IDEA会认为这是错误，所以这个时候就需要一个lombok插件来解决问题。

3、重启IDEA后，就可以开始使用LomBok插件的功能了，如果在运行时有报错，可能是插件没能正常加载，再次重启IDEA就好。



#### 三、LomBok插件常用注解

##### 1、@Getter

作用：用来给成员变量生成相应的get方法，同时可以为生成的方法指定访问修饰符，默认修饰符是 public ，也可手动指定修饰符。

位置：指定在类上，会对当前类的所有字段都生成相应的get方法，指定在字段上，只会对当前字段生效，当类和字段都设定上后，字段部分的设定优先生效。

注：@Getter注解在指定类的时候，默认不会对 静态变量 生效，需要手动在静态字段上指定才可以，对于 final 常量可以正常生效。

##### 2、@Setter

作用：用来给成员变量生成相应的set方法，同时可以为生成的方法指定访问修饰符，默认修饰符是 public ，也可手动指定修饰符。

位置：同 @Getter 的用法。

注：@Setter注解在指定类的时候，默认也不会对 静态变量 生效，需要手动在静态字段上指定才可以，但对于 final 常量不可以生效。

```java
@Getter
@Setter(AccessLevel.PRIVATE)	//设定生成的set方法是private的
public class LomBok {
    int id = 10;
}//可在类上直接设置@Getter和@Setter注解
```

##### 3、@Accessors

作用：这个注解要与@Getter与@Setter搭配使用，用来修改默认的setter与getter方法的形式。

位置：可指定在类和字段上。

**属性**：

3.1、chain 属性，属性值有true和false两种，默认是false，打开后可以让set方法支持链式调用，也就是set方法会返回对象本身。

3.2、fluent 属性，属性值有true和false两种，默认也是false，打开后会使get和set方法的名字变得和成员变量的名称一样，通过重载去调用git和set。

注：@Getter和@Setter注解生成的get和set方法的命名规则

默认是在get或set后将原变量名的开头字母大写，再拼接到一块，若原本就是大写，则不进行改变。

```java
int id;		getId();	//将成员变量首字母大写后拼接。
int Id		getId();	//原名称大写则不变。
```



##### 4、@ToString

作用：用于格式化输出对象和toString方法类似，生成方法时只会使用类中的非静态成员变量。

位置：只能指定在类上。

**属性**：

- includeFieldNames = true    // 输出时是否显示字段名 ，默认是true   
- exclude = {"name"}     // 排除某些字段，可多选，用逗号分隔就好
-  of = {"age"}      // 只输出某些字段，和exclude不能同时使用
-  callSuper = true //是否输出父类对象的toString信息， 默认false
- doNotUseGetters   // 输出时不使用get方法获取成员属性

@ToString.Include.rank  用于排序，数字越大排序越靠前，只能适用于字段和方法类型。

@ToString.Include.name  用于自定义字段名称，也只能适用于字段和方法。

##### 5、@EqualsAndHashCode

作用：用于生成equals和hashCode方法，也是只会使用非静态成员变量。

位置：只能指定在类上。

**属性**：

- callSuper 用于比较父类对象是否相同，对于继承的子类的equals方法中最好使用这个选项，可以提高安全性，防止将父类对象和子类对象当成相同的对象。默认是false
- cacheStrategy 对于hashCode方法是否添加缓存，在使用时会将第一次结果保存在变量中，下次就可以直接调用使用，默认是从不。
- exclude = {"name"}     // 排除某些字段，可多选，用逗号分隔就好
-  of = {"age"}      // 只输出某些字段，和exclude不能同时使用



##### 6、构造器相关注解

**共同点：**

都只能在类上使用。

```
staticName：自定义构造方法的名称，原构造方法会被设定为私有。

access：设定方法的访问修饰符。默认是public

onConstructor：生成指定注解的构造器，默认为空
```

###### (1)、@AllArgsConstructor

作用：生成所有参数的构造方法，

###### (2)、@NoArgsConstructor

作用：生成无参的构造方法

**特有属性**：force：可以将所有 final 字段初始化为 0、null、false。默认为 false。

###### (3)、@RequiredArgsConstructor

作用：会将类中所有带有`@NonNull注解` 的或者带有`final修饰的成员变量`生成对应的构造方法。



##### 7、@Data

作用：相当于注解集合。效果等同于 **@Getter + @Setter + @ToString + @EqualsAndHashCode + @RequiredArgsConstructor** 效果同和这5个注解的效果。

注：该注解并不包括@NoArgsConstructor和@AllArgsConstructor注解效果，且在有继承的情况下不建议使用此注解，因为这里的equals无法进行精确设定，容易出现问题。

位置：指定在类中

**属性**：staticConstructor：为生成的构造器指定自定义方法名称。

##### 8、@value

作用： 将字段都变成不可变类型：**使用final修饰， 同时还包含@ToString、@EqualsAndHashCode、@AllArgsConstructor 、@Getter**(注意只有Getter没有Setter)和@Data的作用比较类似。

位置：指定在类上

**属性**：staticConstructor：为生成的构造器指定自定义方法名称。 



##### 9、@SneakyThrows

作用：用于构建try-catch语句将方法包括起来。

位置：指定在方法或构造方法上

**属性**：value： 用于指明要抛出的异常类型，默认是Throwable



##### 10、@Cleanup

作用：主要用来修饰 IO 流相关类, 会在 finally 代码块中对该资源进行 close();

位置：作用于局部变量中

**属性**：value：指定关闭方法的方法名，默认是close。

注：也可以使用try-with-resources的写法，也可以自动关闭歹有autoclose的连接。



##### 11、@Builder

作用：建造者模式，一种比较重要的创建对象的过程，不再是生成一个构造方法，而是使用了一个内部类来完成对象的创建，可以精细化、分步去进行一个类对象的创建。

构造器类的的set方法支持链式调用

位置：类、方法、构造器上使用

**属性**：

`builderMethodName`：生成builder内部类对象的方法名称，默认是：builder

`buildMethodName`： 返回建造对象的方法名称，默认是：build

`builderClassName`：内部类的类名，

`toBuilder`： 将build重新变回builder进行再次设置，默认为false



**@Builder.Default** 

使用在属性上，用于指定某个属性的默认值，若无设置则使用默认值。

**@Builder.ObtainVia**

使用在属性上和方法内的参数里，用于对toBuilder方法生成的builder对象的值或方法的获取方式做一些限定。

```
field：将某个属性的值用另外一个属性的值去代替
method：将某个属性的值获取方法改成自己指定的方法
```



#### 四、LomBok插件优缺点

优点：

可以帮助我们省去很多冗余代码，使项目的开发更加高效、便捷。

缺点：

有很高的侵入性，在项目中有人使用会使得所有的人都要去使用这种插件，

会让代码可读性变差，调试起来也更加麻烦

同时过度使用可能会创造很多用不到的方法，造成隐患。