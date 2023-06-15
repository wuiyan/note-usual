##	Java IDEA使用maven配置spring环境

####	1、IDEA创建maven项目

文件——新建项目——选择maven项目——设置名称、JDK版本、存储位置、模板、加载目录——开始创建

**重点：**

1. *设置maven的项目模板，开始可选择org.apache.maven.archetypes:maven-archetype-quickstart，比较轻量化的一个模板*
2. *可设置模板的加载目录，默认有内部、本地、远程三种选项，一般默认即可。*



####	2、等待maven加载配置环境

maven会从云端源中进行加载所需的依赖并保存到本地，依赖全部下载完成后会进行 *Generating project in Batch mode* 操作，这一步本质上是访问云端的一个	*archetype-catalog.xml* 文件，这个文件大小是比较大的，在网络不好的情况下会加载漫长时间的，所以可以将文件下载到本地，可以提高很多的加载速度。

- 下载地址：[Central Repository: (maven.org)](https://repo1.maven.org/maven2/)
- 文件保存位置：本地仓库路径\org\apache\maven\archetype\archetype-catalog\3.2.1
- 配置修改位置：设置——构建——maven——Runner——VM Options
- 配置修改：添加	-DarchetypeCatalog=internal ，有三个可选值，分别为：internal：内部的、remote：远程的、local：本地的，默认是remote远程的。

#### 3、配置pom.xml文件

1、在pom.xml中配置spring的坐标依赖

```xml
<dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.4.RELEASE</version>
      </dependency>
```

2、设置JDK版本

```xml
 <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>

  </properties>
```

#### 4、配置完成

到此spring的基础配置环境就完成了