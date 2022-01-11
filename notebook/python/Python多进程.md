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
    // 必须要get下，这是懒加载
    asy_res.get()
```

下面的源码，可以看出，已经做了解包

```python
def starmap(function, iterable):
    # starmap(pow, [(2,5), (3,2), (10,3)]) --> 32 9 1000
    for args in iterable:
        yield function(*args)
```

## 多线程

> ​	所谓的多线程有点类似cpu调度算法的时间片轮转，也即一会儿执行一下这个方法，一会儿执行另一个线程的方法，还是只用到了一个核，而且考虑到上下文的切换，并不能加速。在计算密集型的任务中，使用多进程反而会拖慢速度。而在IO密集型任务中，cpu大部分时间都在等待，这时多线程就有意义。

### 死锁

```python
import time
def wait_on_b():
    time.sleep(5)
    print(b.result())  # b will never complete because it is waiting on a.
    return 5

def wait_on_a():
    time.sleep(5)
    print(a.result())  # a will never complete because it is waiting on b.
    return 6

executor = ThreadPoolExecutor(max_workers=2)
a = executor.submit(wait_on_b)
b = executor.submit(wait_on_a)
```

两个线程，都需要对方先完成，然后相互等待，造成死锁

```python
def wait_on_future():
    f = executor.submit(pow, 5, 2)
    # This will never complete because there is only one worker thread and
    # it is executing this function.
    print(f.result())

executor = ThreadPoolExecutor(max_workers=1)
executor.submit(wait_on_future)
```

只有一个线程，导致等待的任务没有线程去完成，产生死锁

### 示例

好好琢磨一下，官方的写法

```python
import concurrent.futures
import urllib.request

URLS = ['http://www.foxnews.com/',
        'http://www.cnn.com/',
        'http://europe.wsj.com/',
        'http://www.bbc.co.uk/',
        'http://some-made-up-domain.com/']

# Retrieve a single page and report the URL and contents
def load_url(url, timeout):
    with urllib.request.urlopen(url, timeout=timeout) as conn:
        return conn.read()

# We can use a with statement to ensure threads are cleaned up promptly
with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
    # Start the load operations and mark each future with its URL
    future_to_url = {executor.submit(load_url, url, 60): url for url in URLS}
    for future in concurrent.futures.as_completed(future_to_url):
        url = future_to_url[future]
        try:
            data = future.result()
        except Exception as exc:
            print('%r generated an exception: %s' % (url, exc))
        else:
            print('%r page is %d bytes' % (url, len(data)))
```

