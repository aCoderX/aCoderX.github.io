---
title: spring-boot的简单搭建
date: 2016-10-09 16:04:45
tags: [spring,java]
---
### spring-boot简介

> spring-boot旨在帮助开发者更容易地创建基于Spring的应用程序和服务，使得现有的和新的Spring开发者能够最快速地获得所需要的Spring功能。

Spring Boot不生成代码，且完全不需要XML配置。
 <!--more-->
---
### 工程的构建
这里使用maven构建spring boot工程
#### 创建pom文件
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.3.5.RELEASE</version>
  </parent>
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <version>${spring.boot.version}</version>
        <executions>
          <execution>
            <goals>
              <goal>repackage</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
    <finalName>spring-boot-demo</finalName>
  </build>
```
spring-boot-maven-plugin插件将spring-boot程序在package阶段打包成可执行的jar包。

spring-boot的依赖是模块化的，这里用到的是spring-boot-starter-web，其他依赖都是通过其传递引入
```
<parent>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-parent</artifactId>  
    <version>1.3.5.RELEASE</version> 
</parent>  
```

#### 创建Application.java
```java
@ComponentScan  //自动扫包 当前包和子包
@EnableAutoConfiguration //让 Spring Boot 根据应用所声明的依赖来对 Spring 框架进行自动配置
public class Application {
    public static void main(String[] args) throws Exception{
        SpringApplication.run(Application.class,args);
    }
}
```
也可直接使用@SpringBootApplication注解

#### 创建控制类TestController
```java
@RestController
@RequestMapping("/springboot")

public class TestController {
    @Autowired
    User user;

    @RequestMapping(value = "/{name}", method = RequestMethod.GET)
    public String sayWorld(@PathVariable("name")String name){
        return "Hello"+name;
    }
}
```
这样最简单的spring-boot就搭建好了，运行main方法就可以了，在浏览器输入[http://localhost:8080/springboot/gblx](http://localhost:8080/springboot/gblx)就可以看到效果了。

*ps: @ComponentScan只能扫描当前包和当前包的子包，所以controller可以放在同包下或者子包下*

---
### 读取配置文件
spring boot 默认支持两个格式的配置文件:.properties .yml。

这里使用properties文件
#### 新建application.properties
```
#application.properties配置文件需要放在resources目录下
name=gblx
age=${random.int[0,100]}
remark=hello,my name is ${userName},age is ${age}
user.address=china,nanjing
```

#### 新建User.java实体类
```java
@Component
@ConfigurationProperties(prefix = "user")
public class User {
    private @Value("${name:default}") String name;
    private @Value("${age}") int age;
    private @Value("${remark}") String remark;
    private String address;
    //set、get方法...
}
```
#### controller方法
```java
public String sayWorld(@PathVariable("name")String name){
        System.out.println("username:"+user.getName());
        System.out.println("age:"+user.getAge());
        System.out.println("remark:"+user.getRemark());
        System.out.println("address:"+user.getAddress());
        return "Hello"+name;
    }
```

---
### 内嵌容器tomcat配置
spring boot默认web程序启用tomcat内嵌容器tomcat，监听8080端口,servletPath默认为 / 通过需要用到的就是端口、上下文路径的修改，在spring boot中其修改方法及其简单；
```
在资源文件中配置：   
server.port=9090 
server.contextPath=/demo
```
启动程序，执行[http://localhost:9090/demo/springboot/gblx](http://localhost:9090/demo/springboot/gblx)即可成功访问

如果需要使用ssl或者多端口监听等高级配置，则需要编写配置类