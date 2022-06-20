# Spring注解

## 核心注解

+ `@Autowired` 

    > 用于field,setter方法以及构造方法上,实现依赖自动注入(ps:个人理解就是不再new对象了,交给spring容器来需要的时候自动创建对象)

    > 用于field时,比较便捷,但是不推荐,通常将该注解用于setter方法上.当用于构造方法上时,需要注意:一个类中只允许有一个构造方法使用此注解,此外,在sping4.3后,如果一个类只有一个构造方法,那么即使不使用该注解,sping也会自动注入相关的bean.

    > java有个注解**@Resource**和**@Autowired**很类似，在spring中也是支持的
    >
    > 在接口只有单一实现类的时候二者可以互换
    >
    > 在一个接口有多个实现类的时候，就会有歧义，没法确定使用哪个实现类
    >
    > **@Resource**先ByName,没有再ByType，（name指类名，首字母要小写，type指的是class的类型），可以通过**name**属性解决，**@Resource(name="man")**,或者  **@Resource + @Qualifier("woman")**
    >
    > **@Autowired** 先type 后 name，通过可以通过**@Qualifier("woman")**解决

+ `@Value`

## stereotype注解

+ `@Component`

    > 用于class上声明该类为一个spring组件(bean),交给spring容器管理(加入到应用上下文中applicationcontext)

+ `@Service`

    > 用在类上,说明此类是一个服务类(执行业务逻辑,计算,调用内部API),是@Component的一种具体形式.
    ps:就是说可以用Component代替,但是Service具有一定的语义

+ `@Controller`

    > 同上,具有说明该类是springcontroller的含义.

+ `@Repository`

    > 同service,说明此类用于访问数据库,dao的角色.

## Spring Boot注解

+ `@SpringBootApplication`

    > 此注解用于springboot项目的主类上(此类需要在base package中).会使springboot启动对base package以及其sub-package下的类进行ComponentScan.

    > 相当于以下三个注解的组合:`@Configuration`,`@EnableAutoConfiguration`,`@ComponentScan`

## SpringMVC和REST注解

+ `@RequestMapping`

    > 此注解用在类和方法上,用来映射web请求到一个handler类或者handler方法上,通常用于控制层,需要指定`path`属性,说明访问路径.

    > 是以下注解的总和:`@PostMapping`,`@GetMapping`,`@PutMapping`,`@DeleteMapping`

+ `@PathVariable`

    > 与`@RequestMapping`搭配使用,将请求路径上的参数绑定到请求方法参数上.

+ `@RequestBody`

    > 此注解用在请求handler方法参数上,用于将http请求的Body绑定到方法参数上.(ps:将前端发出请求的json对象转化为java对象并给到方法参数)

+ `@RequestParam`

    > 用在请求handler方法参数上,用于将http请求参数的值绑定到方法参数上.需要指定请求参数的名称.

+ `@Response`

    > 用在请求handler方法上,将方法返回的java对象转化为json对象返回给前端

+ `@RestController`

    > 相当于`@Controller`和`@Response`的组合,作用在类上,将类中的每个方法都是`@Response`

