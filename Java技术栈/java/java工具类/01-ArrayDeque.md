# ArrayDeque

> 可以用作双向队列，队列，栈
>
> 可以只记住六种操作就行
>
> 还有**size()** 和 **isEmpty()**方法

## 双向队列

![image-20220825173312494](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/image-20220825173312494.png)

记住三个单词：**offer, poll, peek**, 或者 **add, get, remove**

## 队列

![image-20220825173755171](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/image-20220825173755171.png)

队列操作：插入到队尾，从队头删除

## 栈

![image-20220825174007763](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/image-20220825174007763.png)

## 总结

### Queue 与 Deque 的区别

`Queue` 是单端队列，只能从一端插入元素，另一端删除元素，实现上一般遵循 **先进先出（FIFO）** 规则。

`Queue` 扩展了 `Collection` 的接口，根据 **因为容量问题而导致操作失败后处理方式的不同** 可以分为两类方法: 一种在操作失败后会抛出异常，另一种则会返回特殊值。

| `Queue` 接口 | 抛出异常  | 返回特殊值 |
| ------------ | --------- | ---------- |
| 插入队尾     | add(E e)  | offer(E e) |
| 删除队首     | remove()  | poll()     |
| 查询队首元素 | element() | peek()     |

`Deque` 是双端队列，在队列的两端均可以插入或删除元素。

`Deque` 扩展了 `Queue` 的接口, 增加了在队首和队尾进行插入和删除的方法，同样根据失败后处理方式的不同分为两类：

| `Deque` 接口 | 抛出异常      | 返回特殊值      |
| ------------ | ------------- | --------------- |
| 插入队首     | addFirst(E e) | offerFirst(E e) |
| 插入队尾     | addLast(E e)  | offerLast(E e)  |
| 删除队首     | removeFirst() | pollFirst()     |
| 删除队尾     | removeLast()  | pollLast()      |
| 查询队首元素 | getFirst()    | peekFirst()     |
| 查询队尾元素 | getLast()     | peekLast()      |

事实上，`Deque` 还提供有 `push()` 和 `pop()` 等其他方法，可用于模拟栈。