## 整合示例

本文将以连接MySQL数据库为例(5.5+),使用mybatis基于xml的方式完成一张user表的crud操作,最后使用Swagger自动生成接口文档,对相关方法进行测试.

## 创建项目(添加依赖)
我们使用idea帮我们创建,省得到spring官网进行生成然后导入本地了.
以下说明详细步骤.
1. Create New Project -> Spring Initializr -> Next -> 完成相关配置
	
	![在这里插入图片描述](https://img-blog.csdnimg.cn/20200824095144828.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29zY2hpbmFfNDA1MzA1NzY=,size_16,color_FFFFFF,t_70#pic_center)

	使用maven或者gradle随意,Packaging选择jar,jdk使用1.8,然后Next

2. 选择依赖,如下图所示:
	![在这里插入图片描述](https://img-blog.csdnimg.cn/20200824095351957.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29zY2hpbmFfNDA1MzA1NzY=,size_16,color_FFFFFF,t_70#pic_center)
	Lombok是一种简化工具,也可以不用,使用ide自动生成getter和setter,finsh即可,等待依赖下载,注意配置maven仓库源,使用国内源.

3. 配置数据库和mybaits
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020082409592222.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29zY2hpbmFfNDA1MzA1NzY=,size_16,color_FFFFFF,t_70#pic_center)
	可以看到使用这种方式创建非常快捷,项目目录已经生成好了,在resource有个自动生成的application.properties,我们将其删除,创建application.yml(两者作用本质是一样的,使用哪个都可以,这里我们使用yml方式,比较简洁清晰)
	

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/db_user?characterEncoding=utf-8&useSSL=true&serverTimezone=Asia/Shanghai
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver

mybatis:
  type-aliases-package: edu.learn.usermanager.entity
  mapper-locations: classpath:mapper/*Mapper.xml
```
相关配置如上(根据自己情况进行设置,idea会有自动提示补全)
说明: type-aliases-package可以设置为实体类的包路径,这样就可在写mapper.xml时,直接使用类名作为全限定类名的别名.mapper-locations: 指定mapper.xml的路径,我们这里的路径是在resource下创建的mapper目录,里面放我们的*Mapper.xml文件,你也可以使用其他命名

> 说明：Maven 遵守“约定大于配置”的原则，即：在项目编译的时候，会默认将**src/main/resources**目录下的配置文件（**.xml**等文件）加载到**classpath**路径下，**src/main/java**下的Java源码会被编译成class文件，放到classpath路径下，但是**src/main/java**路径下的配置文件默认是不会加载到classpath下，导致运行时文件找不到，我们可以在**pom.xml**上添加如下配置，设置maven资源文件扫描路径，解决此问题。
>
> ~~~xml
> 			<!--  在 build 中配置 resources 解决非 src/main/resources 路径下资源文件不生效的问题-->
>     <build>
>         <resources>
>             <resource>
>                 <directory>src/main/java</directory>
>                 <includes>
>                     <include>**/*.xml</include>
>                     <include>**/*.properties</include>
>                 </includes>
>                 <filterting>true</filterting>
>             </resource>
>             <resource>
>                 <directory>src/main/resources</directory>
>                 <includes>
>                     <include>**/*.xml</include>
>                     <include>**/*.properties</include>
>                 </includes>
>                 <filterting>true</filterting>
>             </resource>
>         </resources>
>     </build>
> ~~~
>
> 

4. 编写代码
	实体类 User.java

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {

    @ApiModelProperty("主键")
    private Integer id;
    @ApiModelProperty("用户姓名")
    private String name;
    @ApiModelProperty("用户密码")
    private String password;
}
```
@ApiModelProperty是Swagger的注解,现在可不用

映射类 UserMapper.java

```java
@Mapper
public interface UserMapper {

    /**
     * 根据id查找用户并返回
     * @param id
     * @return
     */
    User findUserById(Integer id);

    /**
     * 添加用户
     * @param user
     * @return
     */
    void addUser(User user);

    /**
     * 删除用户
     * @param id
     * @return
     */
    void deleteUser(Integer id);

    /**
     * 修改用户
     * @param user
     * @return
     */
    void editUser(User user);

    /**
     * 查询所有用户
     * @return
     */
    List<User> findAll();
}
```
UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.learn.usermanager.repository.UserMapper">
    <select id="findUserById" resultType="User">
        select * from user where id = #{id}
    </select>


    <insert id="addUser" parameterType="User" useGeneratedKeys="true" keyProperty="id">
        insert into user(name,password) values (#{name},#{password})
    </insert>

    <update id="editUser" parameterType="User">
        update user set name = #{name}, password = #{password} where id =#{id}
    </update>

    <delete id="deleteUser" >
        delete from user where id = #{id};
    </delete>

    <select id="findAll" resultType="User">
        select * from user;
    </select>
</mapper>
```
控制类调用映射类,对前端请求进行响应 UserController.java

```java
@RestController
@RequestMapping("/user")
@Api(tags = "用户管理")
public class UserController {

    private final UserMapper userMapper;

    public UserController(UserMapper userMapper) {
        this.userMapper = userMapper;
    }


    @ApiOperation("添加用户")
    @PostMapping("/add")
    public String addUser(@RequestBody User user){

        userMapper.addUser(user);
        return "saved";
    }

    @ApiOperation("更新用户")
    @PostMapping("/update")
    public String editUser(@RequestBody User user){

        userMapper.editUser(user);
        return "updated";
    }

    @ApiOperation("查找用户")
    @GetMapping("/find")
    public User findUser(Integer id){
        return userMapper.findUserById(id);
    }

    @ApiOperation("删除用户")
    @PostMapping("/delete")
    public String deleteUser(Integer id){
        userMapper.deleteUser(id);
        return "delete";
    }

    @ApiOperation("查询所有")
    @PostMapping("/findAll")
    public List<User> findAll(){

        return userMapper.findAll();
    }
}
```

@Api等是Swagger注解,现在用不到.至此crud完成,其实这种简单的操作,可以使用mybatis的注解方式,不需要配置xml了.使用自动注入时,idea会报错(找不到bean:userMapper,不用理他,可以正常运行的,因为我们已经使用了@Mapper注解的,会添加到spring容器的)

5. 开启MySQL,创建db_user数据库,创建user表,我们可以在idea中完成.
	

```sql
create database if not exists db_user character set utf8;
use db_user;
create table user(
    id int primary key auto_increment,
    name varchar(20),
    password varchar(20)
);
```
注意:指定数据库字符集utf8,不然在运行的时候会因为字符集不同而报错
自此我们就可启动项目

6. 进行简单测试
	![在这里插入图片描述](https://img-blog.csdnimg.cn/2020082410451235.png#pic_center)
	其他方法的测试,我们需要借助一些工具(curl,postman等)
	这里我们使用简单的文档生成工具Swagger,可以对各个接口方法进行测试

## 整合Swagger进行测试

## 添加依赖

```xml
<!-- 使用swagger -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.7.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.7.0</version>
        </dependency>
        <!-- swagger美化 -->
        <dependency>
        <groupId>com.github.xiaoymin</groupId>
        <artifactId>swagger-bootstrap-ui</artifactId>
        <version>1.9.3</version>
        </dependency>
```
在pom.xml加入依赖

## 添加配置类
SwaggerConfiguration.java
```java
@Configuration
@EnableSwagger2
public class SwaggerConfiguration {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("edu.learn.usermanager.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        Contact contact = new Contact("你的名字","http://www.diqiyuzhou.tk","邮箱");
        return new ApiInfoBuilder()
                .title("【用户管理后台Swagger UI】")
                .description("用户管理后台接口")
                .contact(contact)
                .version("1.0")
                .build();
    }
}
```
注意:basePackage方法中设置你的controller的包路径

## 使用注解
前提到的@Api相关的注解可以加入了,另一个就是在启动类上加上@EnableSwaggerBootstrapUI,这里我们使用了第三方的SwaggerUI
@MapperScan("edu.learn.usermanager.repository")指定映射类的扫描路径,这样在编写每个映射类的时候就不用再使用@Mapper注解了

```java
@SpringBootApplication
@EnableSwaggerBootstrapUI
@MapperScan("edu.learn.usermanager.repository")
public class UserManagerApplication {

    public static void main(String[] args) {
        
        SpringApplication.run(UserManagerApplication.class, args);
    }

}
```
## 对每个方法进行测试
重新启动项目,浏览器打开:http://localhost:8080/doc.html
	![在这里插入图片描述](https://img-blog.csdnimg.cn/2020082411012073.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29zY2hpbmFfNDA1MzA1NzY=,size_16,color_FFFFFF,t_70#pic_center)
自动生成了接口文档,同时也非常方便我们进行测试
点击调试,真的非常方便,比起直接使用curl的方式