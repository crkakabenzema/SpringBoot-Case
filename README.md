# SpringBoot-Case
Several cases about SpringBoot

一.SpringBoot父工程pom配置:

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



二. SpringBoot 创建父工程和子工程，并对子工程的dependency进行管理
1. 父级工程Pom的<package>pom</package>
<!-- 父级依赖 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.6.RELEASE</version>
    </parent>
    <packaging>pom</packaging>
2. 父级工程Pom中,添加<dependency Management>, <depenedencies>, <dependency>等.如:
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
3.创建new module,并在该Pom中添加dependencies,如:
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>



三.SpringBoot 自定义属性的类型安全配置方法ConfigurationProperties：
1. 在application.properties文件中自定义属性，如：
book.author = aa;
book.name = bb;
2. 使用注释@ConfigurationProperties(prefix="book")
3. Generate: getter and setter



四.SpringBoot 如何配置profile:
1. 创建application-prod.properties, application-sit.properties, application-dev.properties文件
2. 通过在application.properties中设置spring.profiles.active = dev/sit/prod来指定活动的profile



五.SpringBoot如何进行整合测试:
1. 在测试的子module里添加：
   <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
   </dependency>
   
   <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <scope>test</scope>
   </dependency>
2. 创建controller包，添加Controller.java：
  @Controller
  @EnableAutoConfiguration
  public class SpringController{
     
     ...
     
     public static void main(String[] args){
        
        SpringApplication.run(SpringController.class, args);
     }
     
  }
3. 在子工程的/src/test/java 添加package,添加测试类的java,
如：
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



六.SpringBoot如何分离启动类和控制类：
1. 在src/main/java下新建package,
新建启动类applications.java，
启动类中添加注释@EnableAutoConfiguration+@ComponentScan(“com.project.controller”)
或者@SpringBootApplication(scanBasePackage = {"com.project"})
2. 在applications.java中添加：
public class SpringApplications {

    public static void main(String[] args){
        SpringApplication.run(SpringApplications.class, args);
    }
}
3. 在src/main/java下新建package，
新建控制类controller.java,
控制类添加注释@Controller



七.SpringBoot支持Rest风格,添加@PathVariable注释，如：
@RequestMapping("/info/{msg}")
public String show(@PathVariable String msg){
    return msg;
}



七.SpringBoot设置开发模式：
1. 在POM文件中添加两个dependency:
   <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>springloaded</artifactId>
    </dependency>
   <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
   </dependency>
   


八.SpringBoot 访问静态资源
1. Spring Boot 从classpath的 static, public或 /META-INF/RESOURCES文件夹或从ServletContext根目录提供静态内容
默认 js在resources/public目录中；image在 resources/static目录中

2. 设定自定义静态文件路径，js,css,image等：
spring.resources.static-location = classpath:/staitc/

九.SpringBoot使用FastJson解析json数据
两种方法：
第一种方法：
1. 让启动类applications.java继承WebMvcConfigureAdapter，
2. @Override重写  configureMessageConverters方法
public void configureMessageConverters(List<HttpMessageConverter<?>>
converters) {
   //创建FastJson消息转换器
     FastJsonHttpMessageConverter convert = new   FastJsonHttpMessageConverter();
    //创建FastJson的配置对象
    FastJsonConfig config = new FastJsonConfig();
    //对Json数据进行格式化
    config.setSerializerFeatures(SerializerFeature.PrettyFormat);

   //config对象传入转换器
   convert.setFastJsonConfig(config);
   converters.add(convert);

}
3. 设置application.properties,添加：
spring.http.encoding.force = true
第二种方法：
1. 在启动类application.java中添加注释@Bean:
public HttpMessageConverters fastJsonMessageConverter(){

    //创建FastJson消息转换器
    FastJsonHttpMessageConverter convert = new   FastJsonHttpMessageConverter();

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

十.SpringBoot自定义拦截器：
1. 建立Interceptor.java类，继承 WebMvcConfigurerAdapter
2. 重写HandlerInterceptor方法
@Configuration//声明是一个配置
public class MyInterceptor extends WebMvcConfigurerAdapter {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {

        HandlerInterceptor inter = new HandlerInterceptor() {
            public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
                System.out.println("自定义拦截器");
                return true;
            }

            public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {

            }

            public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {

            }
        };
        registry.addInterceptor(inter).addPathPatterns("/**");
    }
}
  
  
  
  
  
  
  
  
  
  
  
  
  
