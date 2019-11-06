# SpringBoot-Case
Several cases about SpringBoot

## 一.SpringBoot父工程pom配置:

```xml
<!-- 父级依赖 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.6.RELEASE</version>
</parent>

<!-- 使用springmvc和spring的jar包   -->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```



## 二. SpringBoot 创建父工程和子工程，并对子工程的dependency进行管理

### 1.父级工程Pom的package为pom

```xml
<!-- 父级依赖 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.6.RELEASE</version>
</parent>
<packaging>pom</packaging>
```

### 2.父级工程Pom中,添加dependency Management, depenedencies和dependency等.如:

```xml
<dependencyManagement>
    <dependencies>
       <dependency> 
         <groupId>com.springframework.boot</groupId>
         <artifactId>spring-boot-dependencies</artifactId>
         <version>1.5.6.RELEASE</version>
         <type>pom</type>
         <scope>import</scope>
       </dependency>
    </dependencies>
</dependencyManagement>

<!-- 父工程添加后，子工程将直接使用<build>中的plugin -->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
 </build>
```

### 3.创建new module,并在该Pom中添加dependencies,如:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```



## 三.SpringBoot 自定义属性的类型安全配置方法ConfigurationProperties：

### 1.在application.properties文件中自定义属性，如：

```properties
book.author = aa;
book.name = bb;
```

### 2.控制类java中使用注释：

```java
@ConfigurationProperties(prefix="book")
```

### 3.控制类使用Generate: getter and setter



## 四.SpringBoot 如何配置profile:

### 1.创建application-prod.properties, application-sit.properties, application-dev.properties文件

### 2.通过在application.properties中设置spring.profiles.active = dev/sit/prod来指定活动的profile



## 五.SpringBoot如何进行整合测试:

### 1.在测试的子module里添加：

```xml
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-test</artifactId>
 <scope>test</scope>
</dependency>

<!-- junit为测试类plugin  -->
<dependency>
 <groupId>junit</groupId>
 <artifactId>junit</artifactId>
 <scope>test</scope>
</dependency>
```

### 2.创建controller包，添加Controller.java：

```java
@Controller
@EnableAutoConfiguration
public class SpringController{
    
    ...
    public static void main(String[] args)     {
        SpringApplication.run(SpringController.class, args);
    }
}
```

### 3.在子工程的/src/test/java 添加package,添加测试类的java,如：

```java
@SpringBootTest(classes = SpringController.class)  //SpringBootTest 测试的类目标
@RunWith(SpringJUnit4ClassRunner.class)  //指明Runwith 进行测试的类工具
@WebAppConfiguration  //WebAppConfiguration Springboot整合web
public class TestSpringController {
    //使用@Autowired将springcontroller对象进行注入
    @Autowired
    private SpringController springController;
    @Test
    public void test1(){
        //使用assertEquals方法 比较测试值和actual值
    TestCase.assertEquals(this.springController.yes(), "hello");
    }
}
```



## 六.SpringBoot如何分离启动类和控制类：

### 1.在src/main/java下新建package,新建启动类applications.java，启动类中添加注释：

```java
@EnableAutoConfiguration+@ComponentScan(“com.project.controller”)
```

或

```java
@SpringBootApplication(scanBasePackage = {"com.project"})
```

### 2.在applications.java中添加：

```java
public class SpringApplications {
    public static void main(String[] args){
       SpringApplication.run(SpringApplications.class, args);
    }
}
```
### 3.在src/main/java下新建package，新建控制类controller.java,控制类添加注释:

```java
@Controller
```



## 七.SpringBoot支持Rest风格,添加@PathVariable注释，如：

```java
@RequestMapping("/info/{msg}")
public String show(@PathVariable String msg){
    return msg;
}
```



## 八.SpringBoot设置开发模式：

### 1.在POM文件中添加两个dependency:

```xml
<dependency>
 <groupId>org.springframework</groupId>
 <artifactId>springloaded</artifactId>
 </dependency>
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-devtools</artifactId>
</dependency>
```



## 九.多环境日志输出，根据不同环境定义不同的日志输出：

### 1.在logback-spring.xml中使用springProfile节点来定义,如：

```xml
<!-- 测试环境+开发环境 -->
<springProfile name = "test,dev">
 <logger name="com.project.controller" level="info" />
</springProfile>

<！-- 生产环境 -->
<springProfile name = "prod">
    <logger name="com.project.controller" level="ERROR"/>
</springProfile>
```

### 2.在application.properties中：

