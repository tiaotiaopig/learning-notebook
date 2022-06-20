# NumPy 简单入门

## Ndarray 对象

NumPy 最重要的一个特点是其 N 维数组对象 ndarray，它是一系列同类型数据的集合，以 0 下标为开始进行集合中元素的索引。

ndarray 对象是用于存放同类型元素的多维数组。

ndarray 中的每个元素在内存中都有相同存储大小的区域。

ndarray 内部由以下内容组成：

- 一个指向数据（内存或内存映射文件中的一块数据）的指针。
- 数据类型或 dtype，描述在数组中的固定大小值的格子。
- 一个表示数组形状（shape）的元组，表示各维度大小的元组。
- 一个跨度元组（stride），其中的整数指的是为了前进到当前维度下一个元素需要"跨过"的字节数。

创建一个 ndarray 只需调用 NumPy 的 array 函数即可：

```python
numpy.array(object, dtype = None, copy = True, order = None, subok = False, ndmin = 0)
```

**参数说明：**

| 名称   | 描述                                                      |
| :----- | :-------------------------------------------------------- |
| object | 数组或嵌套的数列                                          |
| dtype  | 数组元素的数据类型，可选                                  |
| copy   | 对象是否需要复制，可选                                    |
| order  | 创建数组的样式，C为行方向，F为列方向，A为任意方向（默认） |
| subok  | 默认返回一个与基类类型一致的数组                          |
| ndmin  | 指定生成数组的最小维度                                    |

**ndarray 对象由计算机内存的连续一维部分组成，并结合索引模式，将每个元素映射到内存块中的一个位置。内存块以行顺序(C样式)或列顺序(FORTRAN或MatLab风格，即前述的F样式)来保存元素。**

## 数组属性

本章节我们将来了解 NumPy 数组的一些基本属性。

NumPy 数组的维数称为秩（rank），秩就是轴的数量，即数组的维度，一维数组的秩为 1，二维数组的秩为 2，以此类推。

在 NumPy中，每一个线性的数组称为是一个轴（axis），也就是维度（dimensions）。比如说，二维数组相当于是两个一维数组，其中第一个一维数组中每个元素又是一个一维数组。所以一维数组就是 NumPy 中的轴（axis），第一个轴相当于是底层数组，第二个轴是底层数组里的数组。而轴的数量——秩，就是数组的维数。

很多时候可以声明 axis。axis=0，表示沿着第 0 轴进行操作，即对每一列进行操作；axis=1，表示沿着第1轴进行操作，即对每一行进行操作。

NumPy 的数组中比较重要 ndarray 对象属性有：

| 属性             | 说明                                                         |
| :--------------- | :----------------------------------------------------------- |
| ndarray.ndim     | 秩，即轴的数量或维度的数量                                   |
| ndarray.shape    | 数组的维度，对于矩阵，n 行 m 列                              |
| ndarray.size     | 数组元素的总个数，相当于 .shape 中 n*m 的值                  |
| ndarray.dtype    | ndarray 对象的元素类型                                       |
| ndarray.itemsize | ndarray 对象中每个元素的大小，以字节为单位                   |
| ndarray.flags    | ndarray 对象的内存信息                                       |
| ndarray.real     | ndarray元素的实部                                            |
| ndarray.imag     | ndarray 元素的虚部                                           |
| ndarray.data     | 包含实际数组元素的缓冲区，由于一般通过数组的索引获取元素，所以通常不需要使用这个属性。 |

### ndarray.ndim

ndarray.ndim 用于返回数组的维数，等于秩。

~~~python
import numpy as np
a = np.arange(24)
print (a.ndim) # a 现只有一个维度 输出１
# 现在调整其大小 
b = a.reshape(2,4,3)  # b 现在拥有三个维度 
print (b.ndim) # 输出　３
~~~

### ndarray.shape

ndarray.shape 表示数组的维度，返回一个**元组**，这个元组的长度就是维度的数目，即 ndim 属性(秩)。比如，一个二维数组，其维度表示"行数"和"列数"。

ndarray.shape 也可以用于调整数组大小。

```python
import numpy as np
a = np.array([[1,2,3],[4,5,6]])
print (a.shape)
```

输出结果为：

```
(2, 3)
```

调整数组大小。

~~~python
import numpy as np
a = np.array([[1,2,3],[4,5,6]])
a.shape =  (3,2) 
print (a)
~~~

输出结果为：

```python
[[1 2]
 [3 4]
 [5 6]]
```

NumPy 也提供了 reshape 函数来调整数组大小。(和直接对shape赋值效果相同)

~~~python
import numpy as np
a = np.array([[1,2,3],[4,5,6]])
b = a.reshape(3,2)
print (b)
~~~

输出结果为：

```python
[[1, 2] 
 [3, 4] 
 [5, 6]]
```

ndarray.reshape 通常返回的是非拷贝副本，即改变返回后数组的元素，原数组对应元素的值也会改变。

我的理解，数组在内存的存储不变，但是根据shape，使用不同的索引方式获取值

## 创建数组

### numpy.empty

numpy.empty 方法用来创建一个指定形状（shape）、数据类型（dtype）且未初始化的数组：

```python
numpy.empty(shape, dtype = float, order = 'C')
```

参数说明：

| 参数  | 描述                                                         |
| :---- | :----------------------------------------------------------- |
| shape | 数组形状                                                     |
| dtype | 数据类型，可选                                               |
| order | 有"C"和"F"两个选项,分别代表，行优先和列优先，在计算机内存中的存储元素的顺序。 |

