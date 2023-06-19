## SSM框架使用教程

### 通过注解配置spring框架

##### 1、添加spring框架的坐标依赖

spring-context中包含了：spring-beans、spring-core、spring-aop、spring-expression。

spring-aspects包含了：aspectjweaver依赖

```xml
<!-- spring 框架依赖-->
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.19</version>
</dependency>
<!-- AOP 依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.3.13</version>
</dependency>
```

##### 2、创建spring配置类

作用相当于spring的xml配置文件，使用@Configuration注解指定

```java
@Configuration
public class SprigConfiguration {

}
```

##### 3、创建基础Bean对象

```java
@Configuration
public class SprigConfiguration {
    @Bean
    public TextInfo textInfo(){ // 创建一个Bean对象
        return new TextInfo();
    }
}
```

##### 4、通过配置类加载spring配置

```Java
public static void main(String[] args) {
    AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SprigConfiguration.class);
}

```

##### 5、获取Bean对象

```java
public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SprigConfiguration.class);
        TextInfo textInfo = context.getBean(TextInfo.class);
        System.out.println(textInfo);
    }
```



### 通过注解配置springmvc

##### 1、添加springmvc的坐标依赖：

```xml
<!--        SpringMvc 依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.13</version>
        </dependency>
<!--        servlet 依赖-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
<!--  应注意这两个的版本，有时版本不匹配会导致项目无法启动 -->
```

##### 2、web模块和配置类实现

检查当前项目有无web模块，若没有则在项目设置中添加，添加后注意修改其路径，让其处于src/main/目录下。

springmvc需要多个配置类才可以实现其全部功能，主要有：

- WebInitializer类：继承AbstractAnnotationConfigDispatcherServletInitializer，用于代替web.xml配置DispatcherServlet
- WebServletConfiguration类，mvc关于webServlet的配置类，需要着重配置。
- MainConfiguration类，mvc关于root的配置类，可暂时空置。

对于WebInitializer类：

```java
public class WebInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{MainConfiguration.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebServletConfiguration.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
        // 用于配置根目录
    }
}
```

对于WebServletConfiguration类：

```java
@ComponentScan("org.example.controller")
@EnableWebMvc
@Configuration
public class WebServletConfiguration {
    @Bean
    public InternalResourceViewResolver viewResolver() {
        // 当前配置是使用的springmvc内置的视图解析器，一般被用来解析jsp文件，不能解析html文件
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setOrder(1);
        viewResolver.setPrefix("/");
        viewResolver.setSuffix(".jsp");
        return viewResolver;
    }
}
```

若使用Thymeleaf配置视图解析器

```xml
<!--引入依赖-->
<dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
            <version>3.0.14.RELEASE</version>
</dependency>
```

```java
	 // 配置模板解析器    
	@Bean
    public SpringResourceTemplateResolver springResourceTemplateResolver(){
        SpringResourceTemplateResolver resolver = new SpringResourceTemplateResolver();
        resolver.setSuffix(".html");
        resolver.setPrefix("/");
        return resolver;
    }

    // 配置模板引擎Bean,此时自动注入的其实是上面的模板解析器，@Autowired会优先按照类型去寻找，最终找到实现了此接口的类，即SpringResourceTemplateResolver，SpringResourceTemplateResolver是ITemplateResolver接口的实现类
    @Bean
    public SpringTemplateEngine springTemplateEngine(@Autowired ITemplateResolver iTemplateResolver){
        SpringTemplateEngine engine = new SpringTemplateEngine();
        engine.setTemplateResolver(iTemplateResolver);
        return engine;
    }

// 使用ThymeleafViewResolver作为视图解析器，用于解析HTML页面
    @Bean
    public ThymeleafViewResolver thymeleafViewResolver (@Autowired SpringTemplateEngine springTemplateEngine){
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setOrder(1);
        resolver.setCharacterEncoding("UTF-8");
        resolver.setTemplateEngine(springTemplateEngine);
        return resolver;
    }
```

##### 3、创建测试类

```java
@Controller
public class MainController {
    @RequestMapping(value = "/index",produces="application/json;charset=UTF-8")
    @ResponseBody
    public String index(){
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("name","测试");
        jsonObject.put("password","123456");
        System.out.printf(jsonObject.toJSONString());
        return jsonObject.toJSONString();
    }
    @RequestMapping(value = "/temp")
    public ModelAndView temp(){
        return new ModelAndView("temp");
    }
}
```

##### 4、tomcat启动配置

检查tomcat部署有无工件，若没有工件，则自行创建，创建时需注意，应基于当前的web模块进行创建，创建类型为web应用程序 展开型。

url和应用程序上下文都可调整，但需保持同步。之后就可以请求测试返回结果了。