```properties
spring.profiles.active = {envname}
```



## 十.SpringBoot 访问静态资源

## 1.Spring Boot默认静态文件路径：

从classpath的 static, public或 /META-INF/RESOURCES文件夹或从ServletContext根目录提供静态内容：默认 js在resources/public目录中；image在 resources/static目录中

## 2.自定义静态文件路径，js,css,image等：

spring.resources.static-location = classpath:/staitc/



## 十一.SpringBoot使用FastJson解析json数据：

### 方法一：

#### 1.让启动类applications.java继承WebMvcConfigureAdapter

#### 2.重写 configureMessageConverters方法：

```java
@Override

public void configureMessageConverters(List<HttpMessageConverter<?>>
converters) {
    
    //创建FastJson消息转换器
    FastJsonHttpMessageConverter convert = new  FastJsonHttpMessageConverter();
    //创建FastJson的配置对象
    FastJsonConfig config = new FastJsonConfig();
    //对Json数据进行格式化
    config.setSerializerFeatures(SerializerFeature.PrettyFormat);

    //config对象传入转换器
    convert.setFastJsonConfig(config);
    converters.add(convert);
}
```

#### 3.设置application.properties,添加：

```properties
spring.http.encoding.force = true
```

### 方法二：

#### 1.在启动类application.java中添加注释：

```java
@Bean
public HttpMessageConverters fastJsonMessageConverter(){
    
    //创建FastJson消息转换器
    FastJsonHttpMessageConverter convert = new  FastJsonHttpMessageConverter();

    //创建FastJson的配置对象
    FastJsonConfig config = new FastJsonConfig();
    //对Json数据进行格式化
    config.setSerializerFeatures(SerializerFeature.PrettyFormat);

    //config对象传入转换器
    convert.setFastJsonConfig(config);
    //将返回对象注入到HttpMessageConverter容器中
    HttpMessageConverter<?> con = convert;
    
    return new HttpMessageConverters(con);
}
```



## 十二.SpringBoot自定义拦截器：

### 1.建立Interceptor.java类，继承 WebMvcConfigurerAdapter

### 2.重写HandlerInterceptor方法

```java
@Configuration//声明是一个配置
public class MyInterceptor extends WebMvcConfigurerAdapter {
    
    @Override
    public void addInterceptors(InterceptorRegistry registry) {                         HandlerInterceptor inter = new HandlerInterceptor();
        public boolean preHandle(HttpServletRequest httpServletRequest，HttpServletResponse httpServletResponse, Object o) throws Exception {
         System.out.println("自定义拦截器");
         return true;
        }

       public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
        ...
       }

       public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
        ...
       }
                                                              
       registry.addInterceptor(inter).addPathPatterns("/**");
    }
}
```



## 十三.SpringBoot 实现异步调用：

### 1.创建asyncservice类package,新建AsyncService 接口，如:

```java
public interface AsyncService {

 Future<String> doTask1()throws Exception;
 Future<String> doTask2()throws Exception;
 Future<String> doTask3()throws Exception;
}
```

### 2.创建实现类，实现service类接口,添加@Service、@Async注释，如：

```java
@Service
public class AsyncServiceImpl implements AsyncService {

 private static Random random = new Random();

 @Async
 @Override
 public Future<String> doTask1() throws Exception{
     System.out.println("任务一开始执行");
     long start = System.currentTimeMillis();
     Thread.sleep(random.nextInt(10000));
     long end = System.currentTimeMillis();
     System.out.println(end-start);
     //AsyncResult 为 Future的子类
     return new AsyncResult<>("任务一结束");
 }

 @Async
 @Override
 public Future<String> doTask2() throws Exception{
     System.out.println("任务二开始执行");
     long start = System.currentTimeMillis();
     Thread.sleep(random.nextInt(10000));
     long end = System.currentTimeMillis();
     System.out.println(end-start);
     //AsyncResult 为 Future的子类
     return new AsyncResult<>("任务二结束");
 }
}
```

### 3.在控制类注入asyncservice接口，添加@Autowired注释，如：

```java
@Controller
public class TestController {
    
    @Autowired
    private AsyncService asyncService;

    @RequestMapping("/async")
    @ResponseBody
    public String asyncTest() throws Exception {
        long start = System.currentTimeMillis();
        
        Future<String> task1 = asyncService.doTask1();
        Future<String> task2 = asyncService.doTask1();
        Future<String> task3 = asyncService.doTask1();
        
        while(true){
            if(task1.isDone() && task2.isDone() && task3.isDone()){
                break;
            }
            Thread.sleep(1000);
        }
        
        long end = System.currentTimeMillis();
        return "" + (end-start);
    }
}
```
### 4.在启动类添加@EnableAsync注释，如：

