# Linux IO模型

## 基础概念

1. Linux系统中**一切皆文件**，如下图所示：

![img](https://s2.loli.net/2022/03/18/WlBJq4I3fwnFM6d.png)

2. 这样访问所有资源，就有了一个统一的接口，例如对socket文件进行读写，就可以完成网络通信。

3. 为了方便找到某个文件，我们需要给每个文件一个编号（索引），即：**文件描述符**（file descriptor），在Linux下称为**fd**，在Windows下称为**句柄（handler）**

4. Linux下，启动时会默认打开三个文件，0 标准输入，1标准输出，2标准错误

5. 使用task_struct结构体维护进程相关的表，称为**进程控制块**（PCB），块里有个指针指向file_struct结构体，称为文件描述表（类似二维数组），文件描述符就是这个表的索引

   ![图片](https://s2.loli.net/2022/03/18/D4nWBjZxE7SOlHo.png)

6. 在Linux中，IO数据会先被缓存到操作系统内核的缓冲区，然后再从缓冲区拷贝到应用程序的地址空间。

7. 对于一次IO访问（以read举例），数据会先被拷贝到操作系统内核的缓冲区中，然后才会从操作系统内核的缓冲区拷贝到应用程序的地址空间。所以说，当一个read操作发生时，它会经历两个阶段：

   1. 等待数据准备 (Waiting for the data to be ready)
   2. 将数据从内核拷贝到进程中 (Copying the data from the kernel to the process)

   正是因为这两个阶段，linux系统产生了下面五种网络模式的方案。

   - 阻塞 I/O（blocking IO）
   - 非阻塞 I/O（nonblocking IO）
   -  I/O 多路复用（ IO multiplexing）
   - 信号驱动 I/O（ signal driven IO）
   - 异步 I/O（asynchronous IO）

   前三种都是同步的，最后一种是异步的

## IO多路复用（IO multiplexing）

IO multiplexing就是我们说的**select**，**poll**，**epoll**(都是系统调用函数，IO多路复用的具体实现)，有些地方也称这种IO方式为event driven IO。select/epoll的好处就在于单个process就可以同时处理多个网络连接的IO。它的基本原理就是select，poll，epoll这个function会不断的轮询所负责的所有socket，当某个socket有数据到达了，就通知用户进程。

![clipboard.png](https://s2.loli.net/2022/03/18/dWyctveNq4xo5bG.png)

`当用户进程调用了select，那么整个进程会被block`，而同时，kernel会“监视”所有select负责的socket，当任何一个socket中的数据准备好了，select就会返回。这个时候用户进程再调用read操作，将数据从kernel拷贝到用户进程。

> 所以，I/O 多路复用的特点是通过一种机制一个进程能同时等待多个文件描述符，而这些文件描述符（套接字描述符）其中的任意一个进入读就绪状态，select()函数就可以返回。

这个图和blocking IO的图其实并没有太大的不同，事实上，还更差一些。因为这里需要使用两个system call (select 和 recvfrom)，而blocking IO只调用了一个system call (recvfrom)。但是，用select的优势在于它可以同时处理多个connection。

所以，如果处理的连接数不是很高的话，使用select/epoll的web server不一定比使用multi-threading + blocking IO的web server性能更好，可能延迟还更大。select/epoll的优势并不是对于单个连接能处理得更快，而是在于能处理更多的连接。）

在IO multiplexing Model中，实际中，对于每一个socket，一般都设置成为non-blocking，但是，如上图所示，整个用户的process其实是一直被block的。只不过process是被select这个函数block，而不是被socket IO给block。

## select,poll,epoll

```c
int select (int __nfds, fd_set *__restrict __readfds,
           fd_set *__restrict __writefds,
           fd_set *__restrict __exceptfds,
           struct timeval *__restrict __timeout);
```

1. **fd_set**可以理解为要监听的fd集合，使用位图存储每个fd，内核采用轮询的方式检查这些fd，一旦某个fd就绪了，就修改fd_set的内容，调用方再遍历一遍，就可以知道那些fd就绪了（利用方法参数进行结果传递，熟悉的味道）

2. 多路复用也就体现在这个fd集合上了，一个进程可以监听多个fd，而无需使用多个线程，每个线程监听一个fd，这样很耗费资源，对fd集合的查询，交给内核来完成。
3. select监听的fd数目有限制，最多1024个，poll 65535
4. select和poll的机制基本相同，只不过poll没有select最大文件描述符的限制，在具体使用的时候，有如下缺点：
   - 每次调用select或者poll，都需要将监听的fd_set或者pollfd发送给内核态，如果需要监听大量的文件描述符，这样的效率是很低下的
   - 在内核态中，每次需要对传入的文件描述符进行轮询，查询是否有对应的事件产生。

## epoll

epoll更加高效，主要由下面三个函数组成：

```c
int epoll_create (int __size);
int epoll_ctl (int __epfd, int __op, int __fd, struct epoll_event *__event);
int epoll_wait (int __epfd, struct epoll_event *__events, int __maxevents, int __timeout);
```

#### epoll_create

```c
int epoll_create (int __size);
```

epoll_create函数创建一个epoll实例并返回，该实例可以用于监控__size个文件描述符

#### epoll_ctl

```c
int epoll_ctl (int __epfd, int __op, int __fd, struct epoll_event *__event);
```

简单说，就是增删改fd

该函数用来向epoll中注册事件函数，其中__epfd为epoll_create返回的epoll实例，__op表示要进行的操作，__fd为要进行监控的文件描述符，__event要监控的事件。

__op可用的类型定义在sys/epoll.h头文件中，如下：

```c
#define EPOLL_CTL_ADD 1        // 添加文件描述符
#define EPOLL_CTL_DEL 2        // 删除文件描述符
#define EPOLL_CTL_MOD 3        //    修改文件描述符（指的是epoll_ctl中传入的__event）
```

该函数如果调用成功返回0，否则返回-1。

#### epoll_wait

```c
int epoll_wait (int __epfd, struct epoll_event *__events, int __maxevents, int __timeout);
```

简单说，获取fds集合中已就绪的

epoll_wait类似与select中的select函数、poll中的poll函数，等待内核返回监听描述符的事件产生，其中__epfd是epoll_create创建的epoll实例，__events数组为epoll_wait要返回的已经产生的事件集合，其中第i个元素成员的__events[i]->data->fd表示产生该事件的描述符，__maxevents为希望返回的最大的事件数量（通常为__events的大小），__timeout和poll中的__timeout相同。该函数返回已经就绪的事件的数量，如果为-1表示出错。

已经就绪的fds通过events给出，无需像select和poll自己遍历fd集合，确定那个fd就绪。

## epoll实现TCP反射

TCP反射：就是傻瓜服务器，客户端向服务器发啥，服务器就将同样的内容返给客户端。

该程序的主要逻辑如下：

```c
服务器：
    1. 开启服务器套接字
    2. 将服务器套接字加入要监听的集合中（select的fd_set、poll的pollfd、epoll调用epoll_ctl）
    3. 进入循环，调用IO多路复用的API函数（select/poll/epoll_create），如果有事件产生：
        3.1. 服务器套接字产生的事件，添加新的客户端到监听集合中
        3.2. 客户端套接字产生的事件，读取数据，并立马回传给客户端
        
客户端：
    1. 开启客户端套接字
    2. 将客户端套接字和标准输入文件描述符加入要监听的集合中（select的fd_set、poll的pollfd、epoll调用epoll_ctl）
    3. 进入循环，调用IO多路复用的API函数（select/poll/epoll_create），如果有事件产生：
        3.1. 客户端套接字产生的事件，则读取数据，将其输出到控制台
        3.2. 标准输入文件描述符产生的事件，则读取数据，将其通过客户端套接字传给服务器
```

看完这段逻辑，才对IO多路复用有了感性的认识。

代码连接：https://github.com/yearsj/ClientServerProject 