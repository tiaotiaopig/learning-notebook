# Collection

![image-20220825161853455](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/image-20220825161853455.png)

上图就是Collection接口的实现关系。

> 比较常用的是ArrayList, ArrayDeque, PriorityQueue, HashSet

## Arrays 和 Collections

### Arrays

**数组工具类**，操作的是数组，不是列表，每个功能下有一堆重载方法（针对各种数据类型）

```java
// 数组填充
public static void fill(int[] a, int val)；
public static void fill(int[] a, int fromIndex, int toIndex, int val)；
    
// 升序排序,没有提供降序的方法
public static void sort(int[] a)；
public static void sort(int[] a, int fromInde, int toIndex)；
// 引用类型可以指定顺序
public static <T> void sort(T[] a, Comparator<? super T> c)；
    
// 快捷创建列表
public static <T> List<T> asList(T... a);

// 从一个数组复制到另一个数组
System.arraycopy(Object src, int srcPos, Object dest, int destPos, int length);
// 复制，指定长度，不足则截取
public static int[] copyOf(int[] original, int newLength)；
    
// 复制，指定范围
public static int[] copyOfRange(int[] original, int from, int to)；

// 比较数组
public static boolean equals(int[] a, int[] a2)；

// 中括号
public static String toString(int[] a)；
```

### Collections

## ArrayList

### 构造方法

```java
// 指定初始容量
public ArrayList(int initialCapacity);
// 常用，默认容量10
public ArrayList()；
// 深拷贝
public ArrayList(Collection<? extends E> c)；
```

### 常用方法

1. 添加

   ```java
   // 插入元素到列表尾部
   public boolean add(E e)；
       
   // 指定索引处插入元素，需要移动后面的元素
   public void add(int index, E element)；
       
   // 添加所有
   public boolean addAll(Collection<? extends E> c)；
   ```

2. 查询

   ```java
   // 列表大小
   public int size()；
   
   // 是否为空
   public boolean isEmpty()；
       
   // 是否包含某个元素
   public boolean contains(Object o);
   
   // 获取某个索引下的元素，索引越界会抛异常
   public E get(int index)；
   
   // 替换指定位置的元素
   public E set(int index, E element)；
       
   // 第一次出现的索引
   public int indexOf(Object o)；
   ```

3. 删除

   ```java
   // 移除指定位置的元素，有元素移动,O(n)
   public E remove(int index);
   
   public void clear();
   ```

4. 其他

   ```java
   // 列表排序
   public void sort(Comparator<? super E> c);
   
   // 列表遍历
   public void forEach(Consumer<? super E> action)；
       
   // 转数组
   public <T> T[] toArray(T[] a)；
   ```

   