```java
@EnableAsync//开启异步调用
@SpringBootApplication(scanBasePackages = "com.project")
public class SpringApplications {
    public static void main(String[] args)
    {
        SpringApplication.run(SpringApplications.class, args);
    }
}
```



## 十四. SpringBoot整合Thymeleaf:

### 1.在module中添加dependency:

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>
 <groupId>net.sourceforge.nekohtml</groupId>
 <artifactId>nekohtml</artifactId>
 <version>1.9.22</version>
</dependency>
```

### 2.在application.properties中添加thymeleaf配置：

```properties
<!-- 关闭thymeleaf缓存 开发时使用 否则没有实时画面 -->
spring.thymeleaf.cache = false
# 检查模板是否存在，然后呈现

spring.thymeleaf.check-template-location = true

# Content-Type值

spring.thymeleaf.content-type = text/html

# 启用MVC thymeleaf试图分辨率

spring.thymeleaf.enabled = true

# 模板编码

spring.thymeleaf.mode = LEGACYHTML5

# 在构建URL时附加查看名称的后缀

spring.thymeleaf.suffix = .html

# 在构建URL时预先查看名称的前缀

spring.thymeleaf.prefix = classpath:/templates/
```

### 3.在控制类添加模型，如：

```java
@Controller
public class IndexController {
    
    @RequestMapping("/thymeleaf")
    public String show(Model model){
        model.addAttribute("word","单词");
        return "index";
    }
}
```
### 4.在resources文件下创建templates文件夹，新建index.html文件



## 十五. SpringBoot整合QuartZ(任务工具):

### 1.新建job类，添加@Component注解，在run()函数前添加@Scheduled注解，如：

```java
@Component
public class Myjob {
    
    @Scheduled(fixedRate = 1000)
    public void run(){
        ...
    }
}
```

### 2.在启动类中添加@EnableScheduling注解



## 十五. SpringBoot整合JdbcTemplate(数据库模板):

### 1.在pom添加dependency:

```xml
<dependency>
 <groupId>com.springframework.boot</groupId>
 <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

### 2.在pom添加数据库的配置:

```xml
spring.datasource.driver-class-name = com.mysql.jdbc.Driver
spring.datasource.username = root
spring.datasource.password = root
spring.datasource.url = jdbc:mysql://localhost:3306/db1
```

### 3.新建com.project.pojo包，新建数据库表类，如Users.java:

```java
public class Users {
    
    private Integer id;
    private String name;
    
    public Integer getId(){
        return id;
    }
    
    public String getName(){
        return name;
    }
}
```

### 4.新建com.project.dao包，新建dao类，添加@Repository,@Autowired注释，如：

```java
@Repository
public class UserDao {
    
    @Autowired
    private JdbcTemplate jdbcTemplate;
    
    public void addUser(Users user){
        jdbcTemplate.update("insert into users(name,password,email) values(?,?,?)", user.getName(),user.getPassword(),user.getEmail());
    }
}
```

### 5.新建com.project.service包，新建service类,添加@Service，@Autowired注释，如：

```java
@Service
public class UserService {
    
    @Autowired
    private UserDao userDao;
    
    public void saveUser(Users user){ 
        userDao.addUser(user);
    }
}
```
### 6.新建com.project.controller包，新建控制类，添加@Controller,@Autowired注释，如：

```java
@Controller
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @ResponseBody
    @RequestMapping("/saveUser")
    public String save(){
        Users user = new Users();
        user.setName("AA");
        user.setEmail("aa@a.com");
        user.setPassword("aa");
        userService.saveUser(user);
        return "success";
    }
}
```



## 十六. mybatis逆向工程生成Mysql数据表的Mapper和Pojo:

### 1.添加mybatis.generator, mybatis, mybatis.spring.boot依赖:

