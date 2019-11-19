# SpringBoot-Case

Several cases about SpringBoot

## 一、基础配置：

## 1.SpringBoot父工程pom配置:

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



## 2. SpringBoot 创建父工程和子工程，并对子工程的dependency进行管理

### 2.1父级工程Pom的package为pom

```xml
<!-- 父级依赖 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.6.RELEASE</version>
</parent>
<packaging>pom</packaging>
```

### 2.2父级工程Pom中,添加dependency Management, depenedencies和dependency等.如:

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

### 2.3创建new module,并在该Pom中添加dependencies,如:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```



## 3.SpringBoot 自定义属性的类型安全配置方法（数据属性将存储在全局配置文件中）ConfigurationProperties：

### 3.1在application.properties文件中自定义属性，如：

```properties
book.author = aa;
book.name = bb;
```

### 3.2控制类java中使用注释：

```java
@ConfigurationProperties(prefix="book")
```

### 3.3控制类使用Generate: getter and setter



## 二.测试和开发环境配置：

## 1.SpringBoot 如何配置profile，实现不同开发环境配置:

### 1.1创建application-prod.properties, application-sit.properties, application-dev.properties文件，可添加不同运行环境的端口，激活状态等

### 1.2通过在application.properties中设置spring.profiles.active = dev/sit/prod来指定活动的profile



## 2.SpringBoot如何进行整合测试:

### 2.1在测试的子module里添加：

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

### 2.2创建controller包，添加Controller.java：

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

### 2.3在子工程的/src/test/java 添加package,添加测试类的java,如：

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



## 3.SpringBoot设置开发模式：

### 3.1在POM文件中添加两个dependency:

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



## 4.多环境日志输出，根据不同环境定义不同的日志输出：

### 4.1在logback-spring.xml中使用springProfile节点来定义,如：

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

### 4.2在application.properties中：

```properties
spring.profiles.active = {envname}

```



## 三.网页进阶配置:

## 1.SpringBoot如何分离启动类和控制类：

### 1.1在src/main/java下新建package,新建启动类applications.java，启动类中添加注释：

```java
@EnableAutoConfiguration+@ComponentScan(“com.project.controller”)
```

或

```java
@SpringBootApplication(scanBasePackage = {"com.project"})
```

### 1.2在applications.java中添加：

```java
public class SpringApplications {
    public static void main(String[] args){
       SpringApplication.run(SpringApplications.class, args);
    }
}
```

### 1.3在src/main/java下新建package，新建控制类controller.java,控制类添加注释:

```java
@Controller
```



## 2. SpringBoot整合Thymeleaf:

### 2.1在module中添加dependency:

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

### 2.2在application.properties中添加thymeleaf配置：

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

### 2.3在控制类添加模型，如：

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

### 2.4在resources文件下创建templates文件夹，新建index.html文件



## 3.SpringBoot向server传递参数，支持Rest风格,

### 方法一：在控制类文件的url添加@PathVariable注释，如：

```java
@RequestMapping("/findUsers/{page}/{rows}")
public List<Users> findUsers(@PathVariable int page, @PathVariable int rows){
    return usersService.findUsers(page, rows);
}

```

### 方法二：亦可采用@RequestBody方式向server传递参数，如:

```java
@RequestMapping("/save")
public @ResponseBody Map<String, Object> save(@RequestBody User user) {    
    Map<String, Object> result = new HashMap<>();   
    if (StringUtils.isEmpty(user.id))    
        user.id = userDao.addUser(user);    
    else {        
        userDao.updateUser(user);   
    }    
    result.put("id", user.id);    
    return result;
}

```

```java
public interface UserDao {    
    List<User> findAll();    
    String addUser(User user);
    void updateUser(User user); 
}

```

```java
public class UserImpl implements UserDao {

    @Autowired
    private JdbcTemplate jdbcTemplate;
    
    //添加用户
    @Override
    public String addUser(User user){
        String sql = "insert into testuser(id,name,password,birthday,email) values(?, ?, ?, ?, ?)";
        int i = jdbcTemplate.update(sql,   user.getId(),user.getName(),user.getBirthday(),user.getEmail());
        if(i==0){
            return "fail";
        }else{
            return "user";
        }
    }

    //修改用户
    @Override
    public void updateUser(User user){
        String sql = "update testuser set name=?, password=? where id=?";
        int check = jdbcTemplate.update(sql);
    }
}

```

