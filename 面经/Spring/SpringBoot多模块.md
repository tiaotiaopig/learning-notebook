# SpringBoot 多模块

> 优点:方便重用,某个模块可以单独构建,降低耦合

## 搭建过程

> 利用**IDEA**配合**Spring Initializr**,搭建过程非常方便

### 创建父工程

> 1. 父工程主要是容纳子工程的,所以`src`文件夹可以删去
> 2. 父POM可以放各个子模块的公共依赖(我感觉这样不好,虽然更加简洁,但是有耦合)
> 3. 删除`<build>...</build>`,单独留一个启动模块
> 4. 添加`<modules>...</modules>`,指定包含哪些子模块

### 创建子模块

在子模块BOM的`<parent>中填写父模块的groupId,artifactId和version`

```xml
    <parent>
        <groupId>edu.scu</groupId>
        <artifactId>multimodule</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>
```

没有Application启动类的话,把build也删除