```xml
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>    
    <version>1.3.2</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.0</version>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.1</version>
</dependency>
```
### 2.与src同级目录生成generatorConfig.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <context id="MybatisGenerator" targetRuntime="MyBatis3" >
        <commentGenerator>
            <!-- 是否去除自动生成的注解，true是，false否 -->
            <property name="supressAllComments" value="true"/>
        </commentGenerator>
        <!-- 数据库连接信息： -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/testdb?useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=false&amp;serverTimeZone=SYSTEM&amp;user=root&amp;password=Qwer1234"></jdbcConnection>
        <!-- 默认值false,把JDBC DECIMAL 和NUMBER类型解析为Integer，为true时 解析为 java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!-- 生成的po实体类的存放位置 -->
        <javaModelGenerator targetPackage="com.project.pojo" targetProject=".\src\main\java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
            <!-- 从数据库返回的值为清理前后的空格 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- 生成的mapper映射文件（XML）的存放位置 -->
        <sqlMapGenerator targetPackage="com.project.mapper" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        <!-- 生成的mapper接口（JAVA文件）的存放位置 -->
        <javaClientGenerator targetPackage="com.project.mapper" type="XMLMAPPER" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>
        <!-- 指定数据库表 根据表名，有多个表就写多条数据 -->
        <table tableName="testuser"></table>
    </context>
</generatorConfiguration>
```

### 3.新建java生成mapper文件：

```java
package com.project.app;

import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.exception.XMLParserException;
import org.mybatis.generator.internal.DefaultShellCallback;