### 方法三：使用mybatis在映射类文件添加注释@Insert,@Select,@Update,@Delete

### (".."),缺点是sql发生变化时需重新编译（不推荐），例：

```java
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.beans.factory.annotation.Qualifier;
@Qualifier("testdbSqlSessionFactory")
public interface UsersMapper {    
    @Insert("insert into testuser(id,name,password) values(#{id},#{name},#{password})")    
    void addUser(@Param("id") String id, @Param("name") String name, @Param("password") String password);    
    @Select("select * from testuser where name=#{name}")
    Users findByName(@Param("name")String name);
}

```

```java
@Service
public class UsersServiceImpl implements UsersService {

    @Autowired
    private UsersMapper usersMapper;

    @Override
    //springframework内嵌事务管理
    @Transactional
    public void saveUser(Users user){
        usersMapper.addUser(user.getId(),user.getName(),user.getPassword());
    }

    @Override
    public Users findByName(String name){
        System.out.println("从数据库中查询");
        return usersMapper.findByName(name);
    }
}

```

### 替代方案，使用mybatis在xml文件中配置(Intellij版)：

#### pom文件添加如下配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.6.RELEASE</version>
    </parent>
    
    <groupId>com.project</groupId>
    <artifactId>TestMybatisXml</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- mybatis -->
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

        <!-- mybatis spring整合 -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.1</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
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
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
            <version>1.5.6.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.11</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

        <!-- 分页插件 -->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.1.2</version>
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
            <!--mybatis 逆向工程插件-->
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.5</version>
                <configuration>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
            </plugin>
        </plugins>

        <resources>
            <!-- 表示把java目录下的有关xml文件，properties文件编译/打包时放在resources目录下 -->
            <resource>
                <directory>src/main/java</directory><!--所在的目录-->
                <includes><!--包括目录下的.properties,.xml文件都会扫描到-->
                    <include>**/*.properties</include>
                    <include>**/*.yml</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>

</project>
```

#### 全局文件resources/application.yml配置database，(当配置application.properties时，设置spring.datasource.url)

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/oasys?serverTimezone=GMT%2B8&useSSL=false&useUnicode=true&characterEncoding=UTF8

```

```yml
spring:
  datasource:
    name: test
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: Qwer1234
    url: jdbc:mysql://localhost:3306/testdb?useSSL=false&useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
    type: com.alibaba.druid.pool.DruidDataSource
    filters: stat
    maxActive: 20
    maxWait: 60000
    poolPreparedStatement: true

mybatis:
  mapper-location: classpath:mapping/TestuserMapper.xml
  type-aliases-package: com.project.pojo
  config-location: classpath:mybatis/mybatis-config.xml

pagehelper:
  helperDialect: mysql
  reasonable: true
  supportMethodsArgument: true
  params.count: countSql

```

#### 将mybatis-config.xml文件添加至resources/mybatis文件夹中：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE configuration
                PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<!-- 配置mybatis的缓存，延迟加载等等一系列属性 -->
