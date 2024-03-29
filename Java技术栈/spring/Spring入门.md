# Spring入门

## 认识Spring

+ spring使用JavaBean或者bean来表示应用组件,但是spring组件不一定遵循JavaBean规范,它可以是任何形式的POJO(plain old java object).

+ spring的根本目标:简化java开发
    
     1. 基于POJO的轻量级和最小侵入性编程;
     2. 通过依赖注入和面向接口实现松耦合；
     3. 基于切面和惯例进行声明式编程；
     4. 通过切面和模板减少样板式代码.

+ **依赖注入**:将对象的创建交给容器来管理,降低程序耦合

+ **应用上下文**:创建应用组件之间协作的行为通常称为**装配**.spring有很多装配bean的方式.(xml是其中一种方式).Spring通过应用上下文（Application Context）装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。Spring自带了多种应用上下文的实现，它们之间主要的区别仅仅在于如何加载配置。

+ 示例:用ClassPathXmlApplicationContext加载knights.xml，并获得Knight对象的引用。

![依赖注入](md引用的图片/依赖注入说明.png)

+ **面向切面**:（aspect-oriented programming，AOP）允许你把遍布应用各处的功能分离出来形成可重用的组件。系统由许多不同的组件组成，每一个组件各负责一块特定功能。除了实现自身核心的功能之外，这些组件还经常承担着额外的职责。诸如日志、事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的组件中去，这些系统服务通常被称为横切关注点，因为它们会跨越系统的多个组件。我们可以把切面想象为覆盖在很多组件之上的一个外壳。应用是由那些实现各自业务功能的模块组成的。借助AOP，可以使用各种功能层去包裹核心业务层。这些层以声明的方式灵活地应用到系统中，你的核心应用甚至根本不知道它们的存在。这是一个非常强大的理念，可以将安全、事务和日志关注点与核心业务逻辑相分离.