下面是一个创建空数组的实例：

~~~python
import numpy as np
x = np.empty([3,2], dtype = int)
print (x)
~~~

输出结果为：

```
[[ 6917529027641081856  5764616291768666155]
 [ 6917529027641081859 -5764598754299804209]
 [          4497473538      844429428932120]]
```

**注意** − 数组元素为随机值，因为它们未初始化。

### numpy.zeros

创建指定大小的数组，数组元素以 0 来填充：

```python
numpy.zeros(shape, dtype = float, order = 'C')
```

### numpy.ones

创建指定形状的数组，数组元素以 1 来填充：

```python
numpy.ones(shape, dtype = None, order = 'C')
```

### numpy.randn

Numpy 创建标准正态分布数组：

```python
from numpy import *

# 创建 randn(size) 服从 X~N(0,1) 的正态分布随机数组
a=random.randn(2,3)
print(a)
```

输出结果为：

```python
array([[ 0.50203463,  1.48955265, -0.66236422],
       [ 0.44311407,  0.11144459, -0.13326862]])
```

### numpy.randint

Numpy 创建随机分布整数型数组。

利用 randint([low,high],size) 创建一个整数型指定范围在 [low.high] 之间的数组：

```python
from numpy import *

a=random.randint(100,200,(3,3))
print(a)
```

输出结果为：

```
array([[100, 154, 172],
       [149, 165, 184],
       [140, 140, 142]])
```

### numpy.arange

```python
import numpy as np

a = np.arange(10)
b = np.arange(10, 20)
c = np.arange(10, 20, 2)

print(a)
print(b)
print(c)
```

输出：

```
[0 1 2 3 4 5 6 7 8 9]  
[10 11 12 13 14 15 16 17 18 19]   
[10 12 14 16 18] 
```

### numpy.eye

eye 创建对角矩阵数组

```python
import numpy as np
a = np.eye(5)

print(a)
```

输出：

```python
[[1. 0. 0. 0. 0.]
 [0. 1. 0. 0. 0.]
 [0. 0. 1. 0. 0.]
 [0. 0. 0. 1. 0.]
 [0. 0. 0. 0. 1.]]
```

## 切片和索引

ndarray对象的内容可以通过索引或切片来访问和修改，与 Python 中 list 的切片操作一样。

ndarray 数组可以基于 0 - n 的下标进行索引，切片对象可以通过内置的 **slice 函数**，并设置 start, stop 及 step 参数进行，从原数组中切割出一个新数组。

```python
import numpy as np
a = np.arange(10)
s = slice(2,7,2)   # 从索引 2 开始到索引 7 停止，间隔为2 
print (a[s])
```

输出结果为：

```
[2  4  6]
```

以上实例中，我们首先通过 arange() 函数创建 ndarray 对象。 然后，分别设置起始，终止和步长的参数为 2，7 和 2。

我们也可以通过冒号分隔切片参数 **start:stop:step** 来进行切片操作：

```python
import numpy as np
a = np.arange(10)
b = a[2:7:2]   # 从索引 2 开始到索引 7 停止，间隔为 2 
print(b)
```

输出结果为：

```
[2  4  6]
```

冒号 **:** 的解释：

+ 如果只放置一个参数，如 **[2]**，将返回与该索引相对应的单个元素。
+ 如果为 **[2:]**，表示从该索引开始以后的所有项都将被提取。
+ 如果使用了两个参数，如 **[2:7]**，那么则提取两个索引(不包括停止索引)之间的项。

~~~python
import numpy as np
a = np.arange(10)  # [0 1 2 3 4 5 6 7 8 9] b = a[5]  
print(b)
~~~

输出结果为：

```
5
```

~~~python
import numpy as np
a = np.arange(10)
print(a[2:])
~~~

输出结果为：

```
[2  3  4  5  6  7  8  9]
```

~~~python
import numpy as np
a = np.arange(10)  # [0 1 2 3 4 5 6 7 8 9] 
print(a[2:5])
~~~

输出结果为：

```
[2  3  4]
```

多维数组同样适用上述索引提取方法：

```python
import numpy as np 
a = np.array([[1,2,3],[3,4,5],[4,5,6]]) 
print(a) # 从某个索引处开始切割 print('从数组索引 a[1:] 处开始切割') 
print(a[1:])
```

输出结果为：

```
[[1 2 3]
 [3 4 5]
 [4 5 6]]
从数组索引 a[1:] 处开始切割
[[3 4 5]
 [4 5 6]]
```

切片还可以包括省略号 **…**，来使选择元组的长度与数组的维度相同。 如果在行位置使用省略号，它将返回包含行中元素的 ndarray。

个人理解：冒号 **start : end**就是切片**[start, end)**,省略号就是 **0 : length**切片取全部，**使用逗号,表示对第几个维度进行切片**

```python
import numpy as np  
a = np.array([[1,2,3],[3,4,5],[4,5,6]])   
print (a[...,1])   # 第2列元素 
print (a[:, 1]) # 和上面等同
print (a[1,...])   # 第2行元素 
print (a[...,1:])  # 第2列及剩下的所有元素
```

输出结果为：

```
[2 4 5]
[3 4 5]
[[2 3]
 [4 5]
 [5 6]]
```

在多维数组的切片中，使用 **,** 区分维数。

```python
import numpy as np
  
a=np.arange(0,12)
a.shape=(3,4)
print(a)
print(a[0:2,1:3])
```

输出结果：

```
[[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]]
[[1 2]
 [5 6]]
```