<settings>
    <!-- 该配置影响的所有映射器中配置的缓存的全局开关。默认true -->
    <setting name="cacheEnabled" value="true" />
    <!-- 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置fetchType属性来覆盖该项的开关状态。默认false -->
    <setting name="lazyLoadingEnabled" value="true" />
    <!-- 是否允许单一语句返回多结果集（需要兼容驱动）。 默认true -->
    <setting name="multipleResultSetsEnabled" value="true" />
    <!-- 使用列标签代替列名。不同的驱动在这方面会有不同的表现， 具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。默认true -->
    <setting name="useColumnLabel" value="true" />
    <!-- 允许 JDBC 支持自动生成主键，需要驱动兼容。 如果设置为 true 则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如 Derby）。
      默认false -->
    <setting name="useGeneratedKeys" value="false" />
    <!-- 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。
      FULL 会自动映射任意复杂的结果集（无论是否嵌套）。
      默认 PARTIAL -->
    <setting name="autoMappingBehavior" value="PARTIAL" />

    <setting name="autoMappingUnknownColumnBehavior" value="WARNING" />
    <!-- 配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。默认SIMPLE -->
    <setting name="defaultExecutorType" value="SIMPLE" />
    <!-- 设置超时时间，它决定驱动等待数据库响应的秒数。Not Set (null) -->
    <setting name="defaultStatementTimeout" value="25" />
    <!-- 为驱动的结果集获取数量（fetchSize）设置一个提示值。此参数只可以在查询设置中被覆盖。 -->
    <setting name="defaultFetchSize" value="100" />
    <!-- 允许在嵌套语句中使用分页（RowBounds）。 If allow, set the false. -->
    <setting name="safeRowBoundsEnabled" value="false" />
    <!-- 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。 -->
    <setting name="mapUnderscoreToCamelCase" value="false" />
    <!-- MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular references）和加速重复嵌套查询。
         默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。
         若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlSession 的不同调用将不会共享数据。
     -->
    <setting name="localCacheScope" value="SESSION" />
    <!-- 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。 某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。 -->
    <setting name="jdbcTypeForNull" value="OTHER" />
    <!-- 指定哪个对象的方法触发一次延迟加载。 -->
    <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString" />
</settings>

</configuration>

```

#### 添加resources/generatorConfig.xml文件:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <classPathEntry location="C:\Users\Guo\.m2\repository\mysql\mysql-connector-java\8.0.11\mysql-connector-java-8.0.11.jar"/>
    <context id="testTables" targetRuntime="MyBatis3" >
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/testdb?serverTimeZone=GMT%2B8&amp;useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=false&amp;user=root&amp;password=Qwer1234"
                        userId="root"
                        password="Qwer1234">
        </jdbcConnection>
        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
            NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>
        <!-- targetProject:生成PO类的位置 -->
        <javaModelGenerator targetPackage="com.project.pojo"
                            targetProject="src\main\java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
        <sqlMapGenerator targetPackage="com.project.mapper"
                         targetProject="src\main\java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>
        <!-- targetPackage：mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.project.mapper"
                             targetProject="src\main\java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>

        <!-- 指定数据库表 -->
        <table schema="testdb" tableName="testuser"></table>

        <!-- 有些表的字段需要指定java类型
         <table schema="" tableName="">
            <columnOverride column="" javaType="" />
        </table> -->

    </context>
</generatorConfiguration>

```

#### 使用mybatis-generator plugin生成mapper，mapper.xml, pojo类文件db和dbExample，如需添加查找所有功能，添加如下至dbMapper.xml：

```xml
<select id="getList" resultMap="BaseResultMap" resultType="com.project.pojo.Testuser">  
    select  
    <include refid="Base_Column_List" />
    from testuser
</select>

```

#### 添加如下至dbMapper接口文件：

```java
List<Testuser> getList();

```



## 4. SpringBoot打包发布到TomCat：

### 4.1需要打包war包,pom文件设置packaging为war

### 4.2添加dependency，scope设置为compile：

```xml
<dependency>    
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring-boot-starter-tomcat</artifactId>    
    <scope>compile</scope>
</dependency>

```

### 4.3启动类继承SpringBootServletInitializer,重写configure方法：

```java
@SpringBootApplication(scanBasePackages = "com.project")
public class SpringApplications extends SpringBootServletInitializer {    public static void main(String[] args)    {        
    SpringApplication.run(SpringApplications.class, args);    
}
 //重写configure方法
 @Override
 protected SpringApplicationBuilder configure(SpringApplicationBuilder builder){ 
     return builder.sources(SpringApplications.class); 
 }
}

```



## 5.SpringBoot整合WebSocket:

### 5.1pom文件添加dependency依赖：

```xml
<dependency>   
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
<dependency> 
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

### 5.2新建配置包com.project.config,添加websocket配置类文件，如：

```java
package com.project.config;
import org.springframework.context.annotation.Configuration;
import org.springframework.messaging.simp.config.MessageBrokerRegistry;
import org.springframework.web.socket.config.annotation.AbstractWebSocketMessageBrokerConfigurer;
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
import org.springframework.web.socket.config.annotation.StompEndpointRegistry;
//WebSocket配置类
@Configuration
//开启对Websocket的支持
@EnableWebSocketMessageBroker
public class WebSocketConfig extends AbstractWebSocketMessageBrokerConfigurer {    //注册一个STOMP协议的节点，并映射到指定的URL    
    @Override    
    public void registerStompEndpoints(StompEndpointRegistry registry)
    {        
        //注册一个STOMP的endpoint,并指定使用SockJS协议        
        registry.addEndpoint("/endpointSocket").withSockJS();    
    }    
    
