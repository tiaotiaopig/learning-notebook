# Swagger简单入门

## 快速使用

1. 导入依赖
```
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
> 说明:前两份为官方依赖,最后的为美化.

2. 添加Swagger的Configuration类
```
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
        Contact contact = new Contact("李峰","http://www.diqiyuzhou.tk","2807229316@qq.com");
        return new ApiInfoBuilder()
                .title("【用户管理后台Swagger UI】")
                .description("用户管理后台接口")
                .contact(contact)
                .version("1.0")
                .build();
    }
}
```
> 需要特别注意的是swagger scan base package,这是扫描注解的配置，即你的API接口位置。

3. 具体使用
```
@RestController
@RequestMapping("/user")
@Api(tags = "用户管理")
public class UserController {

    private UserMapper userMapper;

    @Autowired
    public void setUserMapper(UserMapper userMapper){
        this.userMapper = userMapper;
    }

    @ApiOperation("添加用户")
    @PostMapping("/add")
    public String addUser(@RequestBody User user){

        userMapper.addUser(user);
        return "saved";
    }
```
> `@Api`用于控制类,tags属性

> `@ApiOperation`用于控制类中的方法.说明用法

```
public class User {

    @ApiModelProperty("主键")
    private Integer id;
    @ApiModelProperty("用户姓名")
    private String name;
    @ApiModelProperty("用户密码")
    private String password;
}
```
> `@ApiModelProperty`:标注实体类各个属性

4. 设定访问API doc的路由

    在配置文件中，application.yml中声明：
    `springfox.documentation.swagger.v2.path: /api-docs`
    这个path就是json的访问request mapping.可以自定义，防止与自身代码冲突。

    API doc的显示路由是：http://localhost:8080/swagger-ui.html(使用官方UI)

    使用bootstrapUI:http://localhost:8080/doc.html