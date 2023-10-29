# Python 小技巧

## enumerate

> for 循环获取列表的索引和元素

```python
data = [1, 2, 3, 4, 7, 9]

for index, value in enumerate(data):
    print(f'{index}: {value}')
```

## 列表推导

简洁方式生成新列表，也可以由元组推导（迭代器，懒加载），字典推导

```python
filtered_list = [x for x in data if x % 2 == 0]
```

## 容器排序

```python
# 容器排序
sort_list = sorted(data, reverse=True)
print(sort_list)

dict_data = [
    {"name" : "jia", "age" : 18},
    {"name" : "yi", "age" : 60},
    {"name" : "bing", "age" : 20}
]
new_data = sorted(dict_data, key=lambda x: x["age"])
print(new_data)
```

## 集合去重

```python
# 集合操作,去重
set_data = set(data)
print(set_data)

data_1 = {'Mathematics', 'Chinese', 'English', 'Physics', 'Chemistry', 'Biology'}
data_2 = {'Mathematics', 'Chinese', 'English', 'Politics', 'Geography', 'History'}

# 交集
print(data_1 & data_2)

# 并集
print(data_1 | data_2)

# 差集
print(data_1 - data_2)
```

## 迭代器

```python
# 迭代器，提升效率
data_iter = (index for index in range(100000))
```

## 字典使用get

```python
# 字典使用get方法获取元素，防止报错
dict_example = {'age': 18, 'name': 'xiaoming'}
print(dict_example.get('key'))
dict_example.setdefault('key', 256)
```

## 字符串分隔

```python
# 字符串分隔
str_list = ['hello', 'world', 'man']
print(' '.join(str_list))
```