    //配置消息代理    
    @Override    
    public void configureMessageBroker(MessageBrokerRegistry registry){        
        //配置一个广播式的消息代理        
        registry.enableSimpleBroker("/topic");    
    }
}
```

### 5.3新建映射包com.project.pojo,创建客户端发送给服务端的数据pojo类文件、服务端发送给客户端的数据pojo类文件，如:

```java
//客户端发送给服务端的消息实体类
public class SocketMessage {    
    private String message;    
    public String getMessage(){       
        return message;    
    }    
    public void setMessage(String message){        
        this.message = message;    
    }
}

```

```java
//服务器向客户端响应数据的消息实体类
public class SocketResponse {
    private String responseMessage;    
    public SocketResponse(String responseMessage){   
        this.responseMessage = responseMessage;  
    }    
    public String getResponseMessage(){  
        return responseMessage;
    }
}

```

### 5.4新建控制包com.project.controller,创建控制类文件，如：

```java
@Controller
public class WebSocketController {    
    private SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");    //当客户端向服务端发送STOMP请求时，通过@MessageMapping注解来映射/getServerTime    
    @MessageMapping(value="/getServerTime")    
    //当服务器有消息时，会对订阅了@SendTo中的路径的客户端发送消息    
    @SendTo(value="/topic/getResponse")    
    public SocketResponse serverTime(SocketMessage message) throws InterruptedException{        
        return new SocketResponse(message.getMessage() + sf.format(new Date()));    
    }    
    @RequestMapping("/index")    
    public String toPage(){       
        return "webSocket";    
    }
}

```

### 5.5新建resources/templates文件夹，添加template文件(存放页面)，如：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <title>集成WebSocket示例</title>
</head>
<body>
<div>
    <button id="connect" onclick="connect();">连接</button>
    <button id="disconnect" onclick="disconnect();">断开连接</button>
    <button id="serverTimeId" onclick="getServerTime();">获取服务器端时间</button>
    <hr/>
    <span id="showServerTime"></span>
</div>
<script type="text/javascript" src="sockjs.min.js"></script>
<script type="text/javascript" src="stomp.min.js"></script>
<script type="text/javascript" src="jquery-3.1.1.min.js"></script>
<script type="text/javascript" src="socket.js"></script>
<script type="text/javascript">
    var stompClient = null;
    $(function(){
        setConnect(false);
    });
    function setConnect(connected){
    $("connect").attr({disabled:connected});
    $("disconnect").attr({disabled:!connected});
    }
    function connect(){
        var socket = new SocketJS('/endpointSocket');
        //创建STOMP客户端连接，目标地址为/endpointSocket的STOMP代理
        stompClient = Stomp.over(socket);
        //打印stomp输出信息
        stompClient.debug = function(str){
            console.log(str+"\n");
        };
        //建立连接
        stompClient.connect((),function(frame){
            setConnect(true);
            //连接成功后订阅/topic/getResponse目标发送的信息，该地址在Controller中用@SendTo指定
            stompClient.subscribe(JSON.parse(response.body).responseMessage);
            });
        });
    }

    function disconnect(){
        if(stompClient != null){
             stompClient.disconnect();
        }
        setConnect(false);
    }
    function getServerTime(){
        var message = "The server time is:";
        //发送消息到服务器端，/getServerTime地址是由Controller中的@MessageMapping指定
        stompClient.send("/getServerTime",{},Json.stringify(['message':message]));
    }

    function showResponse(message){
        var response = $("#showServerTime");
        response.html(message);
    }

</script>
</body>
</html>

```

### 5.6新建resources/static文件夹，存放sockjs.min, stomp, socket, jquery的js文件(未安装jquery,使用:npm install -g grunt-cli    :grunt && grunt dist:/pathname/命令)



## 6.SpringBoot整合Angular(前端):

