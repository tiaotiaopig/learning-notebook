# 类型声明

> python是动态编程语言,不需要对变量进行类型声明,一个变量也可以被赋值为不同类型.
>
> Java是静态编程语言,必须对变量类型进行声明.

## 类型提示(type hints)

> python3.5引入了"类型声明"的特性,官方叫做类型提示"**type hints**"
>
> 但是函数本身并不会对输入参数的类型进行检查,传入不匹配的类型,python编译器也不会报错
>
> 优点: 方便**后期维护**以及**IDE的语法提示**
>
> `add(a: int=2, b: int=2) -> int`
>
> 类型提示 + 默认值

## 变量注解(variable annotations)

> python3.6又引入了变量注解功能(variable annotations),就是上面所说的默认值

## 类型

简单类型:`int`, `float`, `bool`, `bytes`, `str`

嵌套类型,有些容器类型内部可能还包含其他类型,可以使用`typing`标准库来声明这些类型.

```python
from typing import List

# 列表
add(add_list: List[int]) -> int:

# 元组
Tuple[int, str, int]

# 集合
Set[int]

# 字典
Dict[str, int]

# 可选类型,使用自定义类型,可无,若有则为Person
Optional[Person] = None

# 声明是类型中的一种
Union[int, str, List[int]]

# Any 是任意类型

# Callable 可调用类型,一般lambda表达式
```

![img](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/v2-66a94b4d4ba1a2a6befc713c1f568030_r.jpg)