# **高级IO函数**
## **介绍了一些与网络编程相关的IO函数，大致分为三种：**
> * 创建文件描述符的函数，pipe、dup/dup2
> * 数据读写函数，readv/writev,sendfile、mmap/munmap、splice和tee函数
> * 文件描述符控制函数，fcntl

1. **pipe函数**
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

2. **dup/dup2函数**
> dup/dup2函数用于复制文件描述符，且指向文件与目标文件描述符一致，其定义
> ```
> int dup (int fd);
> int dup2 (int fd, int start)
> ```
> dup函数创建新的文件描述符，且指向目标文件描述符相同的文件，并且从当前系统未被使用的文件描述符中取最小值<br>
> dup2函数功能类似，但它创建的文件描述符整数值是不小于start的第一个整数值<br>
> **使用dup可以完成文件重定向，代码如下**
> ```
> close(STDOUT_FILENO);     //关闭标准输出
> fd2=dup(fd1)              //fd2的整数值为1
> ```

3. **readv/writev**
> readv提供了从文件描述符读数据至多个内存块，writev提供了从多个内存块向文件描述符写的操作，其函数声明
> ```
> #include <sys/uio.h>
> ssize_t readv(int fd, const struct iovec* vector, int count);
> ssize_t write(int fd, const struct iovec* vector, int count);
> ```
> vector是指向一个iovec结构体的指针，count表示有多少个这样的内存块，iovec内部由一个指向内存块首地址的指针和指明内存块大小的数据成员组成

4. **sendfile函数**
> 函数实现数据在两个文件描述符的传输，并且是在内核中完成，避免了数据在内核缓冲区和用户缓冲区的拷贝，这种技术被称为零拷贝
> ```
> #include <sys/sendfile.h>
> ssize_t sendfile(int out_fd, int in_fd, off_t* offset, size_t count);
> ```
> in_fd表示读取数据的文件描述符，out_fd表示写入数据的文件描述符，offset指示从读取文件的哪个位置开始读取，为0时使用文件默认的文件偏移量，count表示要从in_fd读取多少字节至out_fd
> * **in_fd只能是真实存在的文件，不能是管道或者socket,即in_fd指向的文件必须支持mmap()函数**
> * **out_fd只能是socket**
> * **sendfile函数是专门为网络是专门为网络传输文件设计的**

5. **mmap()和munmap()函数**
> mmap()用于申请一片内存，可以作为进程间通信的共享内存，也可以将文件映射到内存中，而munmap()用于释放掉这片内存，其定义如下
> ```
> #include <sys/mman.h>
> void* mmap(void* start,size_t length, int port, int flags, int fd, off_t offset);
> int munmap( void* start, size_t length);
> ```
> start参数允许用户设定这块内存的起始地址，也可为NULL，由系统分配；<br>
> length用于指定该内存块的大小<br>
> port用于设置内存段的访问权限，PORT_READ,PORT_WRITE,PORT_EXEC,PORT_NONE(不可被访问)<br>
> flags参数用于控制内存段被修改后，程序的行为<br>
> fd是被映射的文件描述符，offset表示从fd的某个位置开始映射<br>
> mmap调用成功时返回内存段的起始地址，失败时返回MAP_FAILED;munmap调用成功时返回0，失败返回-1

6. **splice**
> splice用于在两个文件描述符之间移动数据，也是零拷贝操作
> ```
> #include <fcntl.h>
> ssize_t splice(int fd_in, loff_t* off_in, int fd_out, loff_t* off_out, size_t len, unsigned int flags);
> ```
> * fd_in是待输入数据的文件描述符，如果是一个管道文件，则off_in必须为NULL；不是管道文件，则从off_in位置读取，设置为NULL，则从文件当前偏移位置读取
> * fd_out/off_out的定义相似
> * len表示移动数据的长度，flags参数用于控制数据移动的行为
> * **fd_in和fd_out中至少有一个是管道文件描述符**
> * **可以实现简单高效的回射服务器**

7. **tee函数**
> tee实现在两个管道文件描述符之间复制数据，零拷贝操作，不消耗数据，源文件描述符上数据仍可以用于后续操作
> ```
> #include<fcntl.h>
> ssize_t tee(int fd_in, int fd_out, size_t len, unsigned int flags);
> ```
> **使用tee函数实现shell中的tee程序的基本功能**
> ```
> //filename: tee.cpp
> #include <assert.h>
> #include <stdio.h>
> #include <unistd.h>
> #include <errno.h>
> #include <string.h>
> #include <fcntl.h>
> <br>
> int main(int argc, char* argv[]){
>   if (argc!=2){
>     return 1;
>   }
>   int filefd= open(argv[1], O_CREAT | O_WRONLY | O_TRUNC, 0666);
>   assert(filefd>0);
>   <br>
>   int pipefd_stdout[2];
>   int ret=pipe(pipefd_stdout);
>   assert(ret!=-1);
>   <br>
>   int pipe_file[2];
>   ret=pipe(pipe_file);
>   assert(ret!=-1);
>   <br>
>   /*将标准输入内容输入管道pipefd_stdout*/
>   /*将pipefd_stdout的输出复制到pipefd_file的输入端*/
>   /*将pipefd_file的输出定向到文件描述符filefd上*/
>   /*将pipefd_stdout的输出定向到标准输出*/
>   /*关闭所有文件描述符*/
> }
> ```

8. **fcntl**
> fcntl提供对文件描述符的各种控制操作，另一个常见系统调用是ioctl。fcntl的定义
> ```
> #include <fcntl.h>
> int fcntl( int fd, int cmd, ...);
> ```
> fd参数表明需要控制的文件描述符，cmd则是需要执行的具体操作，根据cmd的类型还有第三个可选的参数<br>
> 常用的cmd操作的功能：<br>
> * **F_DUPFD和F_DUPFD_CLOEXEC**:复制文件描述符，其值大于等于arg
> * **F_GETFD和F_SETFD**：获取或者设置文件描述符的标志
> * **F_GETFL和F_SETFL**：获取和设置文件描述符的状态标志
> * **F_GETPIPE_SZ和F_SETPIPE_SZ**:获取和设置管道的容量






















