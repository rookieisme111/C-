# **Socket相关api**

1. **创建socket**
> 系统调用
> ```
> #include <sys/socket.h>
> #include <sys/types.h>
> int socket( int domain, int type, int protocal);
> ```
> domain表示的是协议簇，常见协议簇符号常量**PF_INET(IPv4)、PF_INET6(IPv6)和PF_UNIX(unix本地域的协议簇)**<br>
> type表示提高的服务类型，常见的有**SOCK_STREAM(流服务，输出层的TCP服务)和SOCK_UGRAM(数据报服务，UDP)**<br>
> protocal 一个具体的协议，默认为0<br>
> 调用成功返回一个socket文件描述符，失败返回-1，并置errno

2. **命名socket**
>将一个socket与socket地址绑定的过程称为socket命名，其系统调用
>```
>#include <sys/socket.h>
>#include <sys/types.h>
>int bind ( int sockfd, const struct sockaddr* my_addr, socklen_t addrlen );
>```
>bind将my_addr中的socket地址分配给未命名的socket文件描述符中，addrlen表示地址长度<br>
>只有服务器上的socket需要显式命名，客户端的socket由操作系统负责隐式命名<br>
>常见的errno标志有EACCES和EADDRINUSE,前者表示绑定到了受保护的地址，后者表示绑定地址正在使用中

3. **监听socket**
> socket**命名**后还不能与客户端连接，需要创建一个监听队列来存放待处理的客户请求，其系统调用如下
> ```
> #include <sys/socket.h>
> int listen( int sockfd, int backlog)
> ```
> **sockfd**指示被监听的socket描述符，**backlog**指示监听队列的最大长度<br>
> 新版本内核，backlog只把ESTABLISHED状态的连接进行计数，对SYN_RCVD状态的连接没有要求

4. **接受连接**
> socket在启动监听后，即存在监听socket后，可以从监听队列中接受客户端的连接请求，系统调用
> ```
> #include <sys/types.h>
> #include <sys/socket.h>
> int accept (int sockfd, struct sockaddr* addr, socklen_t* addrlen)
> ```
> **sockfd**是执行过listen系统调用的监听socket,**addr**用于获取被接受连接的远端socket地址，socket地址的长度由**addrlen**指出<br>
> 调用成功返回一个新的连接socket，该socket唯一标识了被接受的连接，失败返回-1

5. **发起连接**
> 客户端通过下面的系统调用主动与服务器建立连接
> ```
> #include <sys/types.h>
> #include <sys/socket.h>
> int connect( int sockfd , const struct sockaddr* serv_addr, socklen_t addrlen);
> ```
> serv_addr是服务器监听的socket地址，socklen_t是这个地址长度，sockfd是本机内创建的socket对应文件描述符
> 调用成功返回0，sockfd就唯一标识了此连接。失败时返回-1，常见errno是ECONNREFUSED(被拒绝)，ETIMEDOUT(连接超时)

6. **关闭连接**
> 关闭一个连接即关闭连接对应的socket
> 可以使用通用的关闭文件系统调用close，也可以调用专门为网络编程设计的shutdown
> `int close(int fd)`<br>
> `int shutdowm(int sockfd, int howto)`
> close()只能同时关闭socket的读写连接，shutdown可以使用howto控制如何关闭socket(关闭读，关闭写，关闭读写)

