# final

`final`变量可以修改变量和方法，只能保证可见性。

> 1. `final`可以用来修饰变量，此变量被初始化后，无法修改，从而保证了可见性。（PS：常量天生就具备可见性）。
> 2. 被`final`修饰的字段在构造器中一旦被初始化完成，并且构造器没有把“this”的引用传递出去（this引用逃逸是一件很危险的事情， 其他线程有可能通过这个引用访问到“初始化了一半”的对象)，就完成了初始化
> 3. 被`final`修饰的方法，无法被继承