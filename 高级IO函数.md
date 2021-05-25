# **高级IO函数**
## **介绍了一个与网络编程相关的IO函数，大致分为三种：**
> * 创建文件描述符的函数，pipe、dup/dup2
> * 数据读写函数，readv/writev,sendfile、mmap/munmap、splice和tee函数
> * 文件描述符控制函数，fcntl

1. pipe函数
> 用于创建一个管道，其系统调用定义
> ```
> #include <unistd.h>
> int pipe(int fd[2]);
> ```
> * 参数为一个包含两个int数据的数组指针，调用成功返回0，并且**将一对打开的文件描述符填入参数数组中**；失败返回-1，并置errno<br>
> * fd[0]是读取端，fd[1]是写入端，fd[1]中写入的数据可以从fd[0]中读取出来<br>
> * 默认情况下，一对文件描述符都是**阻塞的**,read空管道会引发阻塞，write满管道会引发阻塞<br>
> * 当fd[1]的引用计数为0时，表示没有进程要写入数据，读取进程的read调用返回0，表示文件尾；当fd[0]的引用计数为0时，表示没有进程要读取数据，写入进程的write调用会失败，并引发SIGPIPE信号<br>

> **socket提供了socketpair函数，用于创建双向通道，定义**
> ```
> #include <sys/socket.h>
> #include <sys/types.h>
> int socketpair(int domain, int type, int protocal, int fd[2]);
> ```
> **创建一个双向的管道，两端均可读可写，domain只能是unix本地域协议族AF_UNIX**