7. **数据读写**
  * **TCP数据读写**
  > 文件的读写操作read和write同样适用socket,但内核提供了专门的socket数据读写系统调用
  > ```
  > #include <sys/types.h>
  > #include <sys/socket.h>
  > ssize_t recv(int sockfd, void* buf, size_t len, int flags);
  > ssize_t send(int sockfd, const void* buf, size_t len, int flags);
  > ```
  > recv读取sockfd上的数据，buf和len指定读缓冲区位置和大小。调用成功时返回实际读取到的数据长度，可能小于len，失败返回-1
  > send往sockfd上写数据，buf和len指定写缓冲区位置和大小。成功写入返回实际写入数据长度，失败返回-1
  > **flags参数**提供了额外控制

  * **UDP数据读写**
  > UDP数据报读写的系统调用
  > ```
  > #include <sys/types.h>
  > #include <sys/socket.h>
  > ssize_t recvfrom(int sockfd, void* buf, size_t len, int flags, struct sockaddr* src_addr, socklen_t* addrlen);
  > ssize_t sendto(int sockfd, const void* buf, size_t len, int flags, const struct sockaddr* dest_addr, socklen_t addrlen);
  > ```
  > 因为UDP没有面向连接的概念，所以每次发送或接受都要指定接收端的socket地址或者发送端的socket地址，当然这两个函数也可以使用面向连接的数据读写，只需后两个参数设为NULL
  
  * **通用数据读写函数**
  > socket接口提供了一组通用数据读写接口，适合TCP流数据和UDP数据报，以下是它的系统调用
  > ```
  > #include <sys/socket.h>
  > ssize_t recvmsg(int sockfd, struct msghdr* msg, int flags);
  > ssize_t sendmsg(int sockfd, struct msghdr* msg, int flags);
  > ```
  > 其中msghdr结构体的定义如下
  > ```
  > struct msghdr
  > {
  >   void* msg_name;       //socket地址
  >   socklen_t msg_namelen;
  >   strcut iovec* msg_iov;  //分散内存块
  >   int msg_iovlen;         //内存块的数量
  >   void* msg_control;      //指向辅助数据的起始地址
  >   socklen_t msg_controllen; //辅助数据大小
  >   int msg_flags;          //复制函数中的flags参数，并在调用过程中更新
  > }
  > ```
  > iovec结构体的定义
  > ```
  > struct iovec{
  >   void* iov_base;         //内存块的起始地址
  >   size_t iov_len;         //此内存块的长度
  > }
  > ```
  > 这样的读写方式称为**集中写（gather write）** 和 **分散读（scatter read)**

8. **~带外标记~**
9. **地址信息函数**
> 当想要获得一个连接socket的本端socket地址或远端socket地址时，调用下面的系统api
> ```
> #include <sys/socket.h>
> int getsockname(int sockfd, struct sockaddr* address, socklen_t* address_len)
> int getpeername(int sockfd, struct sockaddr* address, socklen_t* address_len)
> ```
> getsockname获得本端的socket地址，getpeername获得远端socket的地址，成功返回0，失败返回-1，实际地址大于address指向地址时，地址被截断

10. **socket选项**
> fcntl系统调用时控制文件描述符熟悉的通用POSIX方法，内核为socket文件描述符属性提供了专门的读取和设置方法
> ```
> #include <sys/socket.h>
> int getsockopt(int sockfd, int level, int option_name, void* option_value, socklen_t* restrcit option_len);
> int getsockopt(int sockfd, int level, int option_name, const void* option_value, socklen_t option_len);
> ```
> level指定协议，option_name指定选项（属性）名，option_value和option_len分别为被操纵选项的指和长度
> 对于服务器而言，有些选项只能在调用listen监听前给socket设置才有效，因为连接socket只能由accept调用返回，其属性也继承了监听socket,accept接受监听列表中的连接请求，这些连接都是完成了TCP中
> 前两步的（连接处于SYN_RCVD状态），说明服务器已经发送了同步报文段，而有些选项是在同步报文段中设置的，如TCP最大报文段大小。<br>
> 客户端而言，socket选项应该在调用connect前设置<br>
> ~一些常用的socket选项~

11. **网络信息api**
> 一些常用的网络信息api,如从主机名得到ip地址，从服务名得到端口号
   * **gethostbyname和gethostbyaddr**
   > 前者根据主机名获得主机信息，后者通过ip地址获取主机信息，其系统调用
   > ```
   > #include <netdb.h>
   > struct hostent* gethostbyname(const char* name);
   > struct hostent* gethostbyaddr(const void* addr, size_t len, int type);
   > ```
   > name指定主机名，addr指定主机的ip地址，len指定ip地址的长度，type指定ip类型（使用地址组类型）

   * **getservbyname和getservbyport**
   >> 前者通过服务名获取服务的信息，后者通过端口号获取服务的信息，系统调用定义
   >> ```
   >> #include <netdb.h>
   >> struct servent* getservbyname(const char* name, const char* proto);
   >> struct servent* getservbyport(int port, const char* proto);
   >> ```
   >> **以上四个函数都是不可重入的，即非线程安全的，给出了重入版本，在函数名后加_r(re-entrant)** 
   
   * ~**getaddrinfo**~
   >> 通过主机名获取ip地址，通过服务名获得端口号

   * ~**getnameinfo**~
   >> 通过socket地址获取主机名和服务名

















