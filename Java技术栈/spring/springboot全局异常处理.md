# springboot全局异常处理

本篇要点如下

1. 介绍Spring Boot默认的异常处理机制
2. 如何自定义错误页面
3. 通过@ControllerAdvice注解来处理异常
---
## spring boot默认的异常处理机制

    默认情况下，Spring Boot为两种情况提供了不同的响应方式。

    一种是浏览器客户端请求一个不存在的页面或服务端处理发生异常时，一般情
    况下浏览器默认发送的请求头中Accept: text/html，所以Spring Boot
    默认会响应一个html文档内容，称作“Whitelabel Error Page”。

    地址:https://www.jianshu.com/p/accec85b4039