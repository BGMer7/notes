

# Linux

[Linux下查询进程PS或者杀死进程kill的小技巧 - 魁·帝小仙 - 博客园 (cnblogs.com)](https://www.cnblogs.com/dxxblog/p/8033788.html)

### Linux主要负责的功能

系统内存管理

软件程序管理

硬件设备管理

文件系统管理



### Shell管道

[Linux管道命令（pipe） - 简书 (jianshu.com)](https://www.jianshu.com/p/9c0c2b57cb73)

grep



### 文本搜索工具







### 文件描述符fd

文件描述符（File descriptor）是计算机科学中的一个术语，是一个用于表述指向文件的引用的抽象化概念。

文件描述符在形式上是一个非负整数。实际上，它是一个索引值，指向内核为每一个进程所维护的该进程打开文件的记录表。当程序打开一个现有文件或者创建一个新文件时，内核向进程返回一个文件描述符。在程序设计中，一些涉及底层的程序编写往往会围绕着文件描述符展开。但是文件描述符这一概念往往只适用于UNIX、Linux这样的操作系统。

> 在Linux中，一切皆文件。

所有的东西都可以抽象成文件，包括普通文件，目录文件，字符设备文件，块设备文件。套接字等等。所有的都抽象成文件，提供统一的接口，方便应用程序调用。

当应用程序请求内核打开/新建一个文件的时候，Linux内核会返回一个fd用于对应这个打开的文件，fd的本质就是一个非负整数，读写文件也是需要这个fd来指定待读写文件。

**Linux的kernel为每一个进程都维护了一个文件描述符表**，这个表的索引都是从0开始，所以在不同的进程中的同样fd，不一定指向同一个文件。

> 当应用程序启动的时候**，0是标准输入，1是标准输出，2是标准错误。如果此时去打开一个新的文件，它的文件描述符将是3。**POSIX标准要求每次打开文件时（含socket）必须使用当前进程中最小可用的文件描述符号。

文件描述符是一个重要的系统资源，理论上内存有多大就可以打开多少文件描述符。但是实际上OS会对此做出系统级限制和用户级限制，为了不让某一个用户消耗掉所有的文件资源。在Linux中使用ulimit -n查看。

```shell
gatsby@ubuntu:~$ ulimit -n
1024
```

文件描述符的执行关系：应用程序拿到的文件描述符和进程文件描述符表的**索引**一致的话，通过索引拿到**文件指针**，指向系统级文件描述符表的**文件偏移量**，再通过文件偏移量找到**innode指针**，最终找到对应的真实文件。

Socket套接字也是一个文件，当server端监听到有连接的时候，应用程序会请求内核创建Socket，Socket创建好之后会返回一个fd给应用程序。如果有数据包过来，内核会通过数据包的源端口、源地址、目的端口等信息，找到对应的socket，并将数据包赋值到该socket的缓冲区，应用程序请求读取Socket中的数据的时候，内核就会将数据拷贝到缓冲区，将数据拷贝到应用程序的地址空间。

```c
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>

int main(int argc, char* argv[]) {
    // 以只读模式打开 demo.txt 文件
    int fd = open("fd_demo.txt", O_RDONLY);
    int fd_cpy = open("fd_demo_cpy.txt", O_RDONLY);
    if (fd == -1) {
        perror("open demo.txt error\n");
        return EXIT_FAILURE;
    }
    // 打印获取到的文件描述符
    printf("demo.txt fd = %d \n", fd);
    printf("demo_cpy.txt fd = %d \n", fd_cpy);
    printf("Now I'll close the first file with its fd. \n");
    int return_close = close(fd);
    if(return_close==0) printf("close succeed! \n");
    printf("Now the second file's fd is %d \n", fd_cpy);
    close(4);
    fd_cpy = open("fd_demo_cpy.txt", O_RDONLY);
    printf("the fd after the second file closed and reopen again is %d \n",fd_cpy); 
    return EXIT_SUCCESS;
}

```

输出为：

```txt
demo.txt fd = 3 
demo_cpy.txt fd = 4 
Now I'll close the first file with its fd.
close succeed! 
Now the second file's fd is 4 
the fd after the second file closed and reopen again is 3 
```

说明fd的文件存在回收机制。

[文件描述符了解一下 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/108744787)

[理解文件描述符 - 简书 (jianshu.com)](https://www.jianshu.com/p/a2df1d402b4d)



### 同步和异步

同步：当一个同步调用发出去之后，调用者要一直等待到结果的通知之后，才能进行后续的执行。

异步：当一个异步调用发出去之后，调用者不能立刻得到调用的返回。

异步调用之后想要得到返回的结果，一般有两种方式：

- 主动轮询异步调用的结果，但是会消耗大量的资源。
- 被调用方callback来通知调用方调用结果。



### 阻塞和非阻塞

阻塞与非阻塞的重点在于进/线程等待消息时候的行为，也就是在等待消息的时候，当前进/线程是挂起状态，还是非挂起状态。

阻塞调用在发出去后，在消息返回之前，当前进/线程会被挂起，直到有消息返回，当前进/线程才会被激活.

非阻塞调用在发出去后，不会阻塞当前进/线程，而会立即返回。

***总结：***

1. 同步与异步，重点在于**消息通知的方式**；阻塞与非阻塞，重点在于**等消息时候的行为**。

2. 同步才分阻塞和非阻塞，异步一定是非阻塞的。



### I/O模式

缓存 I/O 又被称作标准 I/O，大多数文件系统的默认 I/O 操作都是缓存 I/O。在 Linux 的缓存 I/O 机制中，操作系统会将 I/O 的数据缓存在文件系统的页缓存（ page cache ）中，也就是说，数据会先被拷贝到操作系统内核的缓冲区中，然后才会从操作系统内核的缓冲区拷贝到应用程序的地址空间。

**缓存 I/O 的缺点：**
数据在传输过程中需要在应用程序地址空间和内核进行多次数据拷贝操作，这些数据拷贝操作所带来的 CPU 以及内存开销是非常大的。

每一次的IO访问，以read为例，数据都需要先被拷贝到操作系统的内核的缓冲区中，然后才会从操作系统的缓冲区中拷贝到应用程序的地址空间，所以当一个read发生的时候，会有以下的几个阶段：

1. 等待数据准备
2. 将数据从内核中拷贝到应用程序的地址空间中

这个过程导致Linux主要分为四种IO模式：阻塞IO、非阻塞IO、异步IO、IO多路复用。

#### 阻塞IO

在Linux系统中，默认情况下的socket都是阻塞IO模式。一个典型的读操作流程大概是这样：

![preview](https://segmentfault.com/img/bVm1c3/view)

用户调用recvfrom这个系统调用，kernel就开始准备数据，如果网路中的数据已经到达缓冲区，就将缓冲区中的数据拷贝到用户内存中。如果数据没有准备好，kernel就要等待数据到来，用户进程阻塞，等到数据准备好了，kernel将数据拷贝完毕，返回结果，用户进程解除阻塞，重新运行。

> 所以，blocking IO的特点就是在IO执行的两个阶段都被block了。

#### 非阻塞IO

linux下，可以通过设置socket使其变为non-blocking。当对一个non-blocking socket执行读操作时，流程是这个样子：

![preview](https://segmentfault.com/img/bVm1c4/view)

当用户进程发出read操作时，如果kernel中的数据还没有准备好，那么它并不会block用户进程，而是立刻返回一个error。从用户进程角度讲 ，它发起一个read操作后，并不需要等待，而是得到了一个结果。用户进程判断结果是一个error时，它就知道数据还没有准备好，于是它可以再次发送read操作。一旦kernel中的数据准备好了，并且又再次收到了用户进程的system call，那么它马上就将数据拷贝到了用户内存，然后返回。

> 所以，nonblocking IO的特点是用户进程需要**不断的主动询问**kernel数据好了没有。

但是为了等到数据，仍需要不断地轮询、重复请求，消耗了大量的CPU的资源。一般很少直接使用这种模型，而是在其他IO模型中使用非阻塞IO这一特性。

#### IO多路复用

IO multiplexing就是我们说的select，poll，epoll，有些地方也称这种IO方式为event driven IO。select/epoll的好处就在于单个process就可以同时处理多个网络连接的IO。它的基本原理就是select，poll，epoll这个function会不断的轮询所负责的所有socket，当某个socket有数据到达了，就通知用户进程。

![preview](https://segmentfault.com/img/bVm1c5/view)

当用户进程调用了select，那么整个进程会被block，而同时，kernel会“监视”所有select负责的socket，当任何一个socket中的数据准备好了，select就会返回。这个时候用户进程再调用read操作，将数据从kernel拷贝到用户进程。

> 所以，I/O 多路复用的特点是通过一种机制一个进程能同时等待多个文件描述符，而这些文件描述符（套接字描述符）其中的任意一个进入读就绪状态，select()函数就可以返回。

这个图和blocking IO的图其实并没有太大的不同，事实上，还更差一些。因为这里需要使用两个system call (select 和 recvfrom)，而blocking IO只调用了一个system call (recvfrom)。但是，用select的优势在于它可以同时处理多个connection。

所以，如果处理的连接数不是很高的话，使用select/epoll的web server不一定比使用multi-threading + blocking IO的web server性能更好，可能延迟还更大。select/epoll的优势并不是对于单个连接能处理得更快，而是在于能处理更多的连接。）

在IO multiplexing Model中，实际中，对于每一个socket，一般都设置成为non-blocking，但是，如上图所示，整个用户的process其实是一直被block的。只不过process是被select这个函数block，而不是被socket IO给block。

#### 异步IO

Linux下的异步IO其实使用很少。用户进程发起read操作之后，立刻就可以开始去做其它的事。而另一方面，从kernel的角度，当它受到一个asynchronous read之后，首先它会立刻返回，所以不会对用户进程产生任何block。然后，kernel会等待数据准备完成，然后将数据拷贝到用户内存，当这一切都完成之后，kernel会给用户进程发送一个signal，告诉它read操作完成了。

IO小结：

![preview](https://segmentfault.com/img/bVm1c9/view)

同步和异步的区别在于，异步当进程发起IO 操作之后，就直接返回再也不理睬了，直到kernel发送一个信号，告诉进程说IO完成。在这整个过程中，进程完全没有被block。**同步才分阻塞和非阻塞，异步一定是非阻塞的。**



### select

目前支持I/O多路复用的系统调用有select，poll，epoll，I/O多路复用通过机制，使得一个进程可以监视多个fd

Select：Linux系统通过select函数来实现多路复用I/O模型，select系统调用是用来让我们的程序监视多个文件描述符的状态变化，程序会停留在select处等待，直到监视的fd出现了一个或者多个改变。但select、poll、epoll都是同步IO，因为这些方法都需要就绪之后自己进行读写，这个过程是阻塞的。异步IO不需要自己负责读写，异步IO将数据从内核空间拷贝到用户空间。

**I/O多路复用优势和适用场景：**

I/O多路复用的优势在于，当处理的消耗对比IO几乎可以忽略不计时，**可以处理大量的并发IO**，**而不用消耗太多CPU/内存**。这就像是一个工作很高效的人，手上一个todo list，他高效的依次处理每个任务。这比每个任务单独安排一个人要节省。典型的例子是nginx做代理，代理的转发逻辑相对比较简单直接，那么IO多路复用很适合。相反，如果是一个做复杂计算的场景，计算本身可能是个 指数复杂度的东西，IO不是瓶颈。那么怎么充分利用CPU或者显卡的核心多干活才是关键。

此外，**IO多路复用适合处理很多闲置的IO，因为IO socket的数量的增加并不会带来进（线）程数的增加，也就不会带来stack内存，内核对象，切换时间的损耗。因此像长链接做通知的场景非常适合。**

> select

```cpp
int select (int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

select监视的文件描述符号分三类，分别是readfds, writefds, exceptfds。调用之后select描述符会阻塞，直到有描述符就绪（有数据可读、可写、产生except），或者超时（timeout指定等待时间，如果立即返回就设置为null），函数即可返回。当select函数返回之后，可以通过遍历fdset来找到就绪的描述符。

[**Linux编程之select - Madcola - 博客园 (cnblogs.com)**](https://www.cnblogs.com/skyfsm/p/7079458.html)

最简单的select程序

```cpp
#include <sys/select.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    fd_set rd;
    struct timeval time_val;
    int err;

    FD_ZERO(&rd);
    FD_SET(0, &rd);

    time_val.tv_sec = 5;
    time_val.tv_usec = 0;
    err=select(1, &rd, NULL, NULL, &time_val);
    // int select(int maxfdp, fd_set *readset, fd_set *writeset, fd_set *exceptset,struct timeval *timeout);
    if(err==0) 
        printf("select time out!\n");
    else if(err==-1)
        printf("failed to select!\n");
    else
        printf("data is available!\n");

    return 0;
}

```

程序编译之后的运行结果

```shell
gatsby@ubuntu:~/Desktop/os$ g++ select.cpp -o select
gatsby@ubuntu:~/Desktop/os$ ./select
ls
data is available!
gatsby@ubuntu:~/Desktop/os$ ls
data_process_thread      fd.c             fork      select      wait.cpp
data_process_thread.cpp  fd_demo_cpy.txt  fork.cpp  select.cpp  wait.png
fd                       fd_demo.txt      fork.png  wait
gatsby@ubuntu:~/Desktop/os$ ./select
pwd
data is available!
gatsby@ubuntu:~/Desktop/os$ pwd
/home/gatsby/Desktop/os
```

理解select的关键在于理解fd_set，为了说明方便，取fd_set为一个字节。fd_set的每一个二进制bit都是代表一个fd，那么一个字节的fd_set就可以代表8个文件。

1. 执行fd_set set; FD_ZERO(&set); set用二进制表示就是0000 0000。

2. 如果fd=5，FD_SET(fd, &set); set就变成了0001 0000。fd=5的信息就被保存在fd_set里。

3. 如果再加入FD_SET(2, &set); 那么set变成0001 0010。

4. 执行select(1, &rd, NULL, NULL, &time_val); 阻塞等待

   //int select(int maxfdp, fd_set *readset, fd_set *writeset, fd_set *exceptset,struct timeval *timeout);

5. 如果此时fd=1，fd=2都发生可读事件，则select变成0000 0011。没有发生的fd就被清零。

```cpp
fd_set  set;
    FD_ZERO(&set);        /*将set的所有位置0，如set在内存中占8位则将set置为00000000*/
    FD_SET(0, &set);       /*将set的第0位置1，如set原来是00000000，则现在变为100000000，这样fd==1的文件描述字就被加进set中了*/
    FD_CLR(4, &set);       /*将set的第4位置0，如set原来是10001000，则现在变为10000000，这样fd==4的文件描述字就被从set中清除了*/
    FD_ISSET(5, &set);     /*测试set的第5位是否为1，如果原来set是10000100，则返回非零，表明fd==5的文件描述符在set中，否则返回0*/
```

select函数的特点：

1. **可监控的文件描述符个数取决与sizeof(fd_set)的值**。在原程序中加入sizeof(fd_set)查看得到结果128，说明，每bit表示一个文件描述符，则我服务器上支持的最大文件描述符是512*8=4096。这个数字由编译得Linux系统内核分配。
2. 将fd加入select监控集的同时，**还要再使用一个数据结构array保存放到select监控集中的fd**，一是用于当select返回后，**array作为源数据和fd_set进行FD_ISSET判断**。二是**select返回后会把以前加入的但并无事件发生的fd清空，则每次开始select前都要重新从array取得fd逐一加入（FD_ZERO最先）**，扫描array的同时取得fd最大值maxfd，用于select的第一个参数。
3. 可见select模型**必须在select前循环加fd，取maxfd，select返回后利用FD_ISSET判断是否有事件发生。**

```cpp
#include <sys/types.h> 
#include <sys/socket.h> 
#include <stdio.h> 
#include <netinet/in.h> 
#include <sys/time.h> 
#include <sys/ioctl.h> 
#include <unistd.h> 
#include <stdlib.h>

int main() 
{ 
    int server_sockfd, client_sockfd; 
    int server_len, client_len; 
    struct sockaddr_in server_address; 
    struct sockaddr_in client_address; 
    int result; 
    fd_set readfds, testfds; 
    server_sockfd = socket(AF_INET, SOCK_STREAM, 0);//建立服务器端socket 
    server_address.sin_family = AF_INET; 
    server_address.sin_addr.s_addr = htonl(INADDR_ANY); 
    server_address.sin_port = htons(8888); 
    server_len = sizeof(server_address); 
    bind(server_sockfd, (struct sockaddr *)&server_address, server_len); 
    listen(server_sockfd, 5); //监听队列最多容纳5个 
    FD_ZERO(&readfds); 
    FD_SET(server_sockfd, &readfds);//将服务器端socket加入到集合中
    while(1) 
    {
        char ch; 
        int fd; 
        int nread; 
        testfds = readfds;//将需要监视的描述符集copy到select查询队列中，select会对其修改，所以一定要分开使用变量 
        printf("server waiting\n"); 

        /*无限期阻塞，并测试文件描述符变动 */
        result = select(FD_SETSIZE, &testfds, (fd_set *)0,(fd_set *)0, (struct timeval *) 0); //FD_SETSIZE：系统默认的最大文件描述符
        if(result < 1) 
        { 
            perror("server5"); 
            exit(1); 
        } 

        /*扫描所有的文件描述符*/
        for(fd = 0; fd < FD_SETSIZE; fd++) 
        {
            /*找到相关文件描述符*/
            if(FD_ISSET(fd,&testfds)) 
            { 
              /*判断是否为服务器套接字，是则表示为客户请求连接。*/
                if(fd == server_sockfd) 
                { 
                    client_len = sizeof(client_address); 
                    client_sockfd = accept(server_sockfd, 
                    (struct sockaddr *)&client_address, &client_len); 
                    FD_SET(client_sockfd, &readfds);//将客户端socket加入到集合中
                    printf("adding client on fd %d\n", client_sockfd); 
                } 
                /*客户端socket中有数据请求时*/
                else 
                { 
                    ioctl(fd, FIONREAD, &nread);//取得数据量交给nread
                    
                    /*客户数据请求完毕，关闭套接字，从集合中清除相应描述符 */
                    if(nread == 0) 
                    { 
                        close(fd); 
                        FD_CLR(fd, &readfds); //去掉关闭的fd
                        printf("removing client on fd %d\n", fd); 
                    } 
                    /*处理客户数据请求*/
                    else 
                    { 
                        read(fd, &ch, 1); 
                        sleep(5); 
                        printf("serving client on fd %d\n", fd); 
                        ch++; 
                        write(fd, &ch, 1); 
                    } 
                } 
            } 
        } 
    } 

    return 0;
}
```

```cpp
//客户端
#include <sys/types.h> 
#include <sys/socket.h> 
#include <stdio.h> 
#include <netinet/in.h> 
#include <arpa/inet.h> 
#include <unistd.h> 
#include <stdlib.h>
#include <sys/time.h>

int main() 
{ 
    int client_sockfd; 
    int len; 
    struct sockaddr_in address;//服务器端网络地址结构体 
     int result; 
    char ch = 'A'; 
    client_sockfd = socket(AF_INET, SOCK_STREAM, 0);//建立客户端socket 
    address.sin_family = AF_INET; 
    address.sin_addr.s_addr = inet_addr("127.0.0.1");
    address.sin_port = htons(8888); 
    len = sizeof(address); 
    result = connect(client_sockfd, (struct sockaddr *)&address, len); 
    if(result == -1) 
    { 
         perror("oops: client2"); 
         exit(1); 
    } 
    //第一次读写
    write(client_sockfd, &ch, 1); 
    read(client_sockfd, &ch, 1); 
    printf("the first time: char from server = %c\n", ch); 
    sleep(5);
    
    //第二次读写
    write(client_sockfd, &ch, 1); 
    read(client_sockfd, &ch, 1); 
    printf("the second time: char from server = %c\n", ch);
    
    close(client_sockfd); 
   
    return 0; 
}
```

Select本质上就是靠通过设置或者检查存放fd的标志位是0还是1来进行下一步处理。

- **对于Socket采用线性扫描轮询的方法，效率比较低**。当socket连接比较多的时候，每次select都要轮询整个fd_set，会浪费很多CPU时间。
- **需要维护一个存放大量fd的数据结构**，当内核空间和用户空间传递数据结构的时候会消耗大量资源。

[linux select 函数和 fd_set 用法_stephen_yin的专栏-CSDN博客](https://blog.csdn.net/stephen_yin/article/details/7441165)





### epoll

在Linux的网络编程中，很长的时间都在使用select来做事件触发。在Linux新的内核中，有了一种替换它的机制，就是epoll。
相比于select，epoll最大的好处在于它不会随着监听fd数目的增长而降低效率。因为在内核中的select实现中，它是采用轮询来处理的，轮询的fd数目越多，自然耗时越多。并且，在linux/posix_types.h头文件有这样的声明：
\#define __FD_SETSIZE  1024
表示select最多同时监听1024个fd，当然，可以通过修改头文件再重编译内核来扩大这个数目，但这似乎并不治本。

epoll的函数就只有三个，

```cpp
#include <sys/epoll.h>
int epoll_create(int size)

int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)

int epoll_wait(int epfd, struct epoll_event *event, int maxevents, int timeout)
```

[linux epoll用法 - 二狗啸地 - 博客园 (cnblogs.com)](https://www.cnblogs.com/zuofaqi/p/9638098.html)

函数中的主要参数解释：

1. epoll_create(int size)

   epoll需要一个额外的文件描述符，来唯一标识内核中的这个事件表。这个文件描述符由epoll_create创建

2. epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)

   epfd是epoll_create返回的文件描述符 ，

   op指定操作类型，有如下三个：

   | 宏定义                  | 含义                                          |
   | ----------------------- | --------------------------------------------- |
   | #define EPOLL_CTL_ADD 1 | Add a file descriptor to the interface.       |
   | #define EPOLL_CTL_DEL 2 | Remove a file descriptor from the interface.  |
   | #define EPOLL_CTL_MOD 3 | Change file descriptor epoll_event structure. |

   *event是一个指针，指向epoll_event结构体，epoll_event的结构定义如下：

   ```cpp
   typedef union epoll_data {
       void    *ptr;
       int     fd;
   	uint32_t   u32;
       uint64_t   u64;
   } epoll_data_t;
   
   struct epoll_event {
       uint32_t   events;    /* Epoll events */
       epoll_data_t data;    /* User data variable */
   };
   ```
   
   events 是成员描述符事件类型。事件类型也定义在 sys/epoll.h 文件中
   
   data 是 epoll_data_t 联合体类型。可以用fd 表示文件描述符，或者用ptr指针指向更多的用户数据。
   
   | Epoll_event注册函数 | 表示的含义                                                   |
   | ------------------- | ------------------------------------------------------------ |
   | EPOLLIN             | 表示对应的fd可以读，包括对端Socket正常关闭                   |
   | EPOLLOUT            | 表示对应的fd可以写                                           |
   | EPOLLPRI            | 表示对应的fd有紧急数据可以读（这里表示应该有带外数据来）     |
   | EPOLLERR            | 表示对应的fd发生错误                                         |
   | EPOLLHUP            | 表示对应的fd被挂断，例如收到RST包                            |
   | EPOLLRDHUP          | 表示对应的fd对端Socket关闭事件，就会一直epollin事件，驱动处理client socket |
   | EPOLLET             | 将EPOLL设置为边沿触发                                        |
   | EPOLLONESHOT        |                                                              |
   
3. epoll_wait(int epfd, struct epoll_event *event, int maxevents, int timeout)

   epoll 系列系统调用的主要接口是epoll_wait函数，它在一段超时时间内等待一组文件描述符上的事件。

   成功时，返回就绪文件描述符的个数，失败返回-1,并设置errno

   其中，timeout指定超时时间，单位毫秒。-1表示永远等待直到有文件描述符就绪。

   maxevents 指定最多监听多少个事件，它必须大于0

   epoll_wait 函数如果检测到时间，就将事件从内核事件表中复制到第二个参数events指向的数组中。这个数组只输出epoll_wait检测到的就绪事件。

```cpp
// server
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <errno.h>
#include <sys/socket.h>
#include <netinet/in.h>			/* socket类定义需要*/
#include <sys/epoll.h>			/* epoll头文件 */
#include <fcntl.h>	                /* nonblocking需要 */
#include <sys/resource.h>		/* 设置最大的连接数需要setrlimit */
 
#define	MAXEPOLL	10000	/* 对于服务器来说，这个值可以很大的！ */
#define	MAXLINE		1024
#define 	PORT			6000
#define	MAXBACK	1000
 
//!> 设置非阻塞
//!> 
int setnonblocking( int fd )
{
	if( fcntl( fd, F_SETFL, fcntl( fd, F_GETFD, 0 )|O_NONBLOCK ) == -1 )
	{
		printf("Set blocking error : %d\n", errno);
		return -1;
	}
	return 0;
}
 
int main( int argc, char ** argv )
{
	int 		listen_fd;
	int 		conn_fd;
	int 		epoll_fd;
	int 		nread;
	int 		cur_fds;				//!> 当前已经存在的数量
	int 		wait_fds;				//!> epoll_wait 的返回值
	int		i;
	struct sockaddr_in servaddr;
	struct sockaddr_in cliaddr;
	struct 	epoll_event	ev;
	struct 	epoll_event	evs[MAXEPOLL];
	struct 	rlimit	rlt;		//!> 设置连接数所需
	char 	buf[MAXLINE];
	socklen_t	len = sizeof( struct sockaddr_in );
 
	//!> 设置每个进程允许打开的最大文件数
	//!> 每个主机是不一样的哦，一般服务器应该很大吧！
	//!> 
	rlt.rlim_max = rlt.rlim_cur = MAXEPOLL;
	if( setrlimit( RLIMIT_NOFILE, &rlt ) == -1 )	
	{
		printf("Setrlimit Error : %d\n", errno);
		exit( EXIT_FAILURE );
	}
	
	//!> server 套接口
	//!> 
	bzero( &servaddr, sizeof( servaddr ) );
	servaddr.sin_family = AF_INET;
	servaddr.sin_addr.s_addr = htonl( INADDR_ANY );
	servaddr.sin_port = htons( PORT );
	
	//!> 建立套接字
	if( ( listen_fd = socket( AF_INET, SOCK_STREAM, 0 ) ) == -1 )
	{
		printf("Socket Error...\n");
		exit( EXIT_FAILURE );
	}
	
	//!> 设置非阻塞模式
	//!> 
	if( setnonblocking( listen_fd ) == -1 )
	{
		printf("Setnonblocking Error : %d\n", errno);
		exit( EXIT_FAILURE );
	}
	
	//!> 绑定
	//!>
	if( bind( listen_fd, ( struct sockaddr *)&servaddr, sizeof( struct sockaddr ) ) == -1 )
	{
		printf("Bind Error : %d\n", errno);
		exit( EXIT_FAILURE );
	}
 
	//!> 监听
	//!> 
	if( listen( listen_fd, MAXBACK ) == -1 )
	{
		printf("Listen Error : %d\n", errno);
		exit( EXIT_FAILURE );
	}
	
	//!> 创建epoll
	//!> 
	epoll_fd = epoll_create( MAXEPOLL );	//!> create
	ev.events = EPOLLIN | EPOLLET;		//!> accept Read!
	ev.data.fd = listen_fd;					//!> 将listen_fd 加入
	if( epoll_ctl( epoll_fd, EPOLL_CTL_ADD, listen_fd, &ev ) < 0 )
	{
		printf("Epoll Error : %d\n", errno);
		exit( EXIT_FAILURE );
	}
	cur_fds = 1;
	
	while( 1 )
	{
		if( ( wait_fds = epoll_wait( epoll_fd, evs, cur_fds, -1 ) ) == -1 )
		{
			printf( "Epoll Wait Error : %d\n", errno );
			exit( EXIT_FAILURE );
		}
 
		for( i = 0; i < wait_fds; i++ )
		{
			if( evs[i].data.fd == listen_fd && cur_fds < MAXEPOLL )	
													//!> if是监听端口有事
			{
				if( ( conn_fd = accept( listen_fd, (struct sockaddr *)&cliaddr, &len ) ) == -1 )
				{
					printf("Accept Error : %d\n", errno);
					exit( EXIT_FAILURE );
				}
				
				printf( "Server get from client !\n"/*,  inet_ntoa(cliaddr.sin_addr), cliaddr.sin_port */);
				
				ev.events = EPOLLIN | EPOLLET;		//!> accept Read!
				ev.data.fd = conn_fd;					//!> 将conn_fd 加入
				if( epoll_ctl( epoll_fd, EPOLL_CTL_ADD, conn_fd, &ev ) < 0 )
				{
					printf("Epoll Error : %d\n", errno);
					exit( EXIT_FAILURE );
				}
				++cur_fds; 
				continue;		
			}
			
			//!> 下面处理数据
			//!> 
			nread = read( evs[i].data.fd, buf, sizeof( buf ) );
			if( nread <= 0 )						//!> 结束后者出错
			{
				close( evs[i].data.fd );
				epoll_ctl( epoll_fd, EPOLL_CTL_DEL, evs[i].data.fd, &ev );	//!> 删除计入的fd
				--cur_fds;					//!> 减少一个呗！
				continue;
			}
			
			write( evs[i].data.fd, buf, nread );			//!> 回写
			
		}
	}
	
	close( listen_fd );
	return 0;
}

```

```cpp
// client

#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>
#include <netinet/in.h>
#include <sys/types.h>
#include <sys/socket.h>
#include  <arpa/inet.h>
#include <sys/select.h>
 
#define MAXLINE 1024
#define SERV_PORT 6000
 
//!> 注意输入是由stdin，接受是由server发送过来
//!> 所以在client端也是需要select进行处理的
void send_and_recv( int connfd )
{
	FILE * fp = stdin;
	int   lens;
	char send[MAXLINE];
	char recv[MAXLINE];
	fd_set rset;
	FD_ZERO( &rset );
	int maxfd = ( fileno( fp ) > connfd ? fileno( fp ) : connfd  + 1 );	
							        //!> 输入和输出的最大值
	int n;
	
	while( 1 )
	{
		FD_SET( fileno( fp ), &rset );
		FD_SET( connfd, &rset );			//!> 注意不要把rset看作是简单的一个变量
								//!> 注意它其实是可以包含一组套接字的哦，
								//!> 相当于是封装的数组！每次都要是新的哦！
		
		if( select( maxfd, &rset, NULL, NULL, NULL ) == -1 )
		{
			printf("Client Select Error..\n");
			exit(EXIT_FAILURE  );
		}
		
		//!> if 连接口有信息
		if( FD_ISSET( connfd, &rset ) )	//!> if 连接端口有信息
		{
			printf( "client get from server ...\n" );
			memset( recv, 0, sizeof( recv ) );
			n = read( connfd, recv, MAXLINE );
			if( n == 0 )
			{
				printf("Recv ok...\n");
				break;
			}
			else if( n == -1 )
			{
				printf("Recv error...\n");
				break;
			}
			else
			{
				lens = strlen( recv );
				recv[lens] = '\0';
				//!> 写到stdout
				write( STDOUT_FILENO, recv, MAXLINE );
				printf("\n");
			}
 
		}
		
		//!> if 有stdin输入
		if( FD_ISSET( fileno( fp ), &rset ) )	//!> if 有输入
		{
			//!> printf("client stdin ...\n");
			
			memset( send, 0, sizeof( send ) );
			if( fgets( send, MAXLINE, fp ) == NULL )
			{
				printf("End...\n");
				exit( EXIT_FAILURE );
			}
			else
			{
				//!>if( str )
				lens = strlen( send );
				send[lens-1] = '\0';		//!> 减一的原因是不要回车字符
								//!> 经验值：这一步非常重要的哦！！！！！！！！
				if( strcmp( send, "q" ) == 0 )
				{
					printf( "Bye..\n" );
					return;
				}
				
				printf("Client send : %s\n", send);
				write( connfd, send, strlen( send ) );
			}
		}
		
	}
	
}
 
int main( int argc, char ** argv )
{
	//!> char * SERV_IP = "10.30.97.188";
	char 	buf[MAXLINE];
	int   	connfd;
	struct sockaddr_in servaddr;
	
	if( argc != 2 )
	{
		printf("Input server ip !\n");
		exit( EXIT_FAILURE );
	}
	
	//!> 建立套接字
	if( ( connfd = socket( AF_INET, SOCK_STREAM, 0 ) ) == -1 )
	{
		printf("Socket Error...\n" , errno );
		exit( EXIT_FAILURE );
	}
 
	//!> 套接字信息
	bzero(&servaddr, sizeof(servaddr));
    servaddr.sin_family = AF_INET;
    servaddr.sin_port = htons(SERV_PORT);
    inet_pton(AF_INET, argv[1], &servaddr.sin_addr);
	
	//!> 链接server
	if( connect( connfd, ( struct sockaddr *  )&servaddr, sizeof( servaddr ) ) < 0 )
	{
		printf("Connect error..\n");
		exit(EXIT_FAILURE);
	}	
	/*else
	{
		printf("Connet ok..\n");
	}*/
 
	//!>
	//!> send and recv
	send_and_recv( connfd );
	
	//!> 
 
	close( connfd );
	printf("Exit\n");
	
	return 0;
}
```

[linux 网络编程：epoll 的实例_shanshanpt的专栏-CSDN博客_epoll编程实例](https://blog.csdn.net/shanshanpt/article/details/7383400)







#### epoll的两种触发方式

epoll 对文件描述符的操作有两种模式：LT（level trigger 电平触发）和 ET（edge trigger 边沿触发）。

这两个词汇来自电学术语，你可以将 fd 上有数据认为是**高电平**，没有数据认为是**低电平**，将 fd 可写认为是**高电平**，fd 不可写认为是**低电平**。那么水平模式的触发条件是状态处于高电平，而边缘模式的触发条件是新来一次电信号将当前状态变为高电平，即：

**水平模式的触发条件**

```
低电平 => 高电平
处于高电平状态
```

**边缘模式的触发条件**

```
低电平 => 高电平
```

LT是默认的工作模式。在这种模式下，文件描述符会一直被检测到直到应用程序处理它。ET模式下，文件描述符就绪，被通知给应用程序，之后，就不再通知该同一事件了。ET模式降低了同一个epoll时间被重复触发的次数，因此效率较高。

通俗来讲，LT模式下，一个事件只要有，就会一直触发，ET模式下，只有发生变化才会触发。

[epoll LT 模式和 ET 模式详解（文末赠书） - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1636224)

**epoll的优点：**

1、**没有最大并发连接的限制，能打开的FD的上限远大于1024（1G的内存上能监听约10万个端口）**；
**2、效率提升，不是轮询的方式，不会随着FD数目的增加效率下降。只有活跃可用的FD才会调用callback函数；**
即Epoll最大的优点就在于它只管你“活跃”的连接，而跟连接总数无关，因此**在实际的网络环境中，Epoll的效率就会远远高于select和poll。**

3、 内存拷贝，利用mmap()文件映射内存加速与内核空间的消息传递；即epoll使用mmap减少复制开销。
**select、poll、epoll 区别总结：**



### select poll eopll三者的联系与区别

1. 支持一个进程所能打开的最大连接数：

   select和poll的区别：select 函数中，内核对 fd_set 集合的大小做出了限制，大小不可变为1024；而 poll 函数中，并没有最大文件描述符数量的限制（基于链表存储）。

   epoll的底层通过红黑树实现，基本没有数量限制，1GB内存可以监听10万个端口，相比之下32位机的select的内核只允许单个进程监视1024个fd，64位只允许2048个fd。

2. fd剧增之后带来的IO效率问题：

   select和poll都是依赖线性扫描查看是否fd需要返回，fd剧增会导致性能线性下降。

   因为epoll内核中实现是根据每个fd上的callback函数来实现的，只有活跃的socket才会主动调用callback，所以在活跃socket较少的情况下，使用epoll没有前面两者的线性下降的性能问题。只有当所有socket都很活跃的情况下，性能问题将会面临和前两者同样的问题。

3. 消息传递的机制：

   select和poll都需要在用户空间和内核空间之间不停拷贝数据，如果fd_set的数据量比较大，那么将会增大内核的负担。epoll的操作中，内核空间和用户空间共用同一块内存。



### Linux下的TCP Socket连接传输文件

```cpp
int  listenfd, connfd;
struct sockaddr_in  servaddr;
char  buff[4096];
FILE *fp;
```



### Linux的文件属性

```shell
gatsby@ubuntu:~$ ls -l
total 64
drwxr-xr-x 7 gatsby gatsby  4096 Jun 27 12:43 Desktop
drwxr-xr-x 2 gatsby gatsby  4096 May 30 14:25 Documents
drwxr-xr-x 2 gatsby gatsby  4096 May 30 14:25 Downloads
-rw-r--r-- 1 gatsby gatsby  8980 May 30 14:17 examples.desktop
-rwxr-xr-x 1 gatsby gatsby 13264 May 30 15:00 hello
drwxr-xr-x 2 gatsby gatsby  4096 May 30 14:25 Music
drwxr-xr-x 2 gatsby gatsby  4096 May 30 16:10 Pictures
drwxr-xr-x 2 gatsby gatsby  4096 May 30 14:25 Public
drwxr-xr-x 4 gatsby gatsby  4096 Jun  1 20:56 snap
drwxr-xr-x 2 gatsby gatsby  4096 May 30 14:25 Templates
drwxr-xr-x 2 gatsby gatsby  4096 May 30 14:25 Videos

gatsby@ubuntu:~/Desktop$ ls -l
total 44
drwxrwxr-x 2 gatsby gatsby  4096 Jun 15 10:37 cpp
drwxrwxr-x 2 gatsby gatsby  4096 Jun 27 10:07 go
-rwxr-xr-x 1 gatsby gatsby 13264 Jun 13 16:36 hello
-rw-r--r-- 1 gatsby gatsby   399 May 30 15:01 hello.cpp
drwxr-xr-x 2 gatsby gatsby  4096 Jun 13 16:47 io
-rw-r--r-- 1 gatsby gatsby   495 Jun 13 16:53 io.md
drwxrwxr-x 2 gatsby gatsby  4096 Jun 15 10:29 os
drwxrwxr-x 2 gatsby gatsby  4096 Jun 27 16:45 socket

```

> drwxr-x--- 该权限分为4个部分d、rwx、r-x、---

d - 表示文件类型，-:普通文件，d:目录文件，l:链接文件，b:设备文件，c:字符设备文件，p:管道文件

rwx - 表示文件的所有者对于该文件的权限

r-x - 表示文件所属的组对于该文件的权限

--- - 表示其他用户对于该文件的权限

- 读(read)，写(write)，执行r(recute)简写即为(r,w,x),亦可用数字来(4,2,1)表示。
- 如果某文件权限为7则代表可读(4)、可写(2)、可执行(1)，即(4+2+1=7).
- 若权限为6(4+2)则代表可读(4)、可写(2)。
- 权限为5(4+1)代表可读(4)和可执行(1).
- 权限为3(2+1)代表可写(2)和可执行(1)。

```shell
drwxrwxr-x 2 gatsby gatsby  4096 Jun 27 10:07 go
// go是一个文件夹 文件夹中的权限如下
gatsby@ubuntu:~/Desktop$ ls -l go
total 1900
-rwxr-xr-x 1 gatsby gatsby 1937655 Jun 27 10:07 helloWorld
// 读写执行/读执行/读执行  属主 属组 
-rw-rw-r-- 1 gatsby gatsby     237 Jun 27 12:43 helloWorld.go
// 读写/读写/读 属主 属组 
```



### Linux的目录结构

[深入理解linux系统的目录结构（总结的非常详细）_反者道之动；弱者道之用-CSDN博客_linux系统目录结构](https://blog.csdn.net/yup1212/article/details/82152106)



### Linux查看内存、硬盘

[linux 查看空间（内存、磁盘、文件目录、分区）的几个命令_愿我如星君如月 ... 夜夜流光相皎洁 ...-CSDN博客_linux查看磁盘空间 命令](https://blog.csdn.net/jiangyu1013/article/details/86685893)



### init

[linux 下的init 0，1，2，3，4，5，6知识介绍_My Oracle Recipe-CSDN博客](https://blog.csdn.net/cougar_mountain/article/details/9798191)



### find & locate & location

[linux中whereis、which、find、location的区别和用法 - 泥土里的绽放 - 博客园 (cnblogs.com)](https://www.cnblogs.com/cjjjj/p/9846374.html)

find <指定目录> <指定条件> <指定动作>

-<指定目录>：所要搜索的目录的所有子目录。默认为当前目录。

-<指定条件>：所要搜索的文件的特征。

-<指定动作>：对搜索结果进行特定处理。

```shell
gatsby@ubuntu:~/Desktop/git$ find . -name "*thread*"
./WebServer/old_version/old_version_0.6/threadpool.h
./WebServer/old_version/old_version_0.6/threadpool.cpp
./WebServer/old_version/old_version_0.1/threadpool.h
./WebServer/old_version/old_version_0.1/threadpool.cpp
./WebServer/old_version/old_version_0.5/threadpool.h
./WebServer/old_version/old_version_0.5/threadpool.cpp
./WebServer/old_version/old_version_0.3/threadpool.h
./WebServer/old_version/old_version_0.3/threadpool.cpp
./WebServer/old_version/old_version_0.2/threadpool.h
./WebServer/old_version/old_version_0.2/threadpool.cpp
./WebServer/old_version/old_version_0.4/threadpool.h
./WebServer/old_version/old_version_0.4/threadpool.cpp
gatsby@ubuntu:~/Desktop/git$ find . -name "*thread*" -ls -l
find: unknown predicate `-l'
gatsby@ubuntu:~/Desktop/git$ find . -name "*thread*" -ls -a
find: invalid expression
gatsby@ubuntu:~/Desktop/git$ find . -name "*thread*" -ls
   295450      4 -rwxr-xr-x   1 root     root         1496 Jul 13 10:23 ./WebServer/old_version/old_version_0.6/threadpool.h
   295449      8 -rwxr-xr-x   1 root     root         4627 Jul 13 10:23 ./WebServer/old_version/old_version_0.6/threadpool.cpp
   295344      4 -rwxr-xr-x   1 root     root         1992 Jul 13 10:23 ./WebServer/old_version/old_version_0.1/threadpool.h
   295343      8 -rwxr-xr-x   1 root     root         6063 Jul 13 10:23 ./WebServer/old_version/old_version_0.1/threadpool.cpp
   295415      4 -rwxr-xr-x   1 root     root         1498 Jul 13 10:23 ./WebServer/old_version/old_version_0.5/threadpool.h
   295414      8 -rwxr-xr-x   1 root     root         4627 Jul 13 10:23 ./WebServer/old_version/old_version_0.5/threadpool.cpp
   295375      4 -rwxr-xr-x   1 root     root         2034 Jul 13 10:23 ./WebServer/old_version/old_version_0.3/threadpool.h
   295374      8 -rwxr-xr-x   1 root     root         4772 Jul 13 10:23 ./WebServer/old_version/old_version_0.3/threadpool.cpp
   295359      4 -rwxr-xr-x   1 root     root         1992 Jul 13 10:23 ./WebServer/old_version/old_version_0.2/threadpool.h
   295358      8 -rwxr-xr-x   1 root     root         6063 Jul 13 10:23 ./WebServer/old_version/old_version_0.2/threadpool.cpp
   295394      4 -rwxr-xr-x   1 root     root         2076 Jul 13 10:23 ./WebServer/old_version/old_version_0.4/threadpool.h
   295393      8 -rwxr-xr-x   1 root     root         4501 Jul 13 10:23 ./WebServer/old_version/old_version_0.4/threadpool.cpp
gatsby@ubuntu:~$ find . -type f -mmin -10 // 最近10分钟更新的文件，type -f表示普通文件
./.mozilla/firefox/nuds3s6z.default/permissions.sqlite
./.mozilla/firefox/nuds3s6z.default/AlternateServices.txt
./.mozilla/firefox/nuds3s6z.default/prefs.js
./.mozilla/firefox/nuds3s6z.default/datareporting/archived/2021-07/1626192031061.cd23406a-060b-4fdd-8f80-98acefb693d2.main.jsonlz4
./.mozilla/firefox/nuds3s6z.default/datareporting/aborted-session-ping
./.mozilla/firefox/nuds3s6z.default/datareporting/session-state.json
./.mozilla/firefox/nuds3s6z.default/datareporting/glean/db/data.safe.bin

```



locate [OPTION]... [PATTERN]...

locate命令其实是“find -name”的另一种写法，但是要比后者快得多，原因在于它不搜索具体目录，而是搜索一个数据库（/var/lib/locatedb），这个数据库中含有本地所有文件信息。**Linux系统自动创建这个数据库，并且每天自动更新一次，所以使用locate命令查不到最新变动过的文件。为了避免这种情况，可以在使用locate之前，先使用updatedb命令，手动更新数据库。**



whereis 

whereis命令只能用于程序名的搜索，而且只搜索二进制文件（参数-b）、man说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。

```shell
gatsby@ubuntu:~/Desktop/git$ whereis stress
stress: /usr/bin/stress /usr/share/man/man1/stress.1.gz /usr/share/info/stress.info.gz
```



which

which命令的作用是，在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。也就是说，使用which命令，就可以看到某个系统命令是否存在，以及执行的到底是哪一个位置的命令。

```shell
gatsby@ubuntu:~/Desktop/git$ which stress
/usr/bin/stress
```



# C++

在Linux系统下，并不是依靠文件的后缀去选择相对应的执行程序，因此Linux的后缀其实只是用来给程序员看的，用于识别这是什么程序。

### GCC

在Linux下可以先查看是否已经预置了GCC编译器

```shell
rpm -q | grep gcc
apt list | grep gcc
```

如果没有安装的话

```shell
yum -y install gcc
sudo apt install gcc
```

GCC中的组成主要是C++编译器、源码预处理程序、库文件。

编译的过程中主要分为四步骤：

1. 预处理 

   ```shell
   gcc -E Test.c -o Test.i
   ```

2. 编译成汇编代码 

   ```shell
   gcc -S Test.i -o Test.s
   ```

3. 汇编 

   ```shell
   gcc -c Test.s -o Test.o
   ```

4. 链接生成可执行文件 

   ```shell
   gcc Test.o -o Test
   ```

一步到位：

```shell
gcc main.c -o main
```

 直接生成的可执行文件就是main， 然后

```shell
./main
```

不要加空格，否则就是进入目录。只需要这样就可以执行这个可执行文件，输出将会输出在命令行。

编译的时候可以加上-Wall，用于输出错误和提示的参数。
