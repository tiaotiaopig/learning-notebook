# **identityHashCode与偏向锁**

> synchronized的锁是借助对象头中的Mark Word 字段实现的。通过CAS操作设置这个字段
>
> Mark Word 占用一个字长，主要记录**identityHashcode**和分代年龄，对象的HashCode值不存在这里。
>
> identityHashcode根据对象的内存地址返回哈希值。

## hashCode

我们知道在Java中，一切对象都继承自`java.lang.Object`类。这个类中有一个可继承的方法叫`hashCode()`。它在`Object`类中的方法签名是这样的：

```java
public native int hashCode();
```

可以看到，如果一个对象不覆盖这个方法，那它会继承`Object`类的实现，是一个`native`的方法。这个时候，它会根据**对象的内存地址**返回哈希值。

所以我们运行下面这段代码会输出`false`:

```java
public class HashCodeDemo {
    public static void main(String[] args) {
        Object objectA = new Object();
        Object objectB = new Object();
        System.out.println(objectA.hashCode() == objectB.hashCode());
    }
}
```

有些对象需要根据**对象的字段的内容**来计算hash值，比如字符串`String`。本文不介绍如何复写一个`hashCode()`方法，有兴趣的可以自己去学习一下。

因为复写了`hashCode()`方法，所以以下代码会输出`true`：

```java
public class HashCodeDemo {
    public static void main(String[] args) {
        String s1 = "yasin shaw";
        String s2 = "yasin shaw";
        System.out.println(s1.hashCode() == s2.hashCode());
    }
}
```

## identityHashCode

那如果一个对象覆盖了`hashCode`方法，我们仍然想获得它的内存地址计算的Hash值，应该怎么办呢？

`java.lang.System`类提供了一个静态方法：

```java
public static native int identityHashCode(Object x);
```

这里我们顺便涉及一下字符串的知识：

```java
public class HashCodeDemo {
    public static void main(String[] args) {
        String s1 = "yasin shaw";
        String s2 = "yasin shaw";
        System.out.println(s1.hashCode() == s2.hashCode());
        System.out.println(System.identityHashCode(s1) == System.identityHashCode(s2)); 

        String s3 = new String("yasin shaw");
        String s4 = new String("yasin shaw");
        System.out.println(s3.hashCode() == s4.hashCode());
        System.out.println(System.identityHashCode(s3) == System.identityHashCode(s4)); 
    }
}

// 输出：
true
true
true
false
```

可以看到，s1, s2是在**常量池**里面的，所以它们的内存地址也会相等，所以调用`identityHashCode`方法会返回`true`。但s3, s4是在**堆**里面的，所以调用`identityHashCode`方法会返回`false`。

## 与偏向锁的关系？

通常情况下，我们称”**以内存计算的HashCode的方式**“为“**identity hash code**”。所以其实未覆盖`Object`类的`hashCode()`方法也被称为“identity hash code”。

一个类被加载的时候，`hashCode`是被存放在**对象头**里面的**Mark Word**里面的。在32位的JVM中，它会占25位；在64位的JVM中，它会占31位。

需要注意的是：这里说的hashCode仅仅指的是identity hash code。如果不是identity hash code，那它不会存储在对象头里。

每个Java对象都有对象头。如果是非数组类型，则用2个**字宽**来存储对象头，如果是数组，则会用3个字宽来存储对象头。在32位虚拟机中，一个字宽是32位；在64位虚拟机中，一个字宽是64位。对象头的内容如下表：

| 长度     | 内容                   | 说明                         |
| :------- | :--------------------- | :--------------------------- |
| 32/64bit | Mark Word              | 存储对象的hashCode或锁信息等 |
| 32/64bit | Class Metadata Address | 存储到对象类型数据的指针     |
| 32/64bit | Array length           | 数组的长度（如果是数组）     |

再来看看Mark Word的结构(无锁状态)：

32位：

| 25 bit   | 4 bit        | 1 bit        | 2 bit    |
| :------- | :----------- | :----------- | :------- |
| hashCode | 对象分代年龄 | 是否是偏向锁 | 锁标志位 |

64位：

| 25 bit | 31 bit   | 1 bit    | 4 bit        | 1 bit        | 2 bit    |
| :----- | :------- | :------- | :----------- | :----------- | :------- |
| 未使用 | hashCode | cms_free | 对象分代年龄 | 是否是偏向锁 | 锁标志位 |

注意，这是“**无锁状态**”下。那如果有锁状态怎么办呢？我们知道，Java 6 以后，锁有三种，级别由低到高分别是：**偏向锁、轻量级锁、重量级锁**。

其中，轻量级锁和重量级锁都会在线程的栈里面创建一块专门的空间**Displaced Mark Word**，用于在获得锁的时候，复制“锁”的对象头里面的Mark Word内容，把当前的线程ID写进Mark Word；而在释放锁的时候，再从Displaced Mark Word复制回锁的Mark Word里面。

那偏向锁怎么办呢？

当一个对象已经计算过identity hash code，它就无法进入偏向锁状态；当一个对象当前正处于偏向锁状态，并且需要计算其identity hash code的话，则它的偏向锁会被撤销，并且锁会**膨胀为重量级锁**；

那什么时候对象会计算identity hash code呢？当然是当你调用未覆盖的Object.hashCode()方法或者System.identityHashCode(Object o)时候了。