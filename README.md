# SpringBoot-Case
Several cases about SpringBoot

一.SpringBoot 基于类型安全的配置：
1. 在application.properties文件中自定义属性，如：
book.author = aa;
book.name = bb;
2. 使用注释@ConfigurationProperties(prefix="book")
3. Generate: getter and setter

二.SpringBoot 如何配置profile:
1. 创建application-prod.properties, application-sit.properties, application-dev.properties文件
2. 通过在application.properties中设置spring.profiles.active = dev/sit/prod来指定活动的profile

三.SpringBoot 如何创建父工程和子工程：
1. 在父工程的pom里，<package>必须是pom
2. 在父工程的pom里，添加<dependency Management>, <depenedencies>, <dependency>等.
  如:
  <dependencyManagement>
    <dependencies>
        <dependency>
        <groupId>org.springframework.boot</groupId>
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
        </plugin>
    </plugins>
   </build>
  3. 在父工程添加new module
  4. 在子工程添加相应的<dependencies>,<dependency>等
   <dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
   </dependencies>
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
