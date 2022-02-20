# Socket

[Linux Howtos: C/C++ -> Sockets Tutorial](https://www.linuxhowtos.org/C_C++/socket.htm)

在Unix/Linux系统中，所有的东西都被抽象为文件。为了简化接口，所有的硬件也都被简化为文件。对于这些文件的操作等同于对于硬盘中的文件的操作。

为了区分已经打开了的和没有打开的文件，Linux引入了文件描述符的概念（file descriptor）

例如默认的两个文件描述符：

- fd=0代表标准输入文件，对应的硬件设备是键盘。
- fd=1代表标准输出文件，对应的硬件设备是显示器。

Linux系统在执行任何形式的IO的时候，都是在读取或者写入一个fd。fd只是一个和打开的文件相关联的int，它背后可能是一个普通文件、FIFO、管道、终端、键盘、显示器，甚至是一个网络连接。**在Linux中，网络连接也是一个文件。在Linux中，网络连接也是一个文件，也有fd。**

我们可以通过 socket() 函数来创建一个网络连接，或者说打开一个网络文件，socket() 的返回值就是文件描述符。有了文件描述符，我们就可以使用普通的文件操作函数来传输数据了，例如：

- 用 read() 读取从远程计算机传来的数据；
- 用 write() 向远程计算机写入数据。



### Socket的类型

#### 流格式套接字（SOCK_STREAM）

流格式套接字（Stream Sockets）也叫“面向连接的套接字”，在代码中使用 SOCK_STREAM 表示。**SOCK_STREAM 是一种可靠的、双向的通信数据流，数据可以准确无误地到达另一台计算机，如果损坏或丢失，可以重新发送。**

SOCK_STREAM有以下几个特征：

- 数据在传输过程中不会消失；
- 数据根据顺序传输；
- 数据的发送和接受不是同步的；

SOCK_STREAM能够顺序传输是因为采用了TCP传输，TCP会按照顺序到达并且保证没有错误。

**流格式套接字的内部有一个缓冲区（也就是字符数组），通过 socket 传输的数据将保存到这个缓冲区。**接收端在收到数据后并不一定立即读取，只要数据不超过缓冲区的容量，接收端有可能在缓冲区被填满以后一次性地读取，也可能分成好几次读取。

HTTP的传输使用的是SOCK_STREAM，因为必须要保证html文件没有错误，否则无法解析。

#### 数据报格式套接字（SOCK_DGRAM）

数据报格式套接字（Datagram Sockets）也叫“无连接的套接字”，在代码中使用 SOCK_DGRAM 表示。

计算机只管传输数据，不作数据校验，如果数据在传输中损坏，或者没有到达另一台计算机，是没有办法补救的。也就是说，数据错了就错了，无法重传。因为数据报套接字所做的校验工作少，所以在传输效率方面比流格式套接字要高。

SOCK_DGRAM有以下几个特征：

- 强调快速传输而不是顺序传输；
- 传输的数据有可能丢失或者错误；
- 限制每次传输的大小；
- 数据的发送和接收是同步的；

总之，数据报套接字是一种不可靠的、不按顺序传递的、以追求速度为目的的套接字。数据报套接字也使用 IP 协议作路由，**但是它不使用 TCP 协议，而是使用 UDP 协议（User Datagram Protocol，用户数据报协议）。**

网络视频聊天和语音通话就是使用SOCK_DGRAM传输数据报文，因为语音和视频的通话对于延迟的要求要比对于正确率的要求更高，稍微出现一些差错仍然可以解析。

> 注意：SOCK_DGRAM 没有想象中的糟糕，不会频繁的丢失数据，数据错误只是小概率事件。



### 数据类型

```cpp
struct sockaddr {
    unsigned short  sa_family;          // 地址族，一般为AF_INET
    char            sa_data[14];        // 14字节的协议地址
}
```

```cpp
struct sockaddr_in {
    short int           sin_family;         //地址族
    unsigned short int  sin_port;           //端口号
    struct in_addr      in_addr;            //ip地址
    unsigned char       sin_zero[8];        //填充
}
```



### Linux下的Socket演示程序

```cpp
// server.cpp

#include <stdio.h>
#include <string.h> // 注意是头文件而不是标准库，memset在string.h头文件中
#include <stdlib.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#include <netinet/in.h>

int main(){
    // 创建套接字
    int serv_sock  = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);

    // 将套接字和IP、端口绑定
    struct sockaddr_in serv_addr;
    memset(&serv_addr, 0, sizeof(serv_addr)); 					// 每个字节都用0填充
    serv_addr.sin_family = AF_INET;           					// 使用IPv4地址
    serv_addr.sin_addr.s_addr = inet_addr("127.0.0.1");			// 设置sock具体的IP
    serv_addr.sin_port = htons(1234);							// 设置sock端口
    bind(serv_sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));


    // 监听这个socket，并且规定最大连接请求20
    listen(serv_sock, 20);

    // 接收连接请求
    struct sockaddr_in clnt_addr;
    socklen_t clnt_addr_size = sizeof(clnt_addr);
    int clnt_sock = accept(serv_sock, (struct sockaddr*)&clnt_addr, &clnt_addr_size);

    // write 
    char str[] = "this is why we play";
    write(clnt_sock, str, sizeof(str));

    // close
    close(clnt_sock);
    close(serv_sock);

    return 0;    

}
```



```cpp
// client.cpp

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>

int main() {
    int sock = socket(AF_INET, SOCK_STREAM, 0);
    
    // request 
    struct sockaddr_in serv_addr;
    memset(&serv_addr, 0, sizeof(serv_addr));
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr.s_addr = inet_addr("127.0.0.1");
    serv_addr.sin_port = htons(1234);
    connect(sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));

    // read
    char buffer[40];
    read(sock, buffer, sizeof(buffer) -1);

    printf("message from server %s\n", buffer);

    close(sock);

    return 0;
}

```



> ## 源码解析
>
> 1) 先说一下 server.cpp 中的代码。
>
> 第 11 行通过 socket() 函数创建了一个套接字，参数 AF_INET 表示使用 IPv4 地址，SOCK_STREAM 表示使用面向连接的套接字，IPPROTO_TCP 表示使用 TCP 协议。在 Linux 中，socket 也是一种文件，有文件描述符，可以使用 write() / read() 函数进行 I/O 操作，这一点已在《[socket是什么](http://c.biancheng.net/view/2123.html)》中进行了讲解。
>
> 第 19 行通过 bind() 函数将套接字 serv_sock 与特定的 IP 地址和端口绑定，IP 地址和端口都保存在 sockaddr_in 结构体中。
>
> socket() 函数确定了套接字的各种属性，bind() 函数让套接字与特定的IP地址和端口对应起来，这样客户端才能连接到该套接字。
>
> 第 22 行让套接字处于被动监听状态。所谓被动监听，是指套接字一直处于“睡眠”中，直到客户端发起请求才会被“唤醒”。
>
> 第 27 行的 accept() 函数用来接收客户端的请求。程序一旦执行到 accept() 就会被阻塞（暂停运行），直到客户端发起请求。
>
> 第 31 行的 write() 函数用来向套接字文件中写入数据，也就是向客户端发送数据。
>
> 和普通文件一样，socket 在使用完毕后也要用 close() 关闭。
>
> 2) 再说一下 client.cpp 中的代码。client.cpp 中的代码和 server.cpp 中有一些区别。
>
> **第 19 行代码通过 connect() 向服务器发起请求，服务器的IP地址和端口号保存在 sockaddr_in 结构体中。直到服务器传回数据后，connect() 才运行结束。**
>
> 第 23 行代码通过 read() 从套接字文件中读取数据。



