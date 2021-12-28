# Python多进程

## 说明

> **IO**密集型的任务，使用**多线程**
>
> **计算**密集型的任务，使用**多进程**
>
> 单个任务较为耗时，加速效果明显，不然还不如单线程

## 多进程

> apply两个方法不好用，返回的结果不是顺序的
>
> map和starmap两个方法接收参数列表，返回结果是有序的
>
> 但是map只能接收一个参数，需要自己写一个函数完成解包和打包
>
> starmap就很好，将参数组装成元组列表的形式，就可以直接调用

### apply

参数组需要一个一个传入，结果也是一个一个出来，不好用

```python
with mp.Pool(mp.cpu_count() // 4) as pool:
        params = [(num, 2) for num in range(total)]
        for param in params:
            res = pool.apply_async(add, param)
            res.get() # 最大败笔，直接堵塞，还不如单进程，应该放外边
```

### map

只能接收一个参数，对于参数组，我们需要自己手动解包，不然就会报缺少参数异常

```python
# 手动解包
def add_worker(x):
    return add(*x)

with mp.Pool(mp.cpu_count() // 4) as pool:
    params = [(num, 2) for num in range(total)]
    asy_res = pool.map_async(add_worker, params)
    asy_res.get()
```

### starmap

这个就很好，不用自己再写一个解包的函数啦

```python
import multiprocessing as mp

def add(x: int, y: int) -> int:
    time.sleep(0.1)
    return x ** y

with mp.Pool(mp.cpu_count() // 4) as pool:
    params = [(num, 2) for num in range(total)]
    asy_res = pool.starmap_async(add, params)
    asy_res.get()
```

下面的源码，可以看出，已经做了解包

```python
def starmap(function, iterable):
    # starmap(pow, [(2,5), (3,2), (10,3)]) --> 32 9 1000
    for args in iterable:
        yield function(*args)
```

