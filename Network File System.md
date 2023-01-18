# Network File System

## NFS工作原理

NFS 是 **N**etwork **F**ile **S**ystem 的缩写，是Sun公司于1984开发的一种分布式文件系统协议。它的核心功能就是可以通过网络，让不同的客户端，可以彼此访问共同的文件系统 ，来实现文件的共享。像许多其他的协议，建立在开放的网络计算的远程过程调用（RPC）之上。NFS是在RFC中定义的开放标准，允许任何人实现该协议。

NFS 服务器可以让客户端将网络远程的 NFS 服务器分享的目录，直接挂载到本地端的机器当中。本地端的机器通过直接读写挂载的目录，就可以同步到NFS服务器之上。

## NFS应用场景

- 多个机器共享一台CDROM或其他设备。这对于在多台机器中安装软件来说更加便宜与方便
- 在大型网络中，配置一台中心NFS服务器用来放置所有用户的home目录可能会带来便利。这些目录能被输出到网络以便用户不管在哪台工作站上登录，总能得到相同的home目录
- 不同客户端可在NFS上观看影视文件，节省本地空间
- 在客户端完成的工作数据，可以备份保存到NFS服务器上用户自己的路径下



## NFS体系

nfs体系至少有两个主要部分：

- 一台nfs服务器
- 若干台客户机

客户机通过TCP/IP网络远程访问存放在NFS服务器上的数据
在NFS服务器正式启用前，需要根据实际环境和需求，配置一些NFS参数



## NFS工作机制

### RPC

RPC（Remote Procedure Call Protocol），远程过程调用协议。

RPC要解决的两个问题： 

1. 解决分布式系统中，服务之间的调用问题。 
2. 远程调用时，要能够像本地调用一样方便，让调用者感知不到远程调用的逻辑。

RPC的核心目的是为了允许调用本地服务一样调用远程服务。

在RPC中，如果有两台服务器A和B，一个应用部署在A，一个应用部署在B，想要调用B服务器上应用提供的函数方法，由于不在一个内存空间，并不能直接调用，需要通过网络来表达调用的语义和传达调用的数据。

1. 需要解决通讯的问题

   主要是通过在客户端和服务器之间建立TCP连接（Socket），远程调用中的所有的交换的数据都在这个socket连接里执行。连接可以是按需连接也可以是长连接，多个远程过程调用共享一个连接。

2. 需要解决寻址的问题

   也就是说，A服务器上的应用怎么告诉底层的RPC框架，如何连接到B服务器（如主机或IP地址）以及特定的端口，方法的名称名称是什么，这样才能完成调用。比如基于Web服务协议栈的RPC，就要提供一个endpoint URI，或者是从UDDI服务上查找。如果是RMI调用的话，还需要一个RMI Registry来注册服务的地址。

3. 当A服务器的应用发起了远程过程调用

   方法的参数需要通过底层的网络协议如TCP传递到B服务器，由于网络协议是基于二进制的，内存中的参数的值要序列化成二进制的形式，也就是**序列化（Serialize）**或编组（marshal），通过寻址和传输将序列化的二进制发送给B服务器。

4. 当B服务器收到请求后

   需要对参数反序列化，恢复为内存中的表达方式，然后找到对应的方法（寻址的一部分）进行本地调用，然后得到返回值。

5. 将返回值返回给服务器A

   在网络中也要通过序列化的方式发送，同理，服务器A接收到消息之后再次进行反序列化，交给A服务器上的应用。



> RPC和HTTP
>
> *为何有HTTP之后，还要RPC调用？*
>
>  RPC跟HTTP不是对立面，RPC中可以使用HTTP作为通讯协议。**RPC是一种设计、实现框架，通讯协议只是其中一部分。**
> 
>  **RPC的本质是提供了一种轻量无感知的跨进程通信的方式**，在分布式机器上调用其他方法与本地调用无异（远程调用的过程是透明的，你并不知道这个调用的方法是部署在哪里，通过PRC能够解耦服务）。RPC是根据语言的API来定义的，而不是基于网络的应用来定义的，调用更方便，协议私密更安全、内容更小效率更高。
> 
>  HTTP接口是在接口不多、系统与系统交互较少的情况下，解决信息孤岛初期常使用的一种通信手段；优点就是简单、直接、开发方便。利用现成的HTTP协议 进行传输。但是如果是一个大型的网站，内部子系统较多、接口非常多的情况下，RPC框架的好处就显示出来了，首先（基于TCP协议的情况下）就是长链接，不必每次通信都要像HTTP一样去3次握手什么的，减少了网络开销；其次就是RPC框架一般都有注册中心，有丰富的监控管理；发布、下线接口、动态扩展等，对调用方来说是无感知、统 一化的操作。第三个来说就是安全性。最后就是最近流行的服务化架构、服务化治理，RPC框架是一个强力的支撑。



