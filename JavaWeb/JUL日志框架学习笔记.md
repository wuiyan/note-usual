### JUL日志框架

#### 一、JUL框架介绍

JUL 全称 : Java util logging ，Java原生的日志框架，主要用于小型项目，使用方便。

作用：可以按照不同的层次分级去显示信息，将程序运行中的信息更加规范化的输出。



#### 二、JUL组成介绍

- **Logger**：也被称作记录器，是日志实现的基础。
- **Handler**|Appenders：也被称作处理器，一个 Logger 可同时存在多个处理器，以其具体实现类决定日志的输出位置是控制台、文件还是网络上的其他日志服务器。
- **Layouts**：也被称为Formatters，它负责对日志事件中的数据进行转换和格式化，它决定了日志记录的最终显示形式。
- **Level**：日志输出级别，Logger 和 Handeler 都有一个 Level 级别，如两者出现冲突时，以级别高的等级为准。
- **Filters**：过滤器，可以自主过滤掉不需要的日志信息，可以在 Logger 和 Handeler 上分别设置。

![流程图](D:/macdown/图片/JUL日志框架学习笔记/流程图.png)



#### 三、日志等级划分

在JUL中日志被划分为七个等级，由高到低分别为：

```
SEVERE（最高等级）代表程序出现了严重错误
WARNING	用于表示某些警告信息
INFO（默认等级）常规消息
CONFIG
FINE
FINER
FINEST（最低等级）
```

还有两个特殊等级 OFF和ALL，OFF用于关闭日志记录，ALL用于启用所有的消息日志记录。

需注意，虽然日志等级被分为了七个等级，**但默认只显示INFO及以上等级。**



#### 四、日志框架的基础使用

1、获取当前类的 logger 对象

```java
Logger logger = Logger.getLogger(MainTest.class.getName());
// logger 对象不能直接创建，需要用静态方法去获取，方法的参数是想要记录日志信息类的完整包名，可用 类名.class.getName()去获取。
```

2、输出日志信息

```java
logger.severe("我是一条错误信息");
logger.warning("我是一条警告信息");
logger.info("我是一条日志信息");
// 可通过这种方式去输出日志信息，输出位置要看handler处理器是什么，默认是向控制台输出。
```



#### 五、日志框架的自定义配置

对于JUL日志框架来说，可以设定其日志输出等级，日志处理器类型，输出日志格式，过滤器配置。

可以通过两种方式去设置：程序中设置和配置文件设置，文件设置是比较常用且方便的格式。

1、常用配置文件设置

```
#全局配置

# 顶级RootLogger关联的Handler，多个Handler使用逗号隔开
# 对于其他Logger，如果没有指定自己的Handler，则默认使用此handlers
handlers= java.util.logging.FileHandler

# 默认全局日志级别，Logger和Handler都可以设置自己的日志级别来覆盖此级别
.level= ALL


# Handler 配置

# FileHandler定义
# 日志文件存储位置
java.util.logging.FileHandler.pattern = ./jul%u.log
# 单个文件的最大字节数，0代表不限制
java.util.logging.FileHandler.limit = 50000
# 文件数量上限，多个文件为jul0.log.0，jul0.log.1 ...
java.util.logging.FileHandler.count = 5
# 日志级别
java.util.logging.FileHandler.level = info
# 日志追加方式
java.util.logging.FileHandler.append = true
# Handler对象采用的字符集
java.util.logging.FileHandler.encoding = UTF-8
# 日志格式，使用系统默认的简单格式
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter


# 特定Logger 配置

# 设置名称为org.example.Main的Logger对象的日志级别为WARNING
org.example.Main.level = WARNING
```

加载配置文件：

```java
LogManager logManager = LogManager.getLogManager();   logManager.readConfiguration(Resources.getResourceAsStream("logging.properties"));
```



2、程序中设置

```java
	// 获取当前类的日志记录器对象
    Logger logger = Logger.getLogger(MainTest.class.getName());

    // 关闭默认配置，即不使用父Logger的Handlers
    logger.setUseParentHandlers(false);

    // 设置记录器的日志级别为ALL
    logger.setLevel(Level.ALL);

    // 日志记录格式，使用简单格式转换对象
    SimpleFormatter simpleFormatter = new SimpleFormatter();

    // 控制台输出Handler,并且设置日志级别为INFO，日志记录格式
    ConsoleHandler consoleHandler = new ConsoleHandler();
    consoleHandler.setLevel(Level.INFO);
    consoleHandler.setFormatter(simpleFormatter);

    // 文件输出Handler,并且设置日志级别为FINE，日志记录格式
    FileHandler fileHandler = new FileHandler("./jul.log");
    fileHandler.setLevel(Level.FINE);
    fileHandler.setFormatter(simpleFormatter);

    // 记录器关联处理器，即此logger对象的日志信息输出到这两个Handler进行处理
    logger.addHandler(consoleHandler);
    logger.addHandler(fileHandler);

    // 日志记录输出
```



#### 六、日志处理器的分类

|                  | ConsoleHandeler（控制台日志处理器） |   FileHandler（文件日志处理器）   |
| ---------------- | :---------------------------------: | :-------------------------------: |
| **日志输出位置** |            输出到控制台             |            输出到文件             |
| **日志输出格式** |         默认是普通文本格式          |           默认是xml格式           |
| **写入方式**     |                 无                  | 可以设置是否追加写入，默认是false |
|                  |       Logger的默认日志处理器        |                无                 |



