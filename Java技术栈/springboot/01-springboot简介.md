# springboot 简介

## 简介

> springboot 可以创建单机的、生产环境级别的、可以运行的基于spring的应用
>
> 可以视为 spring 是一个平台，整合其他第三方库（third-party libraries）
>
> 配置极少，**开箱即用**（out of box），偏离默认，自行配置
>
> 提供了一系列非功能的特性，但在大型项目中很常见，例如（**embedded servers, security**, metrics, health checks, and externalized configuration）
>
> 你可以打成jar包进行部署，同时也支持传统的war部署方式

## 环境需求

1. Spring Boot 2.5.2 requires [Java 8](https://www.java.com/) and is compatible up to and including Java 16. [Spring Framework 5.3.8](https://docs.spring.io/spring-framework/docs/5.3.8/reference/html/) or above is also required

2. Explicit build support is provided for the following build tools:

   | Build Tool | Version               |
   | :--------- | :-------------------- |
   | Maven      | 3.5+                  |
   | Gradle     | 6.8.x, 6.9.x, and 7.x |

3. Spring Boot supports the following **embedded servlet containers**:

   | Name         | Servlet Version |
   | :----------- | :-------------- |
   | Tomcat 9.0   | 4.0             |
   | Jetty 9.4    | 3.1             |
   | Jetty 10.0   | 4.0             |
   | Undertow 2.0 | 4.0             |

   You can also deploy Spring Boot applications to any **Servlet 3.1+** compatible container.

## 安装

+ 你可以把springboot简单当作传统的java标准库，使用时将**spring-boot-*.jar**放入**classpath**,无需特殊配置
+ 推荐使用构建工具来支持依赖管理（例如：Maven，Gradle）

+ springboot的依赖在`org.springframework.boot`的`groupId`下
+ Typically, your Maven POM file inherits from the `spring-boot-starter-parent` project and declares dependencies to one or more [“Starters”](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters). Spring Boot also provides an optional [Maven plugin](https://docs.spring.io/spring-boot/docs/current/reference/html/build-tool-plugins.html#build-tool-plugins.maven) to create executable jars.
+ Starter 就是依赖的集合，方便使用管理，不需要你再找地址一个一个导入了
+ 提供maven插件，帮助构建jar
+ spring.io这个网站提供了通用的项目结构模板，idea也有整合，非常便捷

## 快速上手

1. 创建 **POM.xml**

   pom中添加项目所需的依赖地址，依赖就是项目需要用到的java标准库（**Standard Java Library**），给出maven地址，maven会自动从中央仓库下载

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>com.example</groupId>
       <artifactId>myproject</artifactId>
       <version>0.0.1-SNAPSHOT</version>
   
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.5.2</version>
       </parent>
   
       <!-- Additional lines to be added here... -->
       
       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
   	</dependencies>
   
   </project>
   ```

    You can test it by running `mvn package` (for now, you can ignore the “jar will be empty - no content was marked for inclusion!” warning).

2. Adding Classpath Dependencies

   use the `spring-boot-starter-parent` in the `parent` section of the POM. The `spring-boot-starter-parent` is a special starter that provides **useful Maven defaults**. It also provides a [`dependency-management`](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.dependency-management) section so that you can omit `version` tags for “blessed” dependencies.

   `spring-boot-starter-web` including the Tomcat web server and Spring Boot itself.

## 应用入口

默认Maven编译源是`src/main/java`,所以你需要这样的目录结构

添加一个入门java文件`src/main/java/MyApplication.java`

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@EnableAutoConfiguration
public class MyApplication {

    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }

}
```

+ `@RestController`This is known as a *stereotype* annotation. It provides hints for people reading the code and for Spring that the class plays a specific role. In this case, our class is a web `@Controller`, so Spring considers it when handling incoming web requests.

+ *stereotype* annotation:(原型注解，构造型注解)

  `@Component`: 一个通用的组件注解；
  `@Controller` : 该注解表示视图层的一个控制器组件；
  `@Service` : 该注解表示业务逻辑层的一个组件；
  `@Repository` : 该注解表示数据持久层的一个组件。

  首先语义作用，声明组件在项目中扮演的角色，重要的是声明为spring组件，可以被spring容器所管理，使用`@Autowired`进行依赖注入

+ The `@RequestMapping` annotation provides “routing” information. It tells Spring that any HTTP request with the `/` path should be mapped to the `home` method. The `@RestController` annotation tells Spring to render the resulting string directly back to the caller.（java bean ==> json）

+ The second class-level annotation is `@EnableAutoConfiguration`. This annotation tells Spring Boot to “guess” how you want to configure Spring, based on the jar dependencies that you have added. Since `spring-boot-starter-web` added Tomcat and Spring MVC, the auto-configuration assumes that you are developing a web application and sets up Spring accordingly.

+ Auto-configuration is designed to work well with “Starters”, but the two concepts are not directly tied. You are free to pick and choose jar dependencies outside of the starters. Spring Boot still does its best to auto-configure your application.

+ The final part of our application is the `main` method. This is a standard method that follows the Java convention for an **application entry point**. 

+ `@SpringBootApplication` is a convenience annotation that adds all of the following:

  - `@Configuration`: Tags the class as a source of bean definitions for the application context.
  - `@EnableAutoConfiguration`: Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
  - `@EnableWebMvc`: Flags the application as a web application and activates key behaviors, such as setting up a `DispatcherServlet`. Spring Boot adds it automatically when it sees `spring-webmvc` on the classpath.
  - `@ComponentScan`: Tells Spring to look for other components, configurations, and services in the the `com.example.testingweb` package, letting it find the `HelloController` class.

## 项目运行

`spring-boot-starter-parent` 作用，自动配置和依赖版本控制

项目根目录运行：`mvn spring-boot:run`,即可启动（Application.java所在目录）

`ctrl-c` 停止运行

## 打包

如果是通过spring.io创建的项目，在根目录下，会有**mvnw**构建脚本

可以直接在命令行，`./mvnw spring-boot:run`直接运行

`./mvnw clean package`清除并打包

`java -jar target/gs-rest-service-cors-0.1.0.jar` java命令直接运行

We finish our example by creating a completely self-contained executable jar file that we could run in production. Executable jars (sometimes called “fat jars”) are archives containing your compiled classes along with all of the jar dependencies that your code needs to run.

创建一个完全自我包含的、可在生产环境中运行的可执行jar包（类似于exe了）

它是一个压缩包，包含所有编译好的class文件和所有代码运行所需要的依赖jar包，可以用压缩软件打开的

> java并没有提供一个标准的方式去加载嵌套的jar包，这使得发布自包含（self-contained）应用时遇到问题，self-contained 个人理解就是不依赖环境，项目的依赖都一起打包了，只要有**jre**就行
>
> springboot提供了创建可执行jar的方法，让你能够直接创建

To create an executable jar, we need to add the `spring-boot-maven-plugin` to our `pom.xml`. To do so, insert the following lines just below the `dependencies` section:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

> The `spring-boot-starter-parent` POM includes `<executions>` configuration to bind the `repackage` goal. If you do not use the parent POM, you need to declare this configuration yourself. See the [plugin documentation](https://docs.spring.io/spring-boot/docs/2.5.2/maven-plugin/reference/htmlsingle/#getting-started) for details.

Save your `pom.xml` and run `mvn package` from the command line, as follows:

`mvn package`

If you want to peek inside, you can use `jar tvf`, as follows:

`$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar`

You should also see a much smaller file named `myproject-0.0.1-SNAPSHOT.jar.original` in the `target` directory. This is the original jar file that Maven created before it was **repackaged** by Spring Boot.

To run that application, use the `java -jar` command, as follows:

`java -jar target/myproject-0.0.1-SNAPSHOT.jar `