### 6.1pom文件添加xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>   
    <groupId>org.springframework.boot</groupId>   
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>   
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>    
    <groupId>net.sourceforge.nekohtml</groupId>    
    <artifactId>nekohtml</artifactId>  
    <version>1.9.22</version>
</dependency>
```

### 6.2配置全局文件application.properties，如：

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



## 四.数据进阶配置：

## 1.SpringBoot使用FastJson解析json数据：

### 方法一：

#### 1.1让启动类applications.java继承WebMvcConfigureAdapter

#### 1.2重写 configureMessageConverters方法：

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

#### 1.3设置application.properties,添加：

```properties
spring.http.encoding.force = true

```

### 方法二：

#### 2.1在启动类application.java中添加注释：

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



## 3. SpringBoot整合JdbcTemplate(数据库模板):

### 3.1在pom添加dependency:

```xml
<dependency>
 <groupId>com.springframework.boot</groupId>
 <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

```

### 3.2在pom添加数据库的配置:

```xml
spring.datasource.driver-class-name = com.mysql.jdbc.Driver
spring.datasource.username = root
spring.datasource.password = root
spring.datasource.url = jdbc:mysql://localhost:3306/db1

```

### 3.3新建com.project.pojo包，新建数据库表类，如Users.java:

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

### 3.4新建com.project.dao包，新建dao类，添加@Repository,@Autowired注释，如：

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

### 3.5新建com.project.service包，新建service类,添加@Service，@Autowired注释，如：

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

### 3.6新建com.project.controller包，新建控制类，添加@Controller,@Autowired注释，如：

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



## 4. mybatis逆向工程生成Mysql数据表的Mapper和Pojo:

### 4.1添加mybatis.generator, mybatis, mybatis.spring.boot依赖:

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

### 4.2与src同级目录生成generatorConfig.xml:

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

### 4.3新建java生成mapper文件：

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

### 4.4生成的mapper.java文件中，添加行：

```java
Users selectByPrimaryKey(Integer id);

```



## 5. SpringBoot整合mybatis，利用xml文件配置:

### 5.1在pom文件中，添加dependency:

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

### 5.2在resources目录新建mybatis文件夹，新建mybatis-config.xml文件,添加：

```xml
<?xml version="1.0" encoding="UTF-8" ？>
<!DOCTYPE configuration
      "-//mybatis.org//DTD Config 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

</configuration>

```

### 5.3在resources目录添加application.yml,添加数据源属性配置:

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

### 5.4在resources目录添加application.properties全局配置文件：

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

### 5.5新建com.project.pojo和com.project.mapper包:

将逆向工程的mapper包里的dbmapper.java文件放入项目com.project.mapper文件夹里

将逆向工程的dbmapper.xml文件放入项目resources的mapping文件夹下

将逆向工程的pojo包里的db.java和dbExample.java分别放入项目com.project.pojo文件夹里

### 5.6创建接口，如在com.project.service下：

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

### 5.7创建实现类，如在com.project.service下:

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

### 5.8添加控制类，如在项目文件com.project.controller里,创建DBController.java文件：

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

### 5.9添加启动类注释@MapperScan，如：

```java
@SpringBootApplication(scanBasePackages = "com.project")
@MapperScan("com.project.mapper")
public class SpringApplications {    
    public static void main(String[] args){
        SpringApplication.run(SpringApplications.class, args);
    }
}
```



## 6. SpringBoot整合mybatis，利用注解方式配置:

### 6.1在pom添加dependency:

```xml
<dependency>    
    <groupId>org.mybatis.spring.boot</groupId>    
    <artifactId>mybatis-spring-boot-starter</artifactId
    <version>1.3.1</version>
