# final 和 static

> Java的类加载机制。Jvm会在准备阶段为类的静态变量分配内存，并将其初始化为默认值。然后在初始化阶段，对类的静态变量，静态代码块执行初始化操作。

![Java类加载机制](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/007S8ZIlly1gi5eqpkg4cj312m0dyjvn.jpg)

## 内联优化

我们先看直接声明就初始化这种情况：

```
class A {
    private final static int a = 7;
    public static int getA() {
        return a;
    }
}
```

编译再反编译一下：

```
# 我的文件名是Demo.java
javac Demo.java
javap -p -v -c A
```

可以看到这个`getA()`的反编译结果，编译器已经知道了`a=7`，并且由于它是一个final变量，不会变，所以直接写死编译进去了。相当于直接把`return a`替换成了`return 7`。

```
  public static int getA();
    descriptor: ()I
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=1, locals=0, args_size=0
         0: bipush        7
         2: ireturn
      LineNumberTable:
        line 21: 0
```

这其实是一个编译器的优化，专业的称呼叫“内联优化”。其实不只是final变量会被内联优化。一个方法也有可能被内联优化，特别是热点方法。JIT大部分的优化都是在内联的基础上进行的，方法内联是即时编译器中非常重要的一环。

一般来说，内联的方法越多，生成代码的执行效率越高。但是对于即时编译器来说，内联的方法越多，编译时间也就越长，程序达到峰值性能的时刻也就比较晚。有一些参数可以控制方法是否被内联.

## 实例

```
class Config {
    public static Config config = Config.getInstance();
    private static Config instance = new Config();

    public static Config getInstance() {
        return instance;
    }
}

public class Demo {
    public static void main(String[] args) {
        System.out.println(Config.config); // null
        System.out.println(Config.getInstance()); // 有值
    }
}
```

这段代码很好解释，我们在对`Config`类进行初始化的时候，先执行第一行代码，由于第二行代码还没执行，所以这个时候`Config.getInstance()`返回的是`null`，写进了这个静态变量里。所以无论你后面怎么调用`Config.config`这个变量，它都会是`null`。

> 由于内联优化，config直接被写死为null,后续虽然instance被初始化，返回仍然为空。
>
> 颠倒下两个定义的位置就可以啦