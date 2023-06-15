## SSM框架使用教程

### 通过注解配置spring框架

##### 1、添加spring的坐标依赖

```xml
<!--spring 框架依赖-->
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.19</version>
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

