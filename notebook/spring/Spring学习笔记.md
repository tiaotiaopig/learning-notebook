# Spring 学习笔记
---
## 程序的耦合

1. 耦合：程序间的依赖关系
   + 包括：
       + 类之间的依赖
       + 方法间的依赖
   + 解耦：
       + 降低程序间的依赖关系
   + 实际开发中：
       + 应该做到：编译期不依赖，运行时才依赖。
    + 解耦的思路：
       + 第一步：使用反射来创建对象，而避免使用new关键字。
       + 第二步：通过读取配置文件来获取要创建的对象全限定类名

---
## factory模式解耦
 * Bean：在计算机英语中，有可重用组件的含义。
 
 * JavaBean：用java语言编写的可重用组件。
 *      javabean >  实体类

 *   它就是创建我们的service和dao对象的。
 
 *   第一个：需要一个配置文件来配置我们的service和dao
 *           配置的内容：唯一标识=全限定类名（key=value)

 *   第二个：通过读取配置文件中配置的内容，反射创建对象

 *   我的配置文件可以是xml也可以是properties

---
## Spring IOC容器
### 一句话理解：Spring容器就相当于一个Map(id-obj)集合，通过id可以获取相应的对象，对象的创建交给容器来管理，这就是控制反转--ioc 一个对象需要使用另一个对象来完成功能，一般将其作为该对象的成员对象，这就产生了依赖，通过容器来完成对成员对象的赋值，这就是IC,依赖注入

1. 获取spring的Ioc核心容器，并根据id获取对象

     * ApplicationContext的三个常用实现
       + ClassPathXmlApplicationContext：它可以加载类路径下的配置文件，要求配置文件必须在类路径下。不在的话，加载不了。(更常用)
       + FileSystemXmlApplicationContext：它可以加载磁盘任意路径下的配置文件(必须有访问权限）
       +  AnnotationConfigApplicationContext：它是用于读取注解创建容器。

     * 核心容器的两个接口引发出的问题：
      + ApplicationContext:     单例对象适用  多采用此接口  它在构建核心容器时，创建对象采取的策略是采用立即加载的方式。也就是说，只要一读取完配置文件马上就创建配置文件中配置的对象。

     *  BeanFactory:            多例对象使用   它在构建核心容器时，创建对象采取的策略是采用延迟加载的方式。也就是说，什么时候根据id获取对象了，什么时候才真正的创建对象。

---
##bean的配置
###把对象的创建交给spring来管理

#### spring对bean的管理细节

        1.创建bean的三种方式
        2.bean对象的作用范围
        3.bean对象的生命周期
   
### 创建Bean的三种方式
#### 第一种方式：使用默认构造函数创建。
      在spring的配置文件中使用bean标签，配以id和class属性之后，且没有其他属性和标签时。采用的就是默认构造函数创建bean对象。
      此时如果类中没有默认构造函数，则对象无法创建。
 
```
<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"></bean>
```

#### 第二种方式： 使用普通工厂中的方法创建对象（使用某个类中的方法创建对象，并存入spring容器）
```
    <bean id="instanceFactory" class="com.itheima.factory.InstanceFactory"></bean>
    <bean id="accountService" factory-bean="instanceFactory" factory-method="getAccountService"></bean>
```

    <!-- 第三种方式：使用工厂中的静态方法创建对象（使用某个类中的静态方法创建对象，并存入spring容器)
    <bean id="accountService" class="com.itheima.factory.StaticFactory" factory-method="getAccountService"></bean>
    -->

    <!-- bean的作用范围调整
        bean标签的scope属性：
            作用：用于指定bean的作用范围
            取值： 常用的就是单例的和多例的
                singleton：单例的（默认值）
                prototype：多例的
                request：作用于web应用的请求范围
                session：作用于web应用的会话范围
                global-session：作用于集群环境的会话范围（全局会话范围），当不是集群环境时，它就是session

    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl" scope="prototype"></bean>
    -->

    <!-- bean对象的生命周期
            单例对象
                出生：当容器创建时对象出生
                活着：只要容器还在，对象一直活着
                死亡：容器销毁，对象消亡
                总结：单例对象的生命周期和容器相同
            多例对象
                出生：当我们使用对象时spring框架为我们创建
                活着
                死亡
     -->


