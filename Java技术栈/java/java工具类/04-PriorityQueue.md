# PriorityQueue

> `PriorityQueue` 是在 JDK1.5 中被引入的, 其与 `Queue` 的区别在于元素出队顺序是与优先级相关的，即总是优先级最高的元素先出队。
>
> 这里列举其相关的一些要点：
>
> - `PriorityQueue` 利用了二叉堆的数据结构来实现的，底层使用可变长的数组来存储数据
> - `PriorityQueue` 通过堆元素的上浮和下沉，实现了在 O(logn) 的时间复杂度内插入元素和删除堆顶元素。
> - `PriorityQueue` 是非线程安全的，且不支持存储 `NULL` 和 `non-comparable` 的对象。
> - `PriorityQueue` 默认是小顶堆，但可以接收一个 `Comparator` 作为构造参数，从而来自定义元素优先级的先后。**元素升序排列，队头是最小元素**

## 重要方法

```java
// 自定义排序规则
public PriorityQueue(Comparator<? super E> comparator);
// 添加元素，也可以用add
public boolean offer(E e)；
// 获取队头元素
public E peek()；
// 移除队头元素并返回
public E poll()；
```

