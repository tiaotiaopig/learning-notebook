# springboot 使用 cors

## 前后端分离跨域问题

我们在前后端分离的情况下，浏览器由于同源策略，不会携带cookie，这让我们在做登录认证的时候，遇到了麻烦。通过自定义请求头携带token，可以实现认证，自定义请求头，会产生非简单请求，分两次发送，一次预请求，预请求通过，才会发送普通请求

在使用springboot解决跨域问题，主要是通过**跨域资源共享**（**cors**），还有其他解决方式，没试过

其主要实现原理，就是拦截请求和响应，在响应头中添加cors相关的响应头，例如`Access-Control-Allow-Origin`

代码实现只需两步，真的很简单，过程却很曲折

1. `@CrossOrigin`

   该注解可以作用在方法上，也可以作用在controller类上，**开启跨域**，默认**所有origin**都允许，所有方法被允许，**所有请求头**被允许，也可以指定origin，例如192.168.50.145:5533，这样就只有origin是这个地址的请求会被响应

   该注解有以下属性可以配置

   - `origins`
   - `methods`
   - `allowedHeaders`
   - `exposedHeaders`
   - `allowCredentials`
   - `maxAge`

   和全局配置的方法时一一对应的

   基于注解的方式，是更加细粒度的cors控制，基于过滤器的方式，是全局的cors方式，两种允许组合

2. 跨域配置（全局cors）

   ```java
   @Configuration
   public class SaTokenConfiguration implements WebMvcConfigurer {
   
       private StpUtil StpUserUtil;
       /**
        * 使用cors解决跨域问题，nginx不需要额外配置
        * 为了使前后端分离，我们需要使用跨域资源共享解决跨域问题
        * 主要还是为了让跨域能够带cookie,或者自定义请求头，进行认证授权
        * @param registry
        */
       @Override
       public void addCorsMappings(CorsRegistry registry) {
           registry.addMapping("/**")
                   .allowedOrigins("http://127.0.0.1:5500", "http://192.168.50.17:6688")
                   .allowedMethods("GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS")
                   .allowCredentials(true)
                   .maxAge(3600)
                   .allowedHeaders("*");
           // 经测试，允许的额外请求头参数，可以设为"satoken", "content-type"
           // content-type 在登陆时用到啦，为了方便我们还是使用了通配符
       }
       
       // 注册Sa-Token的拦截器
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           // 注册路由拦截器，自定义验证规则
           registry.addInterceptor(new SaRouteInterceptor((req, res, handler) -> {
   
               // 登录验证 -- 拦截所有路由，并排除/user/doLogin 用于开放登录
   //            SaRouter.match("/**", "/user/doLogin", StpUtil::checkLogin);
   
               // 这里把预请求给过滤掉
               // 跨域的自定义请求头，会发送两次请求，但是预请求中不包含自定义请求头
               // 因此已定义请求头也会鉴权，铁定失败，我们在这里过滤一下
               if (!"OPTIONS".equals(req.getMethod())) {
                   // 登录验证 -- 排除多个路径
                   SaRouter.match(Collections.singletonList("/**"), Arrays.asList("/user/doLogin", "/user/captcha"), StpUtil::checkLogin);
               }
   
           })).addPathPatterns("/**")
                   .excludePathPatterns("/doc.html","/v2/api-docs", "/swagger-resources/configuration/ui",
                           "/swagger-resources", "/swagger-resources/configuration/security",
                           "/swagger-ui.html", "/webjars/**", "/images/**", "/layuiadmin/**", "/login.html");
       }
   }
   ```

   继承**WebMvcConfigurer**，java8以上使用，主要时接口允许有默认实现方法了

   重写**addCorsMappings**方法，配置跨域项

   > 1. `allowedOrigins`允许请求的origin，就是前端的地址
   > 2. `allowedMethods`允许的http方法，`*`代表**GET,POST,HEAD**不是所有
   > 3. `allowCredentials`允许带cookie
   > 4. `maxAge`在此时间内相同请求，不会再发送预检请求
   > 5. `allowedHeaders`允许的自定义请求头，一般设为通配符

使用 **nginx** 作为前端的代理服务器，**不需要额外配置跨域**，网上说两边都要配置，实践下来发现，只要后端配置一下就好，使用`nginx`配置也很简单，也是这几项

有人说使用了`nginx`进行请求的代理，通过将跨域请求转为同域请求，也可以实现跨域，没实践，暂不确定

在使用**satoken框架**做认证的时候，可以在**cookie**，**自定义请求头**，**请求体**中带token，进行认证授权

但是前后端分离，没法使用cookie方式，请求体的方式没试过，使用的自定义请求头的方式（作者推荐）

**需要注意**，预检请求（option请求）要**过滤**掉，它没法带token，也无需做认证，拦截器实现路径认证鉴权，会把所有请求都拦截