public class GeneratorSqlmap {
    public void generate() throws Exception {
        List<String> warnings = new ArrayList<String>();
        boolean overwrite =true;
        File configFile = new File("generatorConfig.xml");
        System.out.println("--------"+configFile.getAbsolutePath());
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,callback,warnings);
        myBatisGenerator.generate(null);
    }
    public static void main(String[] args) {
        GeneratorSqlmap generatorSqlmap = new GeneratorSqlmap();
        try {
            generatorSqlmap.generate();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 4.生成的mapper.java文件中，添加行：

```java
Users selectByPrimaryKey(Integer id);
```



## 十七. SpringBoot整合mybatis，利用xml文件配置:

### 1.在pom文件中，添加dependency:

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>  
    <version>1.3.1</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.0</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.0.11</version>
</dependency>
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.1.2</version>
</dependency>
```
### 2.在resources目录新建mybatis文件夹，新建mybatis-config.xml文件,添加：

```xml
<?xml version="1.0" encoding="UTF-8" ？>
<!DOCTYPE configuration
      "-//mybatis.org//DTD Config 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

</configuration>
```

### 3.在resources目录添加application.yml,添加数据源属性配置:

```yml
spring:
    database:
        name:test
        url:jdbc:mysql://127.0.0.1:3306/testdb?useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=false&amp;serverTimeZone=SYSTEM&amp;user=root&amp;password=Qwer1234
        username:root
        password:...
        type:com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.cj.jdbc.Driver
        filters: stat
        maxActive: 20
        initialSize: 1
        maxWait: 60000
        minIdle: 1
        timeBetweenEvictionRunsMillis: 60000
        minEvictableIdleTimeMillis: 300000
        validationQuery: select 'x'
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        poolPreparedStatements: true
        maxOpenPreparedStatements: 20
        
mybatis:
  mapper-locations: classpath:mapping/UsersMapper.xml
  config-location: classpath:mybatis/mybatis-config.xml

pagehelper:
  helperDialect: mysql
  reasonable: true
  supportMethodsArguments: true
  params: count=countsql
```

### 4.在resources目录添加application.properties全局配置文件：

```properties
# <!-- 关闭thymeleaf缓存 开发时使用 否则没有实时画面 -->

spring.thymeleaf.cache = false

检查模板是否存在，然后呈现

spring.thymeleaf.check-template-location = true

Content-Type值

spring.thymeleaf.content-type = text/html

启用MVC thymeleaf视图分辨率

spring.thymeleaf.enabled = true

模板编码

spring.thymeleaf.mode = LEGACYHTML5

在构建URL时附加查看名称的后缀

spring.thymeleaf.suffix = .html

在构建URL时预先查看名称的前缀

spring.thymeleaf.prefix = classpath:/templates/

链接数据库的配置

spring.datasource.driver-class-name = com.mysql.jdbc.Driver
spring.datasource.username = ...
spring.datasource.password = ...
spring.datasource.url = jdbc:mysql://localhost:3306/testdb
```

### 5.新建com.project.pojo和com.project.mapper包:

将逆向工程的mapper包里的dbmapper.java文件放入项目com.project.mapper文件夹里

将逆向工程的dbmapper.xml文件放入项目resources的mapping文件夹下

将逆向工程的pojo包里的db.java和dbExample.java分别放入项目com.project.pojo文件夹里

### 6.创建接口，如在com.project.service下：

```java
package com.project.service;
import com.github.pagehelper.PageHelper;
import com.project.mapper.UsersMapper;
import com.project.pojo.Users;
import com.project.pojo.UsersExample;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class UsersServiceImpl implements UsersService{
    @Autowired
    private UsersMapper usersMapper;
    
    @Override
    public void addUser(Users user){
        usersMapper.insert(user);
    }
    @Override
    public List<Users> findUsers(int page, int rows){
        UsersExample example = new UsersExample();
        //Pagehelper plugin用于分页
        PageHelper.startPage(page, rows);
        List<Users> users = usersMapper.selectByExample(example);
        return users;
    }
}
```
### 7.创建实现类，如在com.project.service下:

```java
package com.project.service;
import com.project.pojo.Users;
import java.util.List;

public interface UsersService {
    //添加用户
    void addUser(Users user);
    //分页查找用户
    List<Users> findUsers(int page, int rows);
}
```

### 8.添加控制类，如在项目文件com.project.controller里,创建DBController.java文件：

```java
package com.project.controller;
import com.project.pojo.Users;
import com.project.service.UsersService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import java.util.List;

@Controller
public class UserController {

   @Autowired
   private UsersService userService;
   
   @ResponseBody
   @RequestMapping("/saveUser")
   public String save(){
       Users user = new Users();
       user.setId(4);
       user.setName("马六");
       user.setEmail("m@m.com");
       user.setPassword("m6");
       userService.addUser(user);
       return "success";
   }

   @RequestMapping("/findUsers/{page}/{rows}")
   public List<Users> findUsers(@PathVariable int page, @PathVariable int rows)
   {
       return userService.findUsers(page, rows);
   }
}
```

9.添加启动类注释@MapperScan，如：

```java
@SpringBootApplication(scanBasePackages = "com.project")
@MapperScan("com.project.mapper")
public class SpringApplications {    
    public static void main(String[] args){
        SpringApplication.run(SpringApplications.class, args);
    }
}
```



## 十八. SpringBoot整合mybatis，利用注解方式配置:

### 1.在pom添加dependency:

```xml
<dependency>    
    <groupId>org.mybatis.spring.boot</groupId>    
    <artifactId>mybatis-spring-boot-starter</artifactId
    <version>1.3.1</version>
</dependency>
```

### 2.新建application.properties全局文件：

```properties
# 链接数据库的配置
spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password = Qwer1234
spring.datasource.url = jdbc:mysql://localhost:3306/testdb
```

### 3.将逆向工程的db.java添加到项目的com.project.pojo包中:

### 4.新建com.project.mapper包，添加dbmapper.java文件：

```java
package com.project.mapper;
import com.project.pojo.Users;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
public interface UsersMapper {    
    @Select("select * from testuser where name=#{name}")    
    Users findUsersByName(@Param("name")String name);    
    @Insert("insert into testuser(id,name,password) values(#{id},#{name},#{password})")    
    void addUser(@Param("id")Integer id,@Param("name")String name, @Param("password") String password);
}
```

### 5.新建com.project.servcie类,创建dbService.java接口，如：

```java
package com.project.service;
import com.project.mapper.UsersMapper;
import com.project.pojo.Users;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UsersServiceImpl implements UsersService{

    @Autowired
    private UsersMapper usersMapper;

    @Override
    public Users findUser(String name) {
        return usersMapper.findUsersByName(name);
    }

    @Override
    public void saveUser(Users user) {
        usersMapper.addUser(user.getId(),user.getName(),user.getPassword());
    }
}

```

### 6.创建dbServiceImpl.java实现类：

```java
package com.project.service;
import com.project.mapper.UsersMapper;
import com.project.pojo.Users;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
@Servicepublic class UsersServiceImpl implements UsersService{    
    @Autowired    
    private UsersMapper usersMapper;    
    @Override    
    public Users findUser(String name) {        
        return usersMapper.findUsersByName(name);    
    }    
    @Override    
    public void saveUser(Users user) {
        usersMapper.addUser(user.getId(),user.getName(),user.getPassword());    }
}
```

### 7.创建控制类，如dbController.java：

```java
package com.project.controller;
import com.project.pojo.Users;
import com.project.service.UsersService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;
@RestController
@Controller
public class UsersController {    
    @Autowired    
    private UsersService usersService;    
    @RequestMapping("/findUser")    
    @ResponseBody    
    public Users findUser(String name){        
        return usersService.findUser(name);    
    }    
    @RequestMapping("/addUser")    
    @ResponseBody    
    public String addUser(){        
        Users user = new Users();        
        user.setId(5);        
        user.setName("Q");        
        user.setPassword("12");        
        usersService.saveUser(user);        
        return "OK";    
    }
}
```

### 9.添加启动类注释@MapperScan，如：

```java
@SpringBootApplication(scanBasePackages = "com.project")
@MapperScan("com.project.mapper")
public class SpringApplications {    
    public static void main(String[] args){
        SpringApplication.run(SpringApplications.class, args);
    }
}
```



## 十九. Springboot 利用包名区分数据源：

### 1.配置application.properties全局文件,添加数据库属性，如：

```properties
# 链接testdb数据库的配置
spring.datasource.testdb.driver-class-name = com.mysql.cj.jdbc.Driver
spring.datasource.testdb.username = root
spring.datasource.testdb.password = Qwer1234
spring.datasource.testdb.url = jdbc:mysql://localhost:3306/testdb
# 链接testdb1数据库的配置
spring.datasource.testdb1.driver-class-name = com.mysql.cj.jdbc.Driver
spring.datasource.testdb1.username = root
spring.datasource.testdb1.password = Qwer1234
spring.datasource.testdb1.url = jdbc:mysql://localhost:3306/testdb
```

### 2.新建com.project.datasource包，添加db1.java,db2.java数据源类，若选db1为首选数据源，则添加@primary注释，如：

```java
//注册到Spring容器中
@Configuration
@MapperScan(basePackages="com.project.testdb.mapper",sqlSessionFactoryRef = "testdbSqlSessionFactory")
public class DataSource1 {    
    /*    
    配置db1数据库    
    @return    
    通过configurationproperties注释指定db对象     
    */    
    @Bean(name="testdbDatasource")
    @ConfigurationProperties(prefix="spring.datasource.testdb")    
    @Primary    
    public DataSource testDatasource(){        
        return DataSourceBuilder.create().build();    
    }    
    /*    
    创建SqlSessionFactory    
    @param dataSource    
    @return    
    @throws Exception     
    */    
    @Bean(name="testdbSqlSessionFactory")    
    @Primary
    public SqlSessionFactory testSqlSessionFactory
        (@Qualifier("testdbDatasource") DataSource dataSource) throws Exception{
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        return bean.getObject();   
    }    
    /*    
    配置事务管理    
    @param dataSource    
    @return     
    */   
    @Bean(name="testdbTransactionManager")
    @Primary   
    public DataSourceTransactionManager testTransactionManager(        @Qualifier("testdbDatasource")DataSource dataSource){
        return new DataSourceTransactionManager(dataSource);    
    }    
    /*    
    封装数据库对象  
    */    
    @Bean(name="testdbSqlSessionTemplate")
    @Primary
    public SqlSessionTemplate testSqlSessionTemplate(@Qualifier("testdbSqlSessionFactory") SqlSessionFactory sqlSessionFactory){
        return new SqlSessionTemplate(sqlSessionFactory);
    }
}
```

### 3.将数据库逆向工程的db.java类添加到com.project.pojo包中

### 4.新建对应数据库的com.project.#{dbname}.mapper包，新建对应的dbMapper接口，添加@Qualifier注解，如：

```java
//使用注解方式,传递数据源datasource
@Qualifier("testdbSqlSessionFactory")
public interface UsersMapper {    
    @Insert("insert into testuser(id,name,password) values(#{id},#{name},#{password})")    
    void addUser(@Param("id") Integer id,@Param("name") String name, @Param("password") String password);
}
```

### 5.分别新建对应的com.project.#{dbname}.service包、dbService接口和service类，如：

```java
public interface UsersService {    
    void saveUser(Users user);
}
```

```java
@Service
public class UsersServiceImpl implements UsersService {    
    @Autowired    
    private UsersMapper usersMapper;
    @Override  
    public void saveUser(Users user){     
        usersMapper.addUser(user.getId(),user.getName(),user.getPassword());   
    }
}
```

### 6.新建com.project.controller包，新建controller类：

```java
@Controller
public class IndexController {    
    //注入service类
    @Autowired 
    private UsersService usersService;
    @Autowired
    private UserService userService;
    
    @RequestMapping("/addUser") 
    @ResponseBody  
    public String addUser(){
        Users u = new Users();
        u.setId(8); 
        u.setName("li");
        u.setPassword("121");
        usersService.saveUser(u);
        return "ok";
    }
}
```

### 7.启动类无需另添加@MapperScan注释，因datasource包已添加@MapperScan注释





















