</dependency>
```

### 6.2新建application.properties全局文件：

```properties
# 链接数据库的配置
spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password = Qwer1234
spring.datasource.url = jdbc:mysql://localhost:3306/testdb
```

### 6.3将逆向工程的db.java添加到项目的com.project.pojo包中:

### 6.4新建com.project.mapper包，添加dbmapper.java文件：

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

### 6.5新建com.project.servcie类,创建dbService.java接口和实体类，如：

```java
public interface UsersService {    
    void saveUser(Users users);
    Users findUser(String name);
}
```

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

### 6.6创建控制类，如dbController.java：

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

### 6.7添加启动类注释@MapperScan，如：

```java
@SpringBootApplication(scanBasePackages = "com.project")
@MapperScan("com.project.mapper")
public class SpringApplications {    
    public static void main(String[] args){
        SpringApplication.run(SpringApplications.class, args);
    }
}
```



## 7. Springboot 利用包名区分数据源：

### 7.1配置application.properties全局文件,添加数据库属性，如：

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

### 7.2新建com.project.datasource包，添加db1.java,db2.java数据源类，新建数据源配置类（若选db1为首选数据源，则添加@primary注释）：

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

### 7.3将数据库逆向工程的db.java类添加到com.project.pojo包中

### 7.4新建对应数据库的com.project.#{dbname}.mapper包，新建对应的dbMapper接口，添加@Qualifier注解，如：

```java
//使用注解方式,传递数据源datasource
@Qualifier("testdbSqlSessionFactory")
public interface UsersMapper {    
    @Insert("insert into testuser(id,name,password) values(#{id},#{name},#{password})")    
    void addUser(@Param("id") Integer id,@Param("name") String name, @Param("password") String password);
}
```

### 7.5分别新建对应的com.project.#{dbname}.service包、dbService接口和service类，如：

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

### 7.6新建com.project.controller包，新建controller类：

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

### 7.7启动类无需另添加@MapperScan注释，因datasource包已添加@MapperScan注释



## 8. SpringBoot 事务管理：

8.1当使用spring-boot-starter-jdbc或spring-boot-starter-data-jpa依赖时，SpringBoot会自动默认注入DataSourceTransactionManager或JpaTransactionManager

8.2在DBServiceImpl实现类的方法上添加@Transactional注释，如：

```java
@Service
public class UsersServiceImpl implements UsersService {    
    @Autowired    
    private UsersMapper usersMapper;    
    @Override    
    @Transactional    
    public void saveUser(Users user){      
        usersMapper.addUser(user.getId(),user.getName(),user.getPassword()); 
        int i = 6/0;
    }
}
```



## 9.SpringBoot整合Neo4j（NOSQL,node）：

### 9.1pom文件添加依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-neo4j</artifactId>
</dependency>
```

### 9.2在全局文件application.properties中添加配置:

```properties
spring.data.neo4j.url = http://localhost:7474
spring.data.neo4j.username = neo4j
spring.data.neo4j.password = neo4j
```

### 9.3新建com.project.pojo包，在包中新建节点实体类和关系实体类，注解NodeEntity和RelationshipEntity将node实体类和relation实体类存储在Neo4j数据库中，如：

```java
//表示节点类型
@NodeEntity(label="User")
public class UserNode {    
    @GraphId    
    private Long nodeId;
    @Property  
    private String userId;  
    @Property
    private String name; 
    @Property 
    private int age;
}
```

```java
//表示节点关系
@RelationshipEntity(type="UserRelation")
public class UserRelation {    
    @GraphId  
    private Long id;
    //注解表示开始和结束节点   
    @StartNode
    private UserNode startNode;
    @EndNode 
    private UserNode enNode;
}
```

### 9.4在节点实体类和关系实体类生成get和set方法

### 9.5新建com.project.dao或mapper包，添加node和relation接口，如：

```java
@Component
public interface UserRepository extends GraphRepository<UserNode> {    
    
    @Query("MATCH (N:User) RETURN n")   
    List<UserNode> getUserNodeList();    
    
    @Query("create (n:User{age:{age},name:{name}}) RETURN n")
    List<UserNode> addUserNodeList(@Param("age") int age, @Param("name") String name);
    
}
```

```java
@Component
public interface UserRelationRepository extends GraphRepository<UserRelation> 
{    
    @Query("match p=(n:User)<-[r:UserRelation]->(n1:User) where n.userId={firstUserId} and n1.userId={secondUserId} return p")    
    List<UserRelation>    findUserRelationByEachId(@Param("firstUserId") String firstUserId, @Param("secondUserId") String secondUserId);    
    
    @Query("match (fu:User),(su:User) where fu.userId={firstUserId} and su.userId={secondUserId} create p=(fu)-[r:UserRelation]->(su) return p")    
    List<UserRelation> addUserRelation(@Param("firstUserId") String firstUserId, @Param("secondUserId") String secondUserId);
}
```

