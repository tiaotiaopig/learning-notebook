# SpringBoot项目中static和template文件夹的区别

1. SpringBoot项目创建后,resources下默认有两个文件夹static和template.一般static存放静态资源,template存放动态资源.

2. 在static文件夹下新建cc.html,浏览器输入localhost:8080/cc.html可以正常访问.同样的操作在访问template下的html则找不到文件

3. 在通过接口访问的时候,如果是在static下的文件,在controller方法中return "cc.html".  如果在template下则是 return "cc";

4. 编译之后static文件夹下的文件和templates文件夹下的文件其实会出现在同一个目录下，引入样式或者默认图片的时候注意

5. 使用idea打开html时,静态的资源是通过绝对路径进行加载(所以按照4的路径,是无法加载的,因为在项目运行时对路径进行了一层映射)

6. spring boot默认静态资源的映射路径分别为:

     * classpath:/META-INF/resources
     * classpath:/resources
     * classpath:/static
     * classpath:/public

     6.1 优先级顺序为：META-INF/resources > resources > static > public

     6.2 spring.mvc.static-path-pattern指定项目启动后静态资源的路径,默认是/**,所以上述的四种静态路径都会被映射为:/,在html中引用css,js,img等静态资源时要注意,即上面的第四点

     6.3 spring.resources.static-locations默认的就是上面的四种静态路径

---

## 自定义springMVC

     如果Spring Boot提供的Sping MVC不符合要求，则可以通过一个配置类（注解有@Configuration的类）加上@EnableWebMvc注解来实现完全自己控制的MVC配置。

     当然，通常情况下，Spring Boot的自动配置是符合我们大多数需求的。在你既需要保留Spring Boot提供的便利，有需要增加自己的额外的配置的时候，可以定义一个配置类并继承WebMvcConfigurerAdapter,无需使用@EnableWebMvc注解。

     这里我们提到这个WebMvcConfigurerAdapter这个类，重写这个类中的方法可以让我们增加额外的配置，这里我们就介绍几个常用的。

### 自定义资源映射addResourceHandlers

     比如，我们想自定义静态资源映射目录的话，只需重写addResourceHandlers方法即可。

```java
@Configuration
public class MyWebMvcConfigurerAdapter extends WebMvcConfigurerAdapter {
    /**
     * 配置静态访问资源
     * @param registry
     */
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/my/**").addResourceLocations("classpath:/my/");
        super.addResourceHandlers(registry);
    }
}
```
通过addResourceHandler添加映射路径，然后通过addResourceLocations来指定路径。我们访问自定义my文件夹中的elephant.jpg 图片的地址为 http://localhost:8080/my/elephant.jpg

如果你想指定外部的目录也很简单，直接addResourceLocations指定即可，代码如下：

```java
@Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/my/**").addResourceLocations("file:E:/my/");
        super.addResourceHandlers(registry);
    }
```
addResourceLocations指的是文件放置的目录，addResoureHandler指的是对外暴露的访问路径
---

### 页面跳转addViewControllers

     以前写SpringMVC的时候，如果需要访问一个页面，必须要写Controller类，然后再写一个方法跳转到页面，感觉好麻烦，其实重写WebMvcConfigurerAdapter中的addViewControllers方法即可达到效果了

```java
/**
     * 以前要访问一个页面需要先创建个Controller控制类，再写方法跳转到页面
     * 在这里配置后就不需要那么麻烦了，直接访问http://localhost:8080/toLogin就跳转到login.jsp页面了
     * @param registry
     */
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/toLogin").setViewName("login");
        super.addViewControllers(registry);
    }
```
值的指出的是，在这里重写addViewControllers方法，并不会覆盖WebMvcAutoConfiguration中的addViewControllers（在此方法中，Spring Boot将“/”映射至index.html），这也就意味着我们自己的配置和Spring Boot的自动配置同时有效，这也是我们推荐添加自己的MVC配置的方式。
## Spring Security使用自定义表单登录要点:

1. 创建一个form表单,至少要要有用户名,密码两项内容,提交方法为post,提交路径(action属性)为loginProcessingUrl
2. 继承WebSecurityConfigurerAdapter覆盖configure(HttpSecurity http)
     2.1 调用formLogin()使用表单登录(ps:没有指定loginPage,则使用security默认表单,默认路径为/login)
     2.2 指定loginPage为自定义的登录界面路径,loginProcessingUrl和表单的action属性一致
               loginProcessingUrl若不指定,则默认和loginPage一致,
               spring security使用loginProcessingUrl路径来接受登录参数,完成认证
               usernameParameter为表单用户名项的参数名,不指定默认为username
               passwordParameter为表单登录密码的参数名,不指定默认为password
               完成上述四项设定,才能将自定义页面和Security认证逻辑绑定(因为我们要将用户名和密码传给Security)
     2.3 permitAll()为登录路径放行
          浏览器请求页面一般是get请求
     defaultSuccessUrl()是重定向,发送的是get请求
     successForwardUrl()是转发请求,转发表单登录的post请求
     如果向GetMapping路径发送post请求会报405错误
     重定向是302