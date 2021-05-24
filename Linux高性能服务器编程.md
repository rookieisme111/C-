# **Socket相关api**

1. **创建socket**
> 系统调用
> ```
> #include <sys/socket.h>
> #include <sys/types.h>
> int socket( int domain, int type, int protocal)
> ```
> domain表示的是协议簇，常见协议簇符号常量**PF_INET(IPv4)、PF_INET6(IPv6)和PF_UNIX(unix本地域的协议簇)**
> type表示提高的服务类型，常见的有**SOCK_STREAM(流服务，输出层的TCP服务)和SOCK_UGRAM(数据报服务，UDP)**
> protocal 一个具体的协议，默认为0