### 9.6新建com.project.service包，添加service类, 将dao层注入，如：

```java
@Service
public class UserService {    
    
    @Autowired   
    private UserRepository userRepository;    
    
    public void addUserNode(UserNode userNode){   
        userRepository.addUserNodeList(userNode.getAge(),userNode.getName());   
    }
}
```

### 9.7新建com.project.controller包，添加控制类，将service类注入，如：

```java
@Controller
public class Neo4jController {
    
    @Autowired 
    private UserService userService;
    @RequestMapping("/saveUser")
    @ResponseBody  
    public String saveUserNode(){ 
        UserNode node = new UserNode();
        node.setUserId("111");
        node.setName("aaa");
        node.setAge(11);
        return "success";   
    }
}
```

### 9.8在启动类添加注释@EnableNeo4jRepositories和@EntityScan，如：

```java
@SpringBootApplication(scanBasePackages = "com.project")
@EnableNeo4jRepositories(basePackages = "com.project.dao")
@EntityScan(basePackages = "com.project.pojo")
public class TestNeo4jStarter {    
    public static void main(String[] args)    {        
        SpringApplication.run(SpringApplications.class, args);    
    }
}
```

#### 附：Neo4j 操作语句：

（ ）里面是节点，[ ]里面是关系，{ }里面的是属性，>表示关系的方向，如：

创建：create (A:Person{name:'Jack'}) - [:Friend] -> (B:Person {name:'tom'}) - [:Friend] -> (C:Person {name:'Lucy'}), (A)-[:Know]->(C)

查询：match (a)-[:Friend]->(b)

​           where b.name = 'tom'

​           return b



## 10. SpringBoot整合Redis(单机版)(CacheDB)

### 10.1添加dependency依赖

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>     
    <version>8.0.11</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 10.2配置application.properties:

```properties
spring.redis.database=0

spring.redis.host=localhost

spring.redis.port=6379

spring.redis.pool.max-active=8

spring.redis.pool.max-wait=1

spring.redis.pool.max-idle=8

spring.redis.pool.min-idle=0

spring.redis.timeout=0
```

### 10.3启动类添加注释@EnableCaching，开启缓存:

### 10.4在需要存储key-value的service实现类中，添加@Cacheable注释，其中注释的value值为key值，如：

```java
@Service
public class UsersServiceImpl implements UsersService {    
    @Autowired 
    private UsersMapper usersMapper;
    
    //store key=myusername, value= return value in redis  
    @Override 
    @Cacheable(value="myusername") 
    public Users findUser(String name) { 
        System.out.println("从数据库中查询");  
        return usersMapper.findUsersByName(name);
    }        
    
    @Override 
    public void saveUser(Users user) {
        usersMapper.addUser(user.getId(),user.getName(),user.getPassword());        }
}
```

### 10.5在pojo包中的数据库对象类继承Serializable,如：

```java
public class Users implements Serializable{
    ...
}
```



## 11. SpringBoot整合Mongodb(NOSQL,json):

### 11.1pom添加dependency依赖:

```xml
<dependency>    
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

### 11.2配置application.properties全局文件:

```properties
spring.data.mongodb.host=localhost
spring.data.mongodb.port=27017
//配置mogodb存储的db,collection由pojo文件配置
spring.data.mongodb.database=mydb
```

### 11.3dbController控制类注入MongoTemplate,直接利用mongoTemplate操作数据，如：

```java
public class UsersController {    
    //从mongodb取出数据
    @Autowired
    private MongoTemplate mongoTemplate;
    
 @RequestMapping("/addUser")
    @ResponseBody
    public String addUser(){
        Users user = new Users();
        user.setId(20);
        user.setName("Q1");
        user.setPassword("12");
        mongoTemplate.save(user);
        return "OK";
    }

    @RequestMapping("/list")
    @ResponseBody
    public List<Users> findUsers(){
        return mongoTemplate.findAll(Users.class);
    }
}
```



## 12.SpringBoot整合ElasticSearch(Search Server, big data analysis):

### 12.1解压缩安装文件，修改elasticsearch.yml配置:

```yml
network.host: IPv4 address