### Linux下的Socket相关的头文件

[Linux下Socket相关头文件总结_永不放弃的地盘-CSDN博客](https://blog.csdn.net/topasstem8/article/details/6014397)

1. socket(int domain, int type, int protocol)

   ```cpp
   #include <sys/types.h>
   #include <sys/socket.h>
   int serv_sock  = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
   ```

   - domain - 协议类型：一般为AF_NET，使用IPv4的地址
   - type - socket的类型：一般为SOCK_STREAM和SOCK_DGRAM

   - protocol - socket传输所使用的传输协议，通常设置为0

2. bind(int sockfd, struct sockaddr* sock_addr, int addrlen)

   ```cpp
   #include <sys/types.h>
   #include <sys/socket.h>
   bind(serv_sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));
   ```

   - sockfd - socket文件描述符，也就是socket()的返回值


   - sock_addr - 是一个指向包含有本机IP地址和端口号等信息的sockaddr类型的指针


   - addrlen - 一般就是这个sockaddr的size

3. connect(int sockfd, struct sockaddr* sock_addr, int addrlen)

   ```cpp
   #include <sys/types.h>
   #include <sys/socket.h>
   connect(serv_sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));
   ```

   - sockfd - serv_sock的文件描述符


   - sock_addr - 是一个指向包含有本机IP地址和端口号等信息的sockaddr类型的指针


   - addrlen - 一般就是这个sockaddr的size

4. listen(int sockfd, int backlog)

   ```cpp
   #include <sys/socket.h>
   listen(serv_sock, 20);
   ```

   - sockfd - serv_sock的文件描述符


   - backlog - 指定在请求队列中的最大请求数，进入的连接请求将在队列中等待accept()它们

5. accept(int sockfd, void *addr, int addrlen)

   ```cpp
   #include <sys/types.h> 
   #include <sys/socket.h>
   struct sockaddr_in clnt_addr;
   socklen_t clnt_addr_size = sizeof(clnt_addr);
   int clnt_sock = accept(serv_sock, (struct sockaddr*)&clnt_addr, &clnt_addr_size);
   ```

   - sockfd - serv_sock的文件描述符


   - addr - 通常是一个指向sockaddr_in变量的指针，也就是client发来的sockaddr_in，该变量用来存放提出连接请求服务的主机的信息


   - addrlen - client 的sockaddr的size

6. send(int sockfd, const void *msg, int len, int flags);

   - sockfd - 用来传输数据的socket的fd


   - msg - 要发送数据的指针

   - len - 缓冲的长度
   - flags - 0

7. recv(int sockfd, void *buf, int len, unsigned int flags)

   - sockfd - 用来传输数据的socket的fd
   - msg - 要发送数据的指针
   - len - 缓冲的长度
   - flags - 0

8. int sendto(int sockfd, const void *msg, int len, unsigned int flags, const struct sockaddr *to, int tolen);

9. int recvfrom(int sockfd, void *buf, int len, unsigned int flags, struct sockaddr *from, int fromlen)

10. read() write()

    int read(int fd, char *buf, int len)

    int write(int fd, char *buf, int len)

11. 

11. shutdown()
12. close(sockfd)



### read()/write()和send()/recv()有什么区别

recv和send函数提供了和read和write差不多的功能。但是他们提供 了第四个参数来控制读写操作。
int recv(int sockfd,void *buf,int len,int flags)
int send(int sockfd,void *buf,int len,int flags)
前面的三个参数和read,write相同,第四个参数能够是0或是以下的组合

| MSG_DONTROUTE | 不查找路由表 |
| MSG_OOB | 接受或发送带外数据 |
| MSG_PEEK | 查看数据,并不从系统缓冲区移走数据|
| MSG_WAITALL | 等待任何数据 |
**假如flags为0,则和read,write相同的操作。**更有其他的几个选项,但是我们实际上用的很少,能够查看Linux Programmer’s Manual得到周详解释

[socket编程中的read、write与recv、send的区别 - haichun - 博客园 (cnblogs.com)](https://www.cnblogs.com/haichun/p/3519232.html)



### shutdown()和close()有什么区别

int shutdown(int sockfd, int howto)
TCP连接是双向的(是可读写的)，**当我们使用close时，会把读写通道都关闭**，**有时侯我们希望只关闭一个方向，这个时候我们能够使用shutdown**。针对不同的howto，系统回采取不同的关闭方式。
  howto=0 - 关闭读通道，但是能够继续往sockfd写。
  howto=1 - 关闭写通道，和上面相反，这时候就只能够读了
  howto=2 - 关闭读写通道，和close相同

**在多进程程式里面，假如有几个子进程共享一个套接字时，假如我们使用shutdown，那么任何的子进程都不能够操作了，这个时候我们只能够使用close来关闭子进程的套接字描述符。**



### Linux下的socket()

[socket编程入门：1天玩转socket通信技术（非常详细） (biancheng.net)](http://c.biancheng.net/socket/)

[socket()函数用法详解：创建套接字 (biancheng.net)](http://c.biancheng.net/view/2131.html)

在Linux下使用<sys/socket.h>头文件中socket来创建套接字。函数原型为：

```cpp
int socket(int af, int type, int protocol);
```

1. af为地址族，全称address family，也就是ip地址类型，常用的有 AF_INET 和 AF_INET6。AF 是“Address Family”的简写，INET是“Inetnet”的简写。AF_INET 表示 IPv4 地址，例如 127.0.0.1；AF_INET6 表示 IPv6 地址，例如 1030::C9B4:FF12:48AA:1A2B。
2. type是数据传输方式/套接字类型，在前文提到，常用的有SOCK_STREAM（流格式套接字/面向连接的套接字）和SOCK_DGRAM（数据报套接字/无连接的套接字），分别代表TCP协议和UDP协议。
3. protocol代表传输协议类型，常用的有 IPPROTO_TCP 和 IPPTOTO_UDP，分别表示 TCP 传输协议和 UDP 传输协议。

实际上，如果已经有IP地址方式，加上数据传输格式，理论上系统已经可以自动推演出传输协议类型。除非遇到这样的情况：有两种不同的协议支持同一种地址类型和数据传输类型。如果我们不指明使用哪种协议，操作系统是没办法自动推演的。

使用IPv4时，如果选用SOCK_STREAM系统会自动选用TCP，此时的protocol如果设置为0也是可以的。

```cpp
int tcp_socket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);  //IPPROTO_TCP表示TCP协议
```

这种套接字称为 TCP 套接字。

如果使用 SOCK_DGRAM 传输方式，那么满足这两个条件的协议只有 UDP，因此可以这样来调用 socket() 函数：

```cpp
int udp_socket = socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP);  //IPPROTO_UDP表示UDP协议
```

这种套接字称为 UDP 套接字。

上面两种情况都只有一种协议满足条件，可以将 protocol 的值设为 0，系统会自动推演出应该使用什么协议，如下所示：

```cpp
int tcp_socket = socket(AF_INET, SOCK_STREAM, 0);  //创建TCP套接字
int udp_socket = socket(AF_INET, SOCK_DGRAM, 0);  //创建UDP套接字
```



### Linux下的bind()

[bind()和connect()函数：绑定套接字并建立连接 (biancheng.net)](http://c.biancheng.net/view/2344.html)

bind()的函数原型为：

```cpp
int bind(int sock, struct sock_addr* addr, socklen_t addrlen);
```

sock为fd，addr时指向sock_addr结构体的指针，addrlen是addr变量的大小，一般由sizeof计算得出。

```
// 首先由socket创建套接字
int serv_sock = socket(AF_INET, SOCK_STREAM, 0);

// 创建sock_addr结构体变量
struct sockaddr_in serv_addr;
memset(&serv_addr, 0, sizeof(serv_addr));  //每个字节都用0填充
serv_addr.sin_family = AF_INET;  //使用IPv4地址
serv_addr.sin_addr.s_addr = inet_addr("127.0.0.1");  //具体的IP地址
serv_addr.sin_port = htons(1234);  //端口

// sock_addr结构体变量的参数设置完毕之后
int bind(serv_sock, (struct sock_addr*)&serv_addr, sizeof(serv_addr));
```

sock_addr结构体中的参数：

```cpp
struct sockaddr_in{
    sa_family_t     sin_family;   //地址族（Address Family），也就是地址类型
    uint16_t        sin_port;     //16位的端口号
    struct in_addr  sin_addr;     //32位IP地址
    char            sin_zero[8];  //不使用，一般用0填充
};
```

1. sin_family的取值和socket()的第一个参数取值要保持一致。

2. sin_port为端口号。uint16_t 的长度为两个字节，**理论上端口号的取值范围为 0~65536**，**但 0~1023 的端口一般由系统分配给特定的服务程序**，例如 Web 服务的端口号为 80，FTP 服务的端口号为 21，所以我们的程序要**尽量在 1024~65536 之间分配端口号。**

   注意：**端口号需要用htons()转换**

3. sin_addr 是 struct in_addr 结构体类型的变量。

   实际上in_addr就是一个结构体变量，这个变量只包含一个成员，就是32位的IP地址。

   ```cpp
   struct in_addr{
       in_addr_t  s_addr;  //32位的IP地址
   };
   ```

   in_addr_t 在头文件 <netinet/in.h> 中定义，等价于 unsigned long，长度为4个字节。也就是说，s_addr 是一个整数，而IP地址是一个字符串，所以需要 inet_addr() 函数进行转换，例如：

   ```cpp
   unsigned long ip = inet_addr("127.0.0.1");
   printf("%ld\n", ip);
   ```

   ![img](http://c.biancheng.net/uploads/allimg/190219/112P63295-0.jpg)

4. sin_zero[8] 是多余的8个字节，没有用，一般使用 memset() 函数填充为 0。上面的代码中，先用 memset() 将结构体的全部字节填充为 0，再给前3个成员赋值，剩下的 sin_zero 自然就是0了。



### Linux下的listen()

[listen()和accept()函数：让套接字进入监听状态并响应客户端请求 (biancheng.net)](http://c.biancheng.net/view/2345.html)

通过 listen() 函数可以让套接字进入被动监听状态，它的原型为：

```cpp
int listen(int socket, int backlog);
```

sock 为需要进入监听状态的套接字，backlog 为请求队列的最大长度。

所谓被动监听，是指当没有客户端请求时，套接字处于“睡眠”状态，只有当接收到客户端请求时，套接字才会被“唤醒”来响应请求。

**请求队列**：当套接字正在处理客户端请求时，如果有新的请求进来，套接字是没法处理的，只能把它放进缓冲区，待当前请求处理完毕后，再从缓冲区中读取出来处理。如果不断有新的请求进来，它们就按照先后顺序在缓冲区中排队，直到缓冲区满。这个缓冲区，就称为请求队列（Request Queue）。backlog参数没有一个准确的标准，如果并发量小的话，大约是10或者20。当请求队列满了之后，当请求队列满时，就不再接收新的请求，对于 Linux，客户端会收到 ECONNREFUSED 错误。

注意：listen() 只是让套接字处于监听状态，并没有接收请求。接收请求需要使用 accept() 函数。

### Linux下的accep()

当套接字处于监听状态时，可以通过 accept() 函数来接收客户端请求。它的原型为：

```
int accept(int socket, struct sockaddr* addr, socklen_t* addr_len);
```

它的参数与 listen() 和 connect() 是相同的：sock 为服务器端套接字，addr 为 sockaddr_in 结构体变量，addrlen 为参数 addr 的长度，可由 sizeof() 求得。

**accept() 返回一个新的套接字来和客户端通信**，**addr 保存了客户端的IP地址和端口号**，而 sock 是服务器端的套接字。**后面和客户端通信时，要使用这个新生成的套接字，而不是原来服务器端的套接字。**

最后需要说明的是：**listen() 只是让套接字进入监听状态，并没有真正接收客户端请求，listen() 后面的代码会继续执行，直到遇到 accept()**。**accept() 会阻塞程序执行（后面代码不能被执行），直到有新的请求到来。**



### Linux下的读写

[send()/recv()和write()/read()：发送数据和接收数据 (biancheng.net)](http://c.biancheng.net/view/2346.html)

Linux 不区分套接字文件和普通文件，使用 write() 可以向套接字中写入数据，使用 read() 可以从套接字中读取数据。

前面我们说过，两台计算机之间的通信相当于两个套接字之间的通信，在服务器端用 write() 向套接字写入数据，客户端就能收到，然后再使用 read() 从套接字中读取出来，就完成了一次通信。



### socket的缓冲区以及阻塞模式

[socket缓冲区以及阻塞模式详解 (biancheng.net)](http://c.biancheng.net/view/2349.html)

每个socket被创建之后都会分配两个缓冲区：输入缓冲区和输出缓冲区。

write()和read()并非直接向socket中写入数据，而是将数据写入缓冲区中，再由TCP或者UDP协议将数据从缓冲区中取出，发送到目标机器，一旦将数据成功写入到缓冲区，函数就可以返回成功，不管他们有没有到达目标机器。这些是TCP协议负责的事情。

TCP协议独立于 write()/send() 函数，数据有可能刚被写入缓冲区就发送到网络，也可能在缓冲区中不断积压，多次写入的数据被一次性发送到网络，这取决于当时的网络情况、当前线程是否空闲等诸多因素，不由程序员控制。

read()/recv() 函数也是如此，也从输入缓冲区中读取数据，而不是直接从网络中读取。

![TCP套接字的I/O缓冲区示意图](http://c.biancheng.net/uploads/allimg/190219/1149355056-0.jpg)



这些I/O缓冲区特性可整理如下：

- I/O缓冲区在每个TCP套接字中单独存在；
- I/O缓冲区在创建套接字时自动生成；
- **即使关闭套接字也会继续传送输出缓冲区中遗留的数据**；// shutdown
- **关闭套接字将丢失输入缓冲区中的数据**。// close

阻塞模式：

对于TCP套接字（默认情况下），当使用 write()/send() 发送数据时：

1. 首先会检查缓冲区，如果缓冲区的可用空间长度小于要发送的数据，那么 write()/send() 会被阻塞（暂停执行），直到缓冲区中的数据被发送到目标机器，腾出足够的空间，才唤醒 write()/send() 函数继续写入数据。

2. 如果TCP协议正在向网络发送数据，那么输出缓冲区会被锁定，不允许写入，write()/send() 也会被阻塞，直到数据发送完毕缓冲区解锁，write()/send() 才会被唤醒。

3. 如果要写入的数据大于缓冲区的最大长度，那么将分批写入。
4. 直到所有数据被写入缓冲区 write()/send() 才能返回。

当使用 read()/recv() 读取数据时：

1. 首先会检查缓冲区，如果缓冲区中有数据，那么就读取，否则函数会被阻塞，直到网络上有数据到来。

2. 如果要读取的数据长度小于缓冲区中的数据长度，那么就不能一次性将缓冲区中的所有数据读出，剩余数据将不断积压，直到有 read()/recv() 函数再次读取。

3. 直到读取到数据后 read()/recv() 函数才会返回，否则就一直被阻塞。

这就是TCP套接字的阻塞模式。**所谓阻塞，就是上一步动作没有完成，下一步动作将暂停，直到上一步动作完成后才能继续，以保持同步性。**



### TCP的粘包问题

由于数据的接收和发送是无关的，read()/recv() 函数不管数据发送了多少次，都会尽可能多的接收数据。也就是说，read()/recv() 和 write()/send() 的执行次数可能不同。

例如，write()/send() 重复执行三次，每次都发送字符串"abc"，那么目标机器上的 read()/recv() 可能分三次接收，每次都接收"abc"；也可能分两次接收，第一次接收"abcab"，第二次接收"cabc"；也可能一次就接收到字符串"abcabcabc"。

这就是数据的“粘包”问题，客户端发送的多个数据包被当做一个数据包接收。也称数据的无边界性，read()/recv() 函数不知道数据包的开始或结束标志（实际上也没有任何开始或结束标志），只把它们当做连续的数据流来处理。



### TCP的优雅关闭连接和强制关闭连接

[11. 如何关闭连接？优雅 VS 粗暴 - 简书 (jianshu.com)](https://www.jianshu.com/p/95d8500c7dcb)

由于TCP断开连接是由四次挥手的过程决定，发起连接关闭请求的那一方会有一段时间处于TIME_WAIT的状态。在最后的关闭阶段需要重点关注的是半连接状态。

因为TCP是全双工的通信协议，数据可以流入也可以流出。在绝大多数情况下，TCP连接都是先关闭一个方向，此时另外一个方向还是可以正常进行数据传输。

1. close函数

   ```cpp
   int close(int sockfd);
   ```

   close函数对于文件的引用计数减一，如果成功则返回0，不成功则返回-1。一旦发现套接字的引用计数到0，就会对套接字彻底释放，并且关闭双向的数据传输。

   **套接字引用计数**：因为套接字可以被多个进程共享，简单理解为，如果我们fork一个子进程，那么套接字的引用计数就会加一，如果close这个sockfd，那么这个计数就会减一。

   close函数具体通过以下方式关闭连接：

   - 在输入方向，系统内核会将该套接字设置为不可读，任何的读取操作都会返回异常。

   - 在输出方向，系统内核会尝试将缓冲区的数据发送给对方，并向对端的socket发送一个FIN报文，之后的所有写操作都会返回异常。如果对端没有检测到连接关闭， 那么系统会重新发送一个RST的报文头，直接将连接关闭。

2. shutdown函数

   ```cpp
   int shutdown(int sockfd, int howto);
   ```

   对于已连接的套接字执行shutdown操作，如果成功返回0，如果不成功返回-1。

   howto 是这个函数的设置选项，它的设置有三个主要选项：

   - SHUT_RD(0)：关闭连接的“读”这个方向，对该套接字进行读操作直接返回 EOF。从数据角度来看，套接字上接收缓冲区已有的数据将被丢弃，如果再有新的数据流到达，会对数据进行 ACK，然后悄悄地丢弃。也就是说，对端还是会接收到 ACK，在这种情况下根本不知道数据已经被丢弃了。
   - SHUT_WR(1)：关闭连接的“写”这个方向，这就是常被称为”半关闭“的连接。此时，不管套接字引用计数的值是多少，都会直接关闭连接的写方向。套接字上发送缓冲区已有的数据将被立即发送出去，并发送一个 FIN 报文给对端。应用程序如果对该套接字进行写操作会报错。
   - SHUT_RDWR(2)：相当于 SHUT_RD 和 SHUT_WR 操作各一次，关闭套接字的读和写两个方向。

shutdown函数和close函数的区别：

close会关闭连接，并且释放所有对应的资源，而shutdown并不会释放套接字和所有的资源。

close函数存在套接字的引用计数的概念，并不一定导致这个套接字会直接不可用，但是shutdown会直接使得套接字不可用。如果这个套接字还有别的进程在使用的话，那么这个进程将会受到影响。

close的引用计数导致socket不一定会发出FIN报文，而shutdown总是会发出FIN结束报文。

