# python 简洁

> python 中有很多优雅的写法，现总结归纳如下：
>
> 1. labmda表达式
> 2. 列表推导
> 3. 枚举
> 4. 三元运算符

## lambda表达式

应用的场景多在是流处理中，本质上就是一种匿名函数，详情参看相关笔记

## 列表推导

一行写完一个循环，并返回一个列表

推导式就是从一个数据序列构建出另一个数据序列

```python
# 列表推导模板
res_list = [expression(argument) for argument in init_list]
res_list = [elem + 2 for elem in init_list]
# map或者filter,可用lambda表达式
res_list = list(map(func, iterable))
res_list = filter(func, iterable)
# 这个示例很好
res_list = list(map(lambda x: x * x, filter(lambda x: x > 5, init_list)))
# 字典推导
res_dict = {expression(key, value): for argument in init_list}
res_dict = {i: i*i for i in range(100)}
# 集合推导
res_set = {expression(argument): for argument in init_list}
# 迭代器推导
res_iter = (expression(argument): for argument in init_list)
```

> for 循环的对象只要是可迭代的对象即可，不一定非要列表
>
> 使用`yield`返回结果的函数可以成为生成器，其返回的结果是一个迭代器，一个一个取，有点懒加载

## 枚举

python的for循环只有元素没有索引，自己计算索引又很丑陋，所以就有了`enumerate`内置函数

```python
for index, elem in enumerate(init_list):
    print(f'{index}-{elem}')
```

## 三元运算符

和 ？ ：很类似

```python
device = 'cuda' if torch.cuda_is_avaliable() else 'cp'
```