discovery.seed_hosts: ["localhost", "host2"]
```

### 12.2windows运行: bin/elasticsearch.bat

### 12.3ElasticSearch存储数据，如：

```bash
curl -XPUT 'IPv4:9200/userindex/user/3?pretty' -d'
```

(注：ElasticSearch以index/type/doc的PUT形式存储数据)

### 12.4ElasticSearch检索文档，如：

```bash
curl -XGET "IPv4:9200/index/type/doc/id"
```

搜索所有type和指定id值的type(_search?q=)：

```bash
curl -XGET "IPv4:9200/index/type/_search"

curl -XGET "IPv4:9200/index/type/_search?q=id:value"
```

### 12.5pom添加dependency依赖

```xml
<dependency>    
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId></dependency>
```



## 五.其他进阶配置：

## 1.SpringBoot 访问静态资源

## 1.1Spring Boot默认静态文件路径：

从classpath的 static, public或 /META-INF/RESOURCES文件夹或从ServletContext根目录提供静态内容：默认 js在resources/public目录中；image在 resources/static目录中

## 1.2自定义静态文件路径，js,css,image等：

spring.resources.static-location = classpath:/staitc/



## 2.SpringBoot自定义拦截器：

### 2.1建立Interceptor.java类，继承 WebMvcConfigurerAdapter

### 2.2重写HandlerInterceptor方法

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



## 3.SpringBoot 实现异步调用：

### 3.1创建asyncservice类package,新建AsyncService 接口，如:

```java
public interface AsyncService {

 Future<String> doTask1()throws Exception;
 Future<String> doTask2()throws Exception;
 Future<String> doTask3()throws Exception;
}
```

### 3.2创建实现类，实现service类接口,添加@Service、@Async注释，如：

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

### 3.3在控制类注入asyncservice接口，添加@Autowired注释，如：

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

### 3.4在启动类添加@EnableAsync注释，如：

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



## 4. SpringBoot整合QuartZ(任务工具):

### 4.1新建job类，添加@Component注解，在run()函数前添加@Scheduled注解，如：

```java
@Component
public class Myjob {
    
    @Scheduled(fixedRate = 1000)
    public void run(){
        ...
    }
}
```

### 4.2在启动类中添加@EnableScheduling注解



## 5. SpringBoot实现文件上传：

### 5.1在pom文件配置dependency:

```xml
<dependency>    
    <groupId>org.springframework.boot</groupId>    
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

### 5.2在全局文件application.properties中，配置multipart,如:

```properties
multipart.maxFileSize = 500Mb
multipart.maxRequestSize = 500Mb
```

### 5.3在resources文件夹下创建templates文件，thymeleaf默认在该文件夹查找template page，如创建upload.html:

```html
<form method="POST" enctype="multipart/form-data" action="/upload">    
    <p>        
        文件<input type="file" name="file"/>    
    </p>    
    <p>        
        <input type="submit" value="上传"/>
    </p>
</form>
```

### 5.4创建上传控制类，如UploadController.java:

```java
@Controller
public class UploadController {    
    @RequestMapping("/index")    
    public String toUpload(){        
        //return template page upload        
        return "upload";    
    }    
    //value请求路径   
    @RequestMapping(value = "/upload", method = RequestMethod.POST)    
    @ResponseBody
    public String uploadFile(MultipartFile file, HttpServletRequest request){        try{     
        //文件上传的存放路径
        String dir = request.getServletContext().getRealPath("/upload");    
        File fileDir = new File(dir); 
        if(!fileDir.exists())
            fileDir.mkdirs();
        //生成文件在服务器存放的名字     
        String fileSuffix = file.getOriginalFilename().substring(file.getOriginalFilename().lastIndexOf("."));            
        //UUID生成存放文件名   
        String filename = UUID.randomUUID().toString()+fileSuffix;          
        File files = new File(fileDir+"/"+filename);
        //upload
        file.transferTo(files);
    }catch(Exception e){ 
        e.printStackTrace();  
        return "upload fail";
    }                                                                                    return "upload success"; }
}
```

### 5.5该上传路径中，request.getServletContext().getRealPath默认在父工程的src/main/webapp目录
