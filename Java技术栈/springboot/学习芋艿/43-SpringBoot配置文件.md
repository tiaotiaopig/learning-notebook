# Spring boot配置文件

## 概述

> Spring配置文件演化过程:`xml -> JavaConfig -> SpringBoot自动配置`
>
> Spring Boot 支持 [Properties](https://zh.wikipedia.org/zh-cn/.properties)、[YAML](https://zh.wikipedia.org/zh/YAML)、JSON 三种格式的配置文件。目前主流的采用的是 Properties 或是 YAML 格式。比较推荐 YAML 格式，因为相比 Properties 来说，支持[层次结构化](https://zh.wikipedia.org/wiki/YAML#階層化的元素)、更丰富的[数据类型](https://zh.wikipedia.org/wiki/YAML#YAML的基本元件)。

## 使用

主要是`application.yml`配置文件,当然也可以自定义

### 多环境配置

生产环境和开发环境的配置可能是不同的,所以需要采用不通过的配置,免得每次都需要修改

配置文件的优先级取决于spring boot加载的先后顺序,后面加载的会配置前面相同的配置,优先级也就越高

针对多环境场景下，我们会给每个环境创建一个配置文件 `application-${profile}.yaml`。其中，`${profile}` 为**环境名**，对应到 Spring Boot 项目**生效的 Profile**。

例如说：`application-dev.yaml` 配置文件，对应 dev 开发环境。这样，我们在**生产**环境的服务器上，使用 `java -jar xxx.jar --spring.profiles.active=prod` 命令，就可以加载 `application-prod.yaml` 配置文件，从而连接上配置文件配置的**生产**环境的 MySQL、Redis 等等服务。

### 自定义配置

这里说一下:我们当如第三方库的时候,它们都有自己的`XXProperties`,这样我们才能在`application.yml`中进行配置

在引入第三方库时,我们可以在`application.yml`中对其进行配置,我们也可以通过[`@ConfigurationProperties`](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/context/properties/ConfigurationProperties.java) 和 [`@Value`](https://github.com/spring-projects/spring-framework/blob/master/spring-beans/src/main/java/org/springframework/beans/factory/annotation/Value.java) 注解，读取该自定义配置。

这里，我们引入 [`spring-boot-configuration-processor`](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-project/spring-boot-tools/spring-boot-configuration-processor) 依赖的原因是，编译项目时，会扫描 `@ConfigurationProperties` 注解，生成 `spring-configuration-metadata.json` 配置元数据文件给 IDEA。这样在 IDEA 中，可以带来两个好处：

- 在 `application.yaml` 配置文件，添加配置项时，IDEA 会给出提示.
- 点击配置项时，可以跳转到对应的 `@ConfigurationProperties` 注解的配置类.
- 在写好被`@ConfigurationProperties`注解的配置类之后,需要重新编译一下,这样在`application.yml`才会自动提示

使用方式主要有两种:

+ `@Autowired`注入,获取配置类实例,从而得到配置的内容
+ `@Value`获取配置项的值,这种没有上面配置也行,更加通用

## 配置随机值

Spring Boot 通过 [RandomValuePropertySource](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/env/RandomValuePropertySource.java) 类，提供配置项的**随机值**。例如说，用于临时密码、服务器端口等等。RandomValuePropertySource 通过在配置文件中，设置配置想的值为 `${random.*}` 来实现，目前提供了如下几种随机值：

- 随机整数。

  ```
  # 指定 int 整数。
  my-number=${random.int}
  # 指定 long 整数。
  my-long-number=${random.long}
  # 随机小于 10 的 int 整数。
  my-number-2=${random.int(10)}
  # 随机大于等于 10 ，小于等于 65536 的 int 整数。
  my-number-3=${random.int[1024,65536]}
  ```

- 随机字符串。

  ```
  # 普通字符串
  secret-1=${random.value}
  # UUID 字符串
  secret-2=${random.uuid}
  ```

🐶 不过，配置随机值，使用非常非常非常少，嘿嘿。知道即可哈~

## 配置引用

在配置文件中，一个配置项可以**引用**另外的配置项，进行拼接。示例如下：

```
order:
  pay-timeout-seconds: 120 # 订单支付超时时长，单位：秒。
  create-frequency-seconds: 10 # 订单创建频率，单位：秒
  desc: "订单支付超时时长为 ${order.pay-timeout-seconds} 秒，订单创建频率为 ${order.create-frequency-seconds} 秒"
```

- 最终，`order.desc` 配置项的输出结果为 `"订单支付超时时长为 120 秒，订单创建频率为 10 秒"`。

🐶 不过，配置引用，使用非常非常非常少，嘿嘿。知道即可哈~

## 命令行配置

Spring Boot 支持从**命令行参数**，读取作为配置。例如说，比较常见的，我们希望修改 SpringMVC 的服务器端口，则会使用 `java -jar xxx.jar --server.port=18080` 命令，将端口修改为 18080。

通过命令行连续的两个中划线 `--`，后面接 `配置项=配置值` 的方式，修改配置文件中对应的**配置项**为对应的**配置值**。例如说，`--配置项=配置值`。如果希望修改多个配置项，则使用多组 `--` 即可。例如说，`--配置项1=配置值1 --配置项2=配置值2`。

要注意，**命令行的配置高于配置文件**。这里我们使用[「2. 自定义配置」](https://www.iocoder.cn/Spring-Boot/config-file/?self#)的示例，进行下演示。直接在 IDEA 中，增加 Program arguments。如下图所示：![IDEA 配置 - local](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/04.png)

执行 Application 的 `#main(String[] args)` 方法，启动 Spring Boot 应用。输出日志如下：

```
# ValueCommandLineRunner 输出
2020-01-19 20:53:30.147  INFO 80935 --- [           main] s.l.p.Application$ValueCommandLineRunner : payTimeoutSeconds:60
2020-01-19 20:53:30.147  INFO 80935 --- [           main] s.l.p.Application$ValueCommandLineRunner : createFrequencySeconds:10

# OrderPropertiesCommandLineRunner 输出
2020-01-19 20:53:30.147  INFO 80935 --- [           main] ication$OrderPropertiesCommandLineRunner : payTimeoutSeconds:60
2020-01-19 20:53:30.147  INFO 80935 --- [           main] ication$OrderPropertiesCommandLineRunner : createFrequencySeconds:10
```

最终 `order.pay-timeout-seconds` 配置项的值为 60，来自命令行配置，符合预期~

😈 命令行配置使用的还是**非常多**的。例如说，通过 `--spring.profiles.active=prod` 命令行参数，设置生效的 Profile 为 `prod` 生产环境。

## 自定义配置文件名

Spring Boot 默认读取文件名为 `application` 的配置文件。例如说，`application.yaml` 配置文件。同时，Spring Boot 可以通过 `spring.config.name` 配置项，设置自定义配置文件名

那么，一般什么情况下，我们会需要自定义配置文件呢？我们来看看下图：![多项目](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/06.png)

- 在图中，我们可以看到在 `demo-application` 项目中，引入 `demo-rpc-service` 和 `demo-business` 项目。而引入的每个项目，都有自己的配置文件。如果每个项目都使用 `application.yaml` 配置文件，则会有且仅有一个生效。
- 因此，我们给每个项目创建了一个**独立**的配置文件名，同时设置 `spring.config.name` 配置项为 `application,demo,rpc`。这样，Spring Boot 就会读取这三个配置文件。并且，它和[「6. 多环境配置」](https://www.iocoder.cn/Spring-Boot/config-file/?self#)是可以共存使用的。

下面，我们来搭建一个读取**多个**自定义配置文件的示例。

创建 [`Application.java`](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-43/lab-43-demo-configname/src/main/java/cn/iocoder/springboot/lab43/propertydemo/Application.java) 类，配置 `@SpringBootApplication` 注解即可。代码如下：

```java
@SpringBootApplication
public class Application {

    /**
     * 设置需要读取的配置文件的名字。
     * 基于 {@link org.springframework.boot.context.config.ConfigFileApplicationListener#CONFIG_NAME_PROPERTY} 实现。
     */
    private static final String CONFIG_NAME_VALUE = "application,rpc";

    public static void main(String[] args) {
        // <X> 设置环境变量
        System.setProperty(ConfigFileApplicationListener.CONFIG_NAME_PROPERTY, CONFIG_NAME_VALUE);

        // 启动 Spring Boot 应用
        SpringApplication.run(Application.class, args);
    }

    @Component
    public class ValueCommandLineRunner implements CommandLineRunner {

        private final Logger logger = LoggerFactory.getLogger(getClass());

        @Value("${application-test}")
        private String applicationTest;

        @Value("${rpc-test}")
        private String rpcTest;

        @Override
        public void run(String... args) {
            logger.info("applicationTest:" + applicationTest);
            logger.info("rpcTest:" + rpcTest);
        }

    }

}
```

- 因为 `spring.config.name` 配置项，必须在读取配置文件之前完成设置，所以我们在 `<X>` 处，通过环境变量来设置。
- 在 ValueCommandLineRunner 中，我们打印了两个配置文件的配置项。

执行 Application 的 `#main(String[] args)` 方法，启动 Spring Boot 应用。输出日志如下：

```
2020-01-20 19:56:38.097  INFO 86516 --- [           main] s.l.p.Application$ValueCommandLineRunner : applicationTest:hahaha
2020-01-20 19:56:38.098  INFO 86516 --- [           main] s.l.p.Application$ValueCommandLineRunner : rpcTest:yeah
```

- 两个配置项都有对应的值，符合预期。

## 配置加密

考虑到安全性，我们可能最好将配置文件中的敏感信息进行加密。例如说，MySQL 的用户名密码、第三方平台的 Token 令牌等等。

配置加密的方案比较多，目前使用比较广泛的是 [Jasypt](http://www.jasypt.org/)。其介绍如下：

> FROM https://www.oschina.net/p/jasypt
>
> Jasypt 这个 Java 类包为开发人员提供一种简单的方式来为项目增加加密功能，包括：密码 Digest认证，文本和对象加密，集成 hibernate，Spring Security(Acegi) 来增强密码管理。
>
> Jasypt 开发团队推出了 Java 加密工具 Jasypt 1.4，它可与 Spring Framework、Hibernate 和 Acegi Security 集成。

在 [`pom.xml`](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-43/lab-43-demo-jasypt/pom.xml) 文件中，引入相关依赖。

```xml
<!-- 实现对 Jasypt 实现自动化配置 -->
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>3.0.2</version>
</dependency>
```

- 引入 [`jasypt-spring-boot-starter`](https://mvnrepository.com/artifact/com.github.ulisesbocchio/jasypt-spring-boot-starter) 依赖，实现对 Jasypt 的自动化配置。舒服啊~

在 [`application.yml`](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-43/lab-43-demo-jasypt/src/main/resources/application.yaml) 中，添加 Jasypt 配置，如下：

```yaml
spring:
  application:
#    name: ENC(YFJ/nBxn89KUfbBW6cPxqjFo3K63GJaOcDMSKWOsFxMAKCgBiLoMAw==)
    name: demo-application

jasypt:
  # jasypt 配置项，对应 JasyptEncryptorConfigurationProperties 配置类
  encryptor:
    algorithm: PBEWithMD5AndDES # 加密算法
    password: ${JASYPT_PASSWORD} # 加密秘钥
```

- `spring.application.name` 配置项，设置应用名。为了方便，我们稍后针对它实现配置加密的功能。

- `jasypt.encryptor`配置项，设置 Jasypt 配置，对应JasyptEncryptorConfigurationProperties配置类

- `algorithm`配置项，设置使用 PBEWithMD5AndDES 算法。这里不采用默认的PBEWITHHMACSHA512ANDAES_256 的原因是，会报如下异常：

  ```
  org.jasypt.exceptions.EncryptionOperationNotPossibleException: Encryption raised an exception. A possible cause is you are using strong encryption algorithms and you have not installed the Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files in this Java Virtual Machine
  ```

`password` 配置项，设置加密秘钥，非常重要。这里，我们显然不能直接把该配置项设置在配置文件中，不然不就白加密了嘛。因此，这里我们采用 `JASYPT_PASSWORD` 环境变量。当然，我们也可以通过命令行配置，例如说 `java -jar xxx.jar --jasypt.encryptor.password=秘钥` 操作。

因为我们设置了 `jasypt.encryptor.password` 配置项读取 `JASYPT_PASSWORD` 环境变量，所以胖友记得设置下噢。以 MAC 操作系统为例：

```yaml
# 编辑 bash_profile 文件
vi ~/.bash_profile
# 行末增加 JASYPT_PASSWORD 秘钥。添加完成后，保存并退出
export JASYPT_PASSWORD="justdoit" # 具体秘钥，自己选择。
```

- 记得重启 IDEA 噢，重新读取环境变量。

### 简单测试

下面，我们进行下简单测试。

- 首先，我们会使用 Jasypt 将 `demo-application` 进行加密，获得加密结果。
- 然后，将加密结果，赋值到 `spring.application.name` 配置项。
- 最后，我们会使用 Jasypt 将 `spring.application.name` 配置项解密。

创建 [JasyptTest](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-43/lab-43-demo-jasypt/src/test/java/cn/iocoder/springboot/lab43/propertydemo/JasyptTest.java) 测试类，编写测试代码如下：

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class JasyptTest {

    @Autowired
    private StringEncryptor encryptor;

    @Test
    public void encode() {
        String applicationName = "demo-application";
        System.out.println(encryptor.encrypt(applicationName));
    }

    @Value("${spring.application.name}")
    private String applicationName;

    @Test
    public void print() {
        System.out.println(applicationName);
    }

}
```

- 首先，执行 `encode()`方法，手动使用 Jasypt 将**demo-application**进行加密，获得加密结果。加密结果如下：xQZuD8KnkqzIGep0FFH0DYJ3Re9TrKTdvu2fxIlWNpwFcdNGhkpCag==

- 然后，将 `application.yaml` 配置文件的 `spring.application.name` 配置项，设置为加密结果 `ENC(xQZuD8KnkqzIGep0FFH0DYJ3Re9TrKTdvu2fxIlWNpwFcdNGhkpCag==)`。注意，前后需要使用 `ENC()` 进行包裹噢。
- 最后，执行 `#print()` 方法，**自动**使用 Jasypt 将 `spring.application.name` 配置项解密。解密结果如下：demo-application.
  - 成功正确解密，符合预期。