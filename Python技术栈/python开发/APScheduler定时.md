# APScheduler 定时

## 安装

1. 推荐使用pip安装：`pip install apscheduler`
2. 国内源好像没有，使用官方源：`pip install APScheduler -i https://pypi.org/simple`

## 简单示例

```python
from apscheduler.schedulers.blocking import BlockingScheduler

def job_function():
    print("定时任务执行")

# 创建调度器
scheduler = BlockingScheduler()

# 添加定时任务，每隔 5 秒执行一次 job_function
scheduler.add_job(job_function, 'interval', seconds=5)

# 启动调度器
scheduler.start()
```

> 在这个示例中，我们首先导入 `BlockingScheduler` 类，并定义了一个名为 `job_function` 的函数作为定时任务。然后，我们创建了一个调度器对象 `scheduler`，并使用 `add_job` 方法添加了一个每隔 5 秒执行一次的定时任务。最后，通过调用 `start` 方法启动调度器。

1. 顾名思义，该种调度器是阻塞的，会一直运行
2. `interval`指定了触发器种类，总共有三种：
   + `interval`间隔；
   + `date`某个时间点运行一次
   + `cron`一天内某个时间周期运行

## 非阻塞示例

```python
from apscheduler.schedulers.background import BackgroundScheduler

def job_function():
    print("定时任务执行")

# 创建后台调度器
scheduler = BackgroundScheduler()

# 添加定时任务，每隔 5 秒执行一次 job_function
scheduler.add_job(job_function, 'interval', seconds=5)

# 启动调度器
scheduler.start()

# 主程序继续执行
while True:
    # 做一些其他的事情
    pass
```

> `BackgroundScheduler` 允许任务在后台线程中异步执行，不会阻塞主程序的执行。
>
> `while True`是需要的，否则没有主程序，不会有执行结果，所以使用异步执行器，主程序也要一直运行。
>
> 在这个示例中，我们首先导入 `BackgroundScheduler` 类，并定义了一个名为 `job_function` 的函数作为定时任务。然后，我们创建了一个后台调度器对象 `scheduler`，并使用 `add_job` 方法添加了一个每隔 5 秒执行一次的定时任务。最后，通过调用 `start` 方法启动调度器。
>
> 注意，`BackgroundScheduler` 在启动后会在后台运行一个线程来执行任务调度，因此主程序可以继续执行其他操作。在这个示例中，我们使用一个简单的无限循环 (`while True`) 来模拟主程序的执行。你可以在循环中添加其他的代码来完成你的业务逻辑。
>
> 另外，`BackgroundScheduler` 还提供了一些其他的配置选项，如设置最大并发数、设定调度器的时区、指定任务的起始时间等。你可以在创建调度器时通过参数传递来进行配置。例如：
>
> ```python
> scheduler = BackgroundScheduler(job_defaults={'misfire_grace_time': 30}, timezone='Asia/Shanghai')
> ```
>
> 在这个示例中，我们通过 `job_defaults` 参数指定了任务的默认配置，设置了任务的最大容错时间为 30 秒。同时，通过 `timezone` 参数设置了调度器的时区为亚洲/上海。

## [基本概念](https://apscheduler.readthedocs.io/en/3.x/userguide.html)