##### 5、附录--自定义mvc中的web配置

如果想对于WebServletConfiguration类进行一些自定义配置可以实现WebMvcConfigurer接口，同时要加上@EnableWebMvc注解来启用mvc的自定义配置，不然自定义配置可能无法生效。

常用方法：

```java
 /* 拦截器配置 */
void addInterceptors(InterceptorRegistry var1);
/* 视图跳转控制器 */
void addViewControllers(ViewControllerRegistry registry);
/**
     *静态资源处理
**/
void addResourceHandlers(ResourceHandlerRegistry registry);
/* 默认静态资源处理器 */
void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer);
/**
     * 这里配置视图解析器
 **/
void configureViewResolvers(ViewResolverRegistry registry);
/* 配置内容裁决的一些选项*/
void configureContentNegotiation(ContentNegotiationConfigurer configurer);
/** 解决跨域问题 **/
public void addCorsMappings(CorsRegistry registry) ;
```



### 通过注解配置Mybatis框架

##### 1、添加Mybatis的坐标依赖

```xml
<!-- mybatis 框架-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.13</version>
</dependency>
<!-- mysql 连接驱动-->
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>8.0.32</version>
</dependency>
<!-- spring 整合 Mybatis依赖-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.6</version>
</dependency>
<!-- spring 数据库资源管理-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.13</version>
</dependency>
<!-- Java注解类，提供@Resource注解-->
<dependency>
    <groupId>javax.annotation</groupId>
    <artifactId>javax.annotation-api</artifactId>
    <version>1.3.2</version>
</dependency>
```

##### 2、添加配置类MybatisConfiguration

它相当于Mybatis的xml配置文件本身，下面是相应配置

```java
@Configuration
@MapperScan("org.example.mapper")	// 用于设定mapper所在的包位置，可自动将包中的接口注册为Bean
@ComponentScan("org.example.service") // 将指定包下的含@Component的类注册为Bean对象
public class MybatisConfiguration {
    @Bean   // 此注解一般只能用到配置类中，相当于xml文件中的配置Bean对象的方式
    public DataSource dataSource(){ 
        // 创建池化数据源，类似xml的设置数据库
        return new PooledDataSource("com.mysql.cj.jdbc.Driver",
                "jdbc:mysql://127.0.0.1:3306/maze_zero",
                "root","123456");
    }
    @Bean   // 配置SqlSessionFactory，基于之前配置的数据源，通过工厂可以获得相应的SqlSession连接
    public SqlSessionFactoryBean sqlSessionFactoryBean(@Autowired DataSource dataSource){
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        factoryBean.setDataSource(dataSource);
        return factoryBean;
    }
}
```

##### 3、添加mapper映射

在配置类指定的mapper包下创建mapper接口，编写相应的SQL语句，对应xml相应的mapper配置

```java
@Mapper
public interface MapMapper {
    @Select("select * from map_info where level_number = 1 ")
    MapInfo getMapInfo();
}
```

##### 4、添加具体实现

编写相应的实现类，去执行相应的mapper中的SQL语句，为了解耦，可以让实现类实现接口，通过接口去调用具体的实现类，实现类和接口要放在配置类中指定的Bean对象的扫描目录

```java
public interface MapService { // 设置接口
    public MapInfo getMapInfo();
}

@Component
public class MapServiceImpl implements MapService{ // 让实现类实现接口
    @Resource	// @Resource注解比较适合使用在字段的注入上
    MapMapper mapper; // 配置好mapper扫描器后，就有spring的容器去管理mapper，可以直接使用。

    @Override
    public MapInfo getMapInfo() { // 通过调用mapper中的方法获取返回对象
        return mapper.getMapInfo();
    }
}
```

##### 5、编写测试类，测试实验效果

```java
public static void main(String[] args) {
    AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MybatisConfiguration.class);
    // 此时导入的是Mybatis的配置类
    MapService bean = context.getBean(MapService.class);
    // 传入实现类的接口，实际返回的是接口的实现类
    MapInfo mapInfo = bean.getMapInfo();
    // 调用方法，获得最终返回对象
    System.out.println(mapInfo);
    }
```

##### 附录--配置类中的部分注解说明

| 注解名称 |   @Configuration   |                      @Bean                       |                 @ComponentScan                 |            @MapperScan             |
| :------: | :----------------: | :----------------------------------------------: | :--------------------------------------------: | :--------------------------------: |
|   作用   | 指定当前类为配置类 | 在配置类中使用，指定当前方法返回的对象为Bean对象 | 将指定目录存在@Component注解的类注册为Bean对象 | 将指定目录的mapper接口类注册为Bean |
|   类比   | 相当于xml配置文件  |             相当于xml中的<bean>标签              |                  相当于扫描器                  |            相当于扫描器            |

