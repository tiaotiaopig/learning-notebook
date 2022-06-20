# HashMap总结

## 扩容

HashMap中**size**表示当前共有多少个KV对，**capacity**表示当前HashMap的容量是多少，默认值是16，每次扩容都是**成倍**的。loadFactor是装载因子，当Map中元素个数超过`loadFactor* capacity`的值时，会触发扩容。`loadFactor* capacity`可以用**threshold**表示。

默认情况下HashMap的容量是16，但是，如果用户通过构造函数指定了一个数字作为容量，那么Hash会选择大于该数字的第一个2的幂作为容量。(1->1、7->8、9->16)

在初始化HashMap的时候，应该尽量指定其大小。尤其是当你已知map中存放的元素个数时。

loadFactor是装载因子，表示HashMap满的程度，默认值为0.75f，设置成0.75有一个好处，那就是0.75正好是3/4，而capacity又是2的幂。所以，两个数的乘积都是整数。

对于一个默认的HashMap来说，默认情况下，当其size大于12(16*0.75)时就会触发扩容。

