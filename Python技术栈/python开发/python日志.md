# python 日志

## 基础入门

```python
# -*- coding:utf-8 -*-
import logging
import datetime
 
# filename：设置日志输出文件，以天为单位输出到不一样的日志文件，以避免单个日志文件日志信息过多，
# 日志文件若是不存在则会自动建立，但前面的路径如log文件夹必须存在，不然会报错
log_file = 'log/sys_%s.log' % datetime.datetime.strftime(datetime.datetime.now(), '%Y-%m-%d')
# level：设置日志输出的最低级别，即低于此级别的日志都不会输出
# 在平时开发测试的时候能够设置成logging.debug以便定位问题，但正式上线后建议设置为logging.WARNING，既能够下降系统I/O的负荷，也能够避免输出过多的无用日志信息
log_level = logging.WARNING
# format：设置日志的字符串输出格式
log_format = '%(asctime)s[%(levelname)s]: %(message)s'
logging.basicConfig(filename=log_file, level=logging.WARNING, format=log_format)
logger = logging.getLogger()
 
# 如下日志输出因为level被设置为了logging.WARNING，因此debug和info的日志不会被输出
logger.debug('This is a debug message!')
logger.info('This is a info message!')
logger.warning('This is a warning message!')
logger.error('This is a error message!')
logger.critical('This is a critical message!')
```

> 1. 使用`logging.basicConfig`进行基础配置，同时会自动创建一个处理器
> 2. 日志级别：critical > error > warning > info > debug，一般生产环境是指为warning，默认也是warning

## 进阶

1. `logging`主要有四个模块：记录器、处理器、过滤器和格式器。

   - 记录器暴露了应用程序代码直接使用的接口。

   - 处理器将日志记录（由记录器创建）发送到适当的目标。

   - 过滤器提供了更细粒度的功能，用于确定要输出的日志记录。

   - 格式器指定最终输出中日志记录的样式。

2. 在命名记录器时使用的一个好习惯是在每个使用日志记录的模块中使用模块级记录器，命名如下:

```python
logger = logging.getLogger(__name__)
```

这意味着记录器名称跟踪包或模块的层次结构，并且直观地从记录器名称显示记录事件的位置。

下面给出简单示例，日志分别存到文件和发送到指定邮箱

```python
import os
import datetime
import logging
from logging.handlers import SMTPHandler

def log_init() -> logging.Logger:
    '''日志初始化'''
    base_dir = os.path.dirname(os.path.abspath(__file__))

    log_file = f"{base_dir}/log/{datetime.datetime.now().strftime('%Y-%m-%d')}.log"
    LOG_FORMAT = "time: %(asctime)s - level: %(levelname)s - message: %(message)s"
    # logging.basicConfig(level=logging.DEBUG, format=LOG_FORMAT)
    logger = logging.getLogger(__name__)
    
    # 格式
    log_formatter = logging.Formatter(LOG_FORMAT)
    
    # 文件处理器
    file_handler = logging.FileHandler(log_file, encoding="utf-8")
    file_handler.setLevel(logging.DEBUG)
    file_handler.setFormatter(log_formatter)
    
    logger.addHandler(file_handler)
    
    # 配置 SMTPHandler
    mail_handler = SMTPHandler(
        mailhost=('smtp.qq.com', 587),  # 邮件服务器的地址和端口
        fromaddr='2807229316@qq.com',  # 发件人邮箱地址
        toaddrs='qingmeijibai@gmail.com',  # 收件人邮箱地址，可以是一个字符串或一个列表
        subject='Error Log',  # 邮件主题
        credentials=('2807229316@qq.com', 'xkyumrdplestdfje'),  # 邮箱登录凭证，用户名和密码
        secure=()  # 安全连接的方式，默认为空
    )

    # 设置日志级别
    mail_handler.setLevel(logging.ERROR)  # 只发送 ERROR 级别的日志信息
    mail_handler.setFormatter(log_formatter)

    logger.addHandler(mail_handler)

    return logger

if __name__ == "__main__":
    logger = log_init()
    logger.debug("debug message")
    logger.info("info message")
    logger.warning("warning message")
    logger.error("error message")
    logger.critical("critical message")
```

## 常用参数

> **logging.basicConfig的参数：**
>
> **filename：**设置日志输出的文件，默认输出到控制台。
> **filemode：**设置打开日志文件的方式，默认为“a”，即追加。
> **format：**设置日志输出的字符串格式，具体的格式有以下几种：
>
> - **%(name)s：**日志记录器的名称
> - **%(levelno)s：**日志级别数值
> - **%(levelname)s：**日志级别名称
> - **%(pathname)s：**输出日志时当前文件的绝对路径
> - **%(filename)s：**输出日志时当前文件名（包含后缀）
> - **%(module)s：**输出日志时的模块名（即%(filename)s不包含后缀名）
> - **%(funcName)s：**输出日志时所在函数名
> - **%(lineno)d：**输出日志时在文件中的行号
> - **%(asctime)s：**输出日志时的时间
> - **%(thread)d：**输出日志的当前线程ID
> - **%(threadName)s：**输出日志的当前线程名称
> - **%(process)s：**输出日志的当前进程ID
> - **%(processName)s：**输出日志的当前进程名称
> - **%(message)s：**输出日志的内容
>
> **datefmt：**设置日志时间字符串的输出格式，默认时间字符串格式为%Y-%m-%d %H:%M:%S。
> **style：**设置format字符串格式化的风格，能够是“%”，“{”或“$”，默认是“%”。
> **level：**设置日志的级别，具体有如下几种（由高到低）：调试
>
> - **CRITICAL**：致命错误
> - **ERROR：**严重错误
> - **WARNING：**须要给出的提示
> - **INFO：**通常的日志信息
> - **DEBUG：**在debug过程当中的debug信息
>
> **stream：**指定日志的输出Stream，能够是sys.stderr，sys.stdout或者是文件（即便用[open函数](https://so.csdn.net/so/search?q=open函数&spm=1001.2101.3001.7020)打开的文件流，可是这个文件流logging模块不会主动关闭它），默认是sys.stderr，若是同时指定了filename参数和stream参数，那stream参数就会被忽略。日志