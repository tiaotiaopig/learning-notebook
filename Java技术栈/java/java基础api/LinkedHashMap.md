# LinkedHashMap

> 它是一个hashtable和linkedlist实现的map，内部的键值对（entry）具有**确定的迭代顺序**，使用linkedlist存储迭代顺序，默认的是**插入顺序**（insertion-order），一个键的**再次插入**并不会改变插入顺序。
>
> Map接口的哈希表和双向列表实现，具有可预测的迭代顺序。这个实现与HashMap的不同之处在于，它维护了一个贯穿所有条目(entry,键值对)的双链表。这个链表定义了迭代顺序，通常是键被插入Map的顺序（插入顺序）。注意，如果一个键被重新插入到Map中，插入顺序不会受到影响。(如果m.put(k, v)被调用，而m.containsKey(k)在调用之前会返回true，那么一个键k就会被重新插入到Mapm中。)
>
> 这个实现使其客户避免了HashMap（和Hashtable）所提供的不明确的、通常是混乱的排序，而不会产生与TreeMap相关的增加的成本。它可以用来产生一个Map的副本，该副本的顺序与原Map相同，而不管原Map的实现如何。
>
> ```java
>  void foo(Map m) {
>      Map copy = new LinkedHashMap(m);
>      ...
>  }
> ```
>
> 如果一个模块在输入时接受了一个Map，并将其复制，随后返回的结果的顺序是由复制的顺序决定的，那么这种技术就特别有用。(客户一般都喜欢以相同的顺序返回东西)。
> 我们提供了一个特殊的构造函数来创建一个LinkeHashMap，其迭代顺序是其条目最后被访问的顺序，从最近被访问的最少的到最近被访问的（*access-order*）。这种Map非常适用于建立**LRU缓存**。调用put、putIfAbsent、get、getOrDefault、compute、computeIfAbsent、computeIfPresent或merge方法会导致对相应条目的访问（假设在调用完成后它存在）。**replace**方法只有在**值被替换**时才会导致对条目的访问。putAll方法为指定Map中的每个映射生成一个条目访问，其顺序是键值映射由指定Map的条目集迭代器提供。其他方法不产生入口访问。特别是，对集合-视图的操作不影响支持Map的迭代顺序。
>
> **removeEldestEntry(Map.Entry)**方法可以被重载，以便在新的映射被添加到Map中时，施加一个自动删除陈旧映射的策略，**继承**LinkedHashMap再**重载**这个方法，然后使用访问顺序，就可以实现**LRU缓存**
>
> ```java
> protected boolean removeEldestEntry(Map.Entry eldest) {
> 	return size() > 100;
> }
> ```
>
> 这个类提供了所有可选的Map操作，并允许空元素。像HashMap一样，它为基本操作（添加、包含和删除）提供了恒定的时间性能，假设散列函数在桶中正确地分散了元素。由于维护链表的额外费用，性能可能略低于HashMap，但有一个例外。对LinkedHashMap的集合视图进行迭代需要的时间与Map的大小成正比，而不考虑其容量。对HashMap的迭代可能更加昂贵，需要的时间与它的容量成正比。
>
> 一个LinkeHashMap有两个影响其性能的参数：**初始容量**和**负载因子**。它们的定义与HashMap一样精确。但是请注意，对于这个类来说，为初始容量选择一个过高的值所带来的惩罚比HashMap要轻，因为这个类的迭代时间不受容量的影响。
>
> 请注意，这个实现是不同步的。如果多个线程同时访问一个链接的哈希图，并且至少有一个线程在结构上修改了该图，那么它必须在外部进行同步。这通常是通过在一些自然封装了Map的对象上进行同步来实现的。如果没有这样的对象，则应使用Collections.synchronizedMap方法将Map "包裹 "起来。这最好在创建时完成，以防止意外地对Map进行非同步访问。
>
>    Map m = Collections.synchronizedMap(new LinkedHashMap(...))。
> **结构性修改**是指增加或删除一个或多个映射的任何操作，或者在访问排序的LinkeHashMap中，**影响迭代顺序**。在插入排序的LinkeHashMap中，仅仅改变与已经包含在Map中的键相关的值，并不是结构性修改。在存取有序的LinkeHashMap中，仅仅用get查询Map就是一种结构修改。)
> 这个类的所有集合视图方法的迭代器方法返回的迭代器是快速失败的：如果Map在迭代器创建后的任何时候被结构化修改，除了通过迭代器自己的移除方法，迭代器将抛出一个ConcurrentModificationException。因此，在面对并发修改时，迭代器会快速而干净地失败，而不是在未来某个不确定的时间冒着任意的、不确定的行为。