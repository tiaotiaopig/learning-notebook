# volatile

`volatile`是一个变量修饰符，只能用来修饰变量。无法修饰方法及代码块等。

`volatile`可以保证**可见性**和**有序性**，无法保证原子性。

## 可见性

可见性主要由以下两条规则保证：

> 1. 在工作内存中，每次使用V前都必须先从主内存刷新最新的值，用于保证能看见其他线程对变量V所做的修改。
> 2. 在工作内存中，每次修改V后都必须立刻同步回主内存中，用于保证其他线程可以看到自己对变量V所做的修改。

`volatile`的特殊规则保证了新值能立即同步到主内存，以及每次使用前立即从主内存刷新。  

## 有序性

volatile修饰的变量不会被指令重排序优化， 从而保证代码的执行顺序与程序的顺序相同。