![img](https://pic4.zhimg.com/v2-a58ac48f833b833d7604d902de46ce83_r.jpg)

在NFS中，RPC将全权负责网络通信，这样一来，网络程序的设计就变得简单了许多，跟本地程序一样简单，通信效率也会有所提升。

当调用 RPC 的服务器程序启动时，相关的进程就会向 RPC 发起注册。RPC 就会开启特定的端口来为客户端提供服务。

由于端口号可能是不固定的，因此 RPC 服务器必须提供一种查询服务端口的方法。

RPC 服务器上有一个叫端口映射器的东西，它固定监听 `UDP 111` 端口。RPC 客户端通过访问这个端口就可以查询到对应服务所使用的端口。

使用 RPC 协议的网络程序在服务器和客户端上都必须借助 RPC 服务才可相互通信。



### 工作进程

nfsd: nfs的守护进程，监听在2049/tcp和2049/udp端口上, 不负责文件存储（由NFS服务器本地内核负责调度存储），用于理解客户端发起的rpc请求，并将其转交给本地内核，而后存储在指定的文件系统上

mountd: 用于验证客户端是否在允许访问此NFS文件系统的客户端列表中，在则允许访问（发放一个令牌，持令牌去找nfsd），否则拒绝访问

idmapd: 实现用户帐号的集中映射，把所有的帐号都映射为NFSNOBODY，但是在访问时却能以本地用户的身份去访问

portmapper: NFS服务器的rpc服务，其监听于111/TCP和111/UDP套接字上，用于管理远程过程调用（RPC）



![img](https://pic4.zhimg.com/v2-a8a1d6bb645abffdf64530b2f847baf3_r.jpg)





## NFS安装步骤

[Linux 环境下 NFS 服务安装及配置使用 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1433259#:~:text=通过 NFS，我们本地 NFS 的客户端应用可以透明地读写位于服务端 NFS,服务器 上的文件，就像访问本地文件一样方便。 简单的理解，NFS 就是可以透过网络，让不同的主机、不同的操作系统可以 共享存储 的服务。)

通过上边简要的介绍，我们知道 NFS 服务需要依赖 RPC 服务，所以这里 NFS 服务端需要安装 `rpcbind` 和 `nfs-utils`，客户端只需要安装 `nfs-utils` 即可，由于我选用的为 CentOS 系统，所以可以使用 yum 快速的安装。

首先，确认下服务端系统是否已安装 NFS。

```shell
$ rpm -qa nfs-utils rpcbind
nfs-utils-1.3.0-0.54.el7.x86_64
rpcbind-0.2.0-38.el7.x86_64
```

这里我已经安装完毕，若为空，则说明未安装。

然后，安装 NFS 服务

```shell
$ yum install rpc*
$ yum install nfs*
```

NFS配置及使用

我们在服务端创建一个共享目录 `/data/share` ，作为客户端挂载的远端入口，然后设置权限。

```shell
$ mkdir -p /data/share
$ chmod 666 /data/share
```

然后，修改 NFS 配置文件 `/etc/exports`

```shell
$ vim /etc/exports
/data/share 10.222.77.0/24(rw,sync,insecure,no_subtree_check,no_root_squash)
```

说明一下，这里配置后边有很多参数，每个参数有不同的含义，具体可以参考下边。此处，我配置了将 `/data/share` 文件目录设置为允许 IP 为该 `10.222.77.0/24` 区间的客户端挂载，当然，如果客户端 IP 不在该区间也想要挂载的话，可以设置 IP 区间更大或者设置为 `*` 即允许所有客户端挂载，例如：`/home *(ro,sync,insecure,no_root_squash)` 设置 `/home` 目录允许所有客户端只读挂载。

说明一下，这里配置后边有很多参数，每个参数有不同的含义，具体可以参考下边。此处，我配置了将 `/data/share` 文件目录设置为允许 IP 为该 `10.222.77.0/24` 区间的客户端挂载，当然，如果客户端 IP 不在该区间也想要挂载的话，可以设置 IP 区间更大或者设置为 `*` 即允许所有客户端挂载，例如：`/home *(ro,sync,insecure,no_root_squash)` 设置 `/home` 目录允许所有客户端只读挂载。

| 参数             | 说明                                                         |
| :--------------- | :----------------------------------------------------------- |
| ro               | 只读访问                                                     |
| rw               | 读写访问                                                     |
| sync             | 所有数据在请求时写入共享                                     |
| async            | nfs 在写入数据前可以响应请求                                 |
| secure           | nfs 通过 1024 以下的安全 TCP/IP 端口发送                     |
| insecure         | nfs 通过 1024 以上的端口发送                                 |
| wdelay           | 如果多个用户要写入 nfs 目录，则归组写入（默认）              |
| no_wdelay        | 如果多个用户要写入 nfs 目录，则立即写入，当使用 async 时，无需此设置 |
| hide             | 在 nfs 共享目录中不共享其子目录                              |
| no_hide          | 共享 nfs 目录的子目录                                        |
| subtree_check    | 如果共享 /usr/bin 之类的子目录时，强制 nfs 检查父目录的权限（默认） |
| no_subtree_check | 不检查父目录权限                                             |
| all_squash       | 共享文件的 UID 和 GID 映射匿名用户 anonymous，适合公用目录   |
| no_all_squash    | 保留共享文件的 UID 和 GID（默认）                            |
| root_squash      | root 用户的所有请求映射成如 anonymous 用户一样的权限（默认） |
| no_root_squash   | root 用户具有根目录的完全管理访问权限                        |
| anonuid=xxx      | 指定 nfs 服务器 /etc/passwd 文件中匿名用户的 UID             |
| anongid=xxx      | 指定 nfs 服务器 /etc/passwd 文件中匿名用户的 GID             |

接下来，我们先启动 RPC 服务。

```shell
$ service rpcbind start
# 或者使用如下命令亦可
$ /bin/systemctl start rpcbind.service

# 查看 NFS 服务项 rpc 服务器注册的端口列表
$ rpcinfo -p localhost 
   program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    100000    3   udp    111  portmapper
    100000    2   udp    111  portmapper
```

注意：此时我们还没有启动 NFS 服务，只监听了 111 端口，接着我们来启动 NFS 服务，再来看下注册的端口列表。

```shell
# 启动 NFS 服务
$ service nfs start
# 或者使用如下命令亦可
/bin/systemctl start nfs.service

# 启动 NFS 服务后 rpc 服务已经启用了对 NFS 的端口映射列表
# rpcinfo -p localhost
   program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    100000    3   udp    111  portmapper
    100000    2   udp    111  portmapper
    100024    1   udp  33745  status
    100024    1   tcp  36980  status
    100005    1   udp  20048  mountd
    100005    1   tcp  20048  mountd
    100005    2   udp  20048  mountd
    100005    2   tcp  20048  mountd
    100005    3   udp  20048  mountd
    100005    3   tcp  20048  mountd
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    3   tcp   2049  nfs_acl
    100003    3   udp   2049  nfs
    100003    4   udp   2049  nfs
    100227    3   udp   2049  nfs_acl
    100021    1   udp  38960  nlockmgr
    100021    3   udp  38960  nlockmgr
    100021    4   udp  38960  nlockmgr
    100021    1   tcp  38362  nlockmgr
    100021    3   tcp  38362  nlockmgr
    100021    4   tcp  38362  nlockmgr
```

我们发现，启动了 NFS 服务后，rpc 注册的端口列表明显增多。OK 现在服务端都启动起来了，在服务端看下是否正确加载了设置的 `/etc/exports` 配置。

```shell
$ showmount -e localhost
Export list for localhost:
/data/share 10.222.77.0/24
```

最后，在另一台 Linux 虚拟机上测试一下，是否能够正确挂载吧。首先，我们可以在客户端查看下 NFS 服务端 (上边服务端 IP 为：10.222.77.86) 设置可共享的目录信息。

```shell
# showmount -e 10.222.77.86
Export list for 10.222.77.86:
/data/share 10.222.77.0/24
```

然后，在客户端创建挂在目录 `/share`

```shell
$ mount 10.222.77.86:/data/share /share
$ df -h | grep 10.222.77.86
Filesystem                 Size  Used  Avail Use% Mounted on
10.222.77.86:/data/share   27G   11G   17G   40%  /share
```

可以看到，可以正确将远端 NFS 目录挂载到本地。注意：挂载点 `/share` 目录必须已经存在，而且目录中没有文件或子目录。

最后，我们在 NFS 服务端 `/data/share` 目录下创建一个文件，看下客户端是否能够正确读取并修改。

```shell
# 服务端写入
$ echo "This is NFS server." > /data/share/nfs.txt
# ll /data/share/
total 4
-rw-r--r-- 1 root root 20 Nov  5 16:49 nfs.txt

# 客户端读取
$ ll /share/
total 4
-rw-r--r-- 1 root root 20 Nov  5 16:49 nfs.txt
$ cat /share/nfs.txt
This is NFS server.

# 客户端写入
$ echo "This is NFS client." >> /share/nfs.txt

# 服务端读取
$ cat /data/share/nfs.txt
This is NFS server.
This is NFS client.
```













