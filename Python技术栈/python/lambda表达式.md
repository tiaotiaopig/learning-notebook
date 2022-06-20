# python lambda 表达式

> 应用场景：lambda表达式是一种匿名函数，广泛用于函数式编程的场景下（说人话，有些函数的参数输入是函数名不带参数，这时就可以直接使用lambda），代码会显得简洁

## lambda语法

```python
# lambda x: x * x
lambda argument_list: expression
```

语法中的**expression**是一个关于参数的表达式，表达式中出现的参数需要在**argument_list**中有定义，并且表达式只能是单行的

## 应用示例

```python
# [3,6]
fliter(lambda x:x%3==0,[1,2,3,4,5,6])
# [0,1,4,9,16]
squares = map(lambda x:x**2,range(5)

a=[('b',3),('a',2),('d',4),('c',1)]
sorted(a,key=lambda x:x[0])
# [('a',2),('b',3),('c',1),('d',4)]
sorted(a,key=lambda x:x[1])
# [('c',1),('a',2),('b',3),('d',4)]
# lambda表达式也可以有默认参数
x=(lambda x='Boo',y='Too',z='Z00'：x+y+z)
print(x('Foo'))
# 'FooTooZoo'
```

求两个列表元素的和

```python
a = [1,2,3,4]
b = [5,6,7,8]
print(list(map(lambda x,y:x+y, a,b)))

[6,8,10,12]
```

求字符串每个单词的长度

```python
sentence = "Welcome To Beijing!"
words = sentence.split()
lengths  = map(lambda x:len(x),words)
print(list(lengths))
[7,2,8]
```

判断字符串是否以某个字母开头有

```python
Names = ['Anne', 'Amy', 'Bob', 'David', 'Carrie', 'Barbara', 'Zach']
B_Name= filter(lambda x: x.startswith('B'),Names)
print(B_Name)

['Bob', 'Barbara']
```

