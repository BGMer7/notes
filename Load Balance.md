# Load Balance 

## Introduction

当大型网站发展到一定的程度的时候，要面对庞大的用户量，高并发，海量数据等等挑战，为了提升系统的的性能，可以采用垂直扩展和水平扩展两种方式。

### 垂直扩展（提升性能）

在网站发展早期，可以从单机的角度通过增加硬件处理能力，比如 CPU 处理能力，内存容量，磁盘等方面，实现服务器处理能力的提升。但是，单机是有性能瓶颈的，一旦触及瓶颈，再想提升，付出的成本和代价会极高。这显然不能满足大型分布式系统（网站）所有应对的大流量，高并发，海量数据等挑战。

### 水平扩展（提升数量）

通过集群来分担大型网站的流量。集群中的应用服务器（节点）通常被设计成无状态，用户可以请求任何一个节点，这些节点共同分担访问压力。水平扩展有两个要点：

1. 应用集群：将一台应用部署到多台机器上，组成处理集群，接收负载均衡设备分发的请求，进行处理，并返回相应的数据。
2. 负载均衡：将用户的访问请求，通过某种算法分发到集群中的节点。

### LB的主要作用

目标是将网络流量平均分发到多个服务器上，以提高系统整体的相应速度和可用性。

> **负载均衡的主要作用：**
>
> **高并发(Concurrence)：**负载均衡通过算法调整负载，尽力均匀的分配应用集群中各节点的工作量，以此提高应用集群的并发处理能力（吞吐量）。
>
> **伸缩性(Elastic)：**增加或减少服务器数量，然后由负载均衡进行分发控制。这使得应用集群具备伸缩性。
>
> **高可用(HA)：**负载均衡器可以监控候选服务器，当服务器不可用时，自动跳过，将请求分发给可用的服务器。这使得应用集群具备高可用的特性。
>
> **安全防护(Security)：**有些负载均衡软件或硬件提供了安全性功能，如：黑白名单处理、防火墙，防DDoS攻击等。

## Categories

从负载均衡的载体来看，可以将负载均衡分为两类：**硬件负载均衡和软件负载均衡**

[深入浅出负载均衡 - 掘金 (juejin.cn)](https://juejin.cn/post/6970950695117651976)

### 硬件负载均衡

硬件负载均衡，一般是在定制处理器上运行的独立负载均衡服务器，价格昂贵，土豪专属。硬件负载均衡的主流产品有:F5 和 A10。

**硬件负载均衡的 优点：**

- 功能强大：支持全局负载均衡并提供较全面的、复杂的负载均衡算法。
- 性能强悍：硬件负载均衡由于是在专用处理器上运行，因此吞吐量大，可支持单机百万以上的并发。
- 安全性高：往往具备防火墙，防DDoS攻击等安全功能。

**硬件负载均衡的 缺点：**

- 成本昂贵：购买和维护硬件负载均衡的成本都很高。
- 扩展性差：当访问量突增时，超过限度不能动态扩容。

### 软件负载均衡

软件负载均衡，应用最广泛。

软件负载均衡从软件层面实现负载均衡，一般可以在任何标准物理设备上运行。

软件负载均衡主流产品：Nginx、HAProxy、LVS

- LVS 可以作为四层负载均衡器。其负载均衡的性能要优于 Nginx。
- HAProxy 可以作为 HTTP 和 TCP 负载均衡器。
- Nginx、HAProxy 可以作为四层或七层负载均衡器。

软件负载均衡的优点：

- 扩展性好：适应动态变化，可以通过添加软件负载均衡实例，动态扩展到超出初始容量的能力。
- 成本低廉：软件负载均衡可以在任何标准物理设备上运行，降低了购买和运维的成本。

软件负载均衡的 缺点：

- 性能略差：相比于硬件负载均衡，软件负载均衡的性能要略低一些。

### 网络通信分类

从网络通信分层的角度看，可以分为四层和七层负载均衡。

四层负载均衡：基于IP地址和端口进行请求的转发。

- DNS 重定向
- HTTP 重定向
- 反向代理

七层负载均衡：就是可以根据访问用户的 HTTP 请求头、URL 信息将请求转发到特定的主机。

- 修改 IP 地址
- 修改 MAC 地址

#### DNS负载均衡

DNS 负载均衡一般用于互联网公司，复杂的业务系统不适合使用。大型网站一般使用 DNS 负载均衡作为 第一级负载均衡手段，然后在内部使用其它方式做第二级负载均衡。DNS 负载均衡属于七层负载均衡。

DNS即域名解析系统，是OSI第七层网络协议，DNS被设计为一个树形结构的分布式应用，自上而下依次为：根服务服务器，一级域名服务器，二级域名服务器...本地域名服务器。显然，如果所有的数据都存储在根服务服务器，那么DNS查询的负载和开销会非常庞大。

因此，DNS查询相对于DNS，是一个逆向的递归过程，DNS客户端依次请求本地DNS服务器，上一级DNS服务器，上上一级DNS服务器，...，根DNS服务器（又叫权威DNS服务器），一旦命中，立即返回。为了减少查询次数，每一级DNS都会设置DNS查询缓存地址。

DNS负载均衡的工作原理就是：基于DNS查询缓存，按照负载情况返回不同服务器的 IP 地址。













#### HTTP负载均衡







#### 反向代理（Reverse Proxy）

反向代理（Reverse Proxy）方式是指以 代理服务器 来接受网络请求，然后 将请求转发给内网中的服务器，并将从内网中的服务器上得到的结果返回给网络请求的客户端。反向代理负载均衡属于七层负载均衡。

反向代理服务的主流产品：**Nginx、Apache**。

正向代理与反向代理有什么区别？

> 正向代理发生在客户端，是由用户主动发起的，翻墙软件就属于典型的主动代理，客户端通过主动访问代理服务器，让代理服务器去访问需要的外网数据，再将数据返回给客户端。
>
> 反向代理发生在服务端，用户并不知道代理的存在，但是服务器会将请求分发给别的服务器，别的服务器再返回数据给这个服务器，最终将数据返回客户端，客户端至始至终只是和一个服务器打交道。













#### IP负载均衡











### 随机









### 轮询

#### 轮询算法





#### 加权轮询算法









### 最小活跃数







### IP哈希































## Nginx

[Nginx和F5区别 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1404473)

Nginx：高性能的 HTTP和反向代理服务器，同时支持作为IMAP/POP3/SMTP代理服务器。

例如腾讯、淘宝、新浪等大型门户及商业网站都采用Nginx进行HTTP网站的数据分流。

### Nginx功能特点

1. 工作在网络层第七层，可以针对http应用做一些分流，例如针对域名、针对目录
2. Nginx对于网络的依赖比较小
3. Nginx安装和配置比较简单，测试起来比较方便
4. 可以承担高的负载压力且稳定，一般能承受超过1万次的并发
5. Nginx可以通过端口检测到服务器内部的故障，比如根据服务器处理网页返回的状态码、超时等等，并且会把返回错误的请求重新提交到另一个节点，不过其中缺点就是不支持url来检测；
6. Nginx对请求的异步处理可以帮助节点服务器减轻负载
7. Nginx能支持http和email

### 软负载

优点：基于系统与应用的负载均衡，能够更好地根据系统与应用的状况来分配负载。这对于复杂应用是很重要的，性价比高，实际上如果几台服务器，用F5之类的硬件产品显得有些浪费，而用软件就要合算得多，因为服务器同时还可以跑应用、做集群等。

缺点：负载能力受服务器本身性能的影响，性能越好，负载能力越大。



### 反向代理

Nginx采用的是反向代理技术，代理服务器来接受Internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个服务器。反向代理负载均衡技术是把将来自internet上的连接请求以反向代理的方式动态地转发给内部网络上的多台服务器进行处理，从而达到负载均衡的目的。

具体是怎么运行的呢？其实当Nginx启动后，其工作进程是由配置文件对其进行初始化的，主进程处理配置文件中的读取、端口绑定等特权操作，之后创建一小组子进程，由这些子进程进行请求的处理，同时缓存加载器加载硬盘中缓存到内存中，接着退出，保证资源开销始终保持着较低的状态。





### 负载均衡策略

内置策略为轮询、加权轮询、IP哈希。







### Nginx配置详解

![img](https://img-blog.csdnimg.cn/20210731155655187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTAyMDMxMjg=,size_16,color_FFFFFF,t_70)

[Nginx配置文件配置详解](https://blog.csdn.net/tjcyjd/article/details/50695922?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0.pc_relevant_default&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

默认的nginx配置结构如下：

```shell
...              #全局块

events {         #events块
   ...
}

http      #http块
{
    ...   #http全局块
    server        #server块
    { 
        ...       #server全局块
        location [PATTERN]   #location块
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    ...     #http全局块
}
```

- **全局块**：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
- **events块**：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
- **http块**：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
- **server块**：配置虚拟主机的相关参数，一个http中可以有多个server。
- **location块**：配置请求的路由，以及各种页面的处理情况。



默认的nginx.conf如下：

```shell
#user  nobody;
worker_processes  1;
 
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
 
#pid        logs/nginx.pid;
 
 
events {
    worker_connections  1024;
}
 
 
http {
    include       mime.types;
    default_type  application/octet-stream;
 
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
 
    #access_log  logs/access.log  main;
 
    sendfile        on;
    #tcp_nopush     on;
 
    #keepalive_timeout  0;
    keepalive_timeout  65;
 
    #gzip  on;
 
    server {
        listen       80;
        server_name  localhost;
 
 
 
        location / {
            root   html;
            index  index.html index.htm;
        }
 
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
 
 
    }
 
 
    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;
 
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
 
 
    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;
 
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;
 
    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;
 
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;
 
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
 
}
```

依次详解相关的conf配置：

1. `#user root;`

   Nginx用户及组，设置worker进程的用户，指的linux中的用户，会涉及到nginx操作目录或文件的一些权限，默认为nobody

2. `worker_processes 1;`

   worker进程工作数设置，一般来说CPU有几个，就设置几个，或者设置为N-1也行

3. nginx 日志级别debug | info | notice | warn | error | crit | alert | emerg，错误级别从左到右越来越大

   error_log logs/error.log; 

   error_log logs/error.log notice; 

   error_log logs/error.log info; 

   错误日志：存放路径。

4. `pid logs/nginx.pid;`

   设置nginx进程的pid，存放路径

5. `worker_rlimit_nofile 204800;`

   指定进程可以打开的最大的fd数量

   注：这个指令是指当一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（ulimit -n）与nginx进程数相除，但是nginx分配请求并不是那么均匀，所以最好与ulimit -n 的值保持一致。

6. `events {use epolls}`

   Linux推荐使用epoll模型，FreeBSD建议使用kqueue，windows平台不指定。

   > 事件模型：
   >
   > A）标准事件模型：
   >
   > select、poll属于标准事件模型，如果当前系统不存在更有效的方法，nginx会选择select或者poll
   >
   > B）高效事件模型：
   >
   > https://www.pianshen.com/article/6950957291/
   >
   > Kqueue：
   >
   > epoll：

7. `worker_connections 204800;`每个工作进程的最大连接数量。

8. `keepalive_timeout 60;`keepalive超时时间

7. `gzip  on;`启用压缩，压缩之后的html/js/css传输会更快

```shell
########### 每个指令必须有分号结束。#################
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;  #允许生成的进程数，默认为1
#pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
events {
    accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;    #最大连接数，默认为512
}
http {
    include       mime.types;   #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。

    upstream mysvr {   
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    error_page 404 https://www.baidu.com; #错误页
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
}
```



### Nginx端口转发

#### 将域名转发到本地端口

首先介绍最常用的，将域名转发到本地另一个端口上

```shell
server{
  listen 80;
  server_name  gatsby.com;
  index  index.php index.html index.htm;

  location / {
    proxy_pass  http://127.0.0.1:8080; # 转发规则
    proxy_set_header Host $proxy_host; # 修改转发请求头，让8080端口的应用可以受到真实的请求
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

监听gatsby.com:80端口，转发到本地的8080端口。



#### 将域名转发到另一个域名

```conf
server{
  listen 80;
  server_name  https://google.com;
  index  index.php index.html index.htm;

  location / {
    proxy_pass  https://www.baidu.com;
    proxy_set_header Host $proxy_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

这样访问google的请求就会被转发到baidu.com



#### 将本地端口转发到另一个域名

```text
server{
  listen 80;
  server_name 127.0.0.1; # 公网ip
  index  index.php index.html index.htm;

  location / {
    proxy_pass  http://127.0.0.1:8080; # 或 http://www.baidu.com
    proxy_set_header Host $proxy_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

这样本地请求的IP就会转发到公网或者baidu。



#### 转发路径

在配置proxy_pass代理转发时，如果后面的url加/，表示绝对根路径；如果没有/，表示相对路径

```text
server_name google.com
location /data/ {
    proxy_pass http://127.0.0.1/;
}
```

表示google.com/data/index.html等页面的请求会转发到本地http://127.0.0.1/index.html

如果是在转发路径后没有加斜杠的，表示相对路径

```text
server_name shaochenfeng.com
location /data/ {
    proxy_pass http://127.0.0.1;
}
```

表示google.com/data/index.html等页面的请求会转发到本地http://127.0.0.1/data/index.html




## CentOS下Nginx安装

[Centos 安装Nginx - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1924770)

### 有yum源

1. 由于Nginx是基于C/C++编写的框架，所以需要有gcc/g++环境，一般来说Linux都会包含，如果没有的话需要用yum安装

   ```shell
   yum install -y gcc gcc-c++
   ```

2. 安装Nginx需要有三个前置依赖包：pcre、zlib、openssl

   ```shell
   yum install -y zlib pcre openssl
   ```

   这三个模块直接通过yum源安装即可。



### 无yum源

如果没有互联网访问，那我们就只能将压缩包手动上传至服务器，将pcre、zlib、openssl、nginx四个压缩包上传到服务器，放在同一个目录下；

```shell
[root@localhost nginx-install]# ll
total 18308
-rw-r--r-- 1 root root  1060580 Nov 11 09:58 nginx-1.19.9.tar.gz
-rw-r--r-- 1 root root 14978663 Nov 11 10:12 openssl-3.0.0.tar.gz
-rw-r--r-- 1 root root  2096552 Nov 11 10:05 pcre-8.45.tar.gz
-rw-r--r-- 1 root root   607698 Nov 11 10:12 zlib-1.2.11.tar.gz
```

把四个压缩包都解压

```shell
[root@localhost nginx-install]# tar -zxvf nginx-1.19.9.tar.gz
[root@localhost nginx-install]# tar -zxvf pcre-8.45.tar.gz
[root@localhost nginx-install]# tar -zxvf openssl-3.0.0.tar.gz
[root@localhost nginx-install]# tar -zxvf zlib-1.2.11.tar.gz

[root@localhost nginx-install]# ll
total 18328
drwxr-xr-x  8 1001  1001      158 Mar 30  2021 nginx-1.19.9
-rw-r--r--  1 root root   1060580 Nov 11 09:58 nginx-1.19.9.tar.gz
drwxrwxr-x 20 root root      4096 Sep  7 19:46 openssl-3.0.0
-rw-r--r--  1 root root  14978663 Nov 11 10:12 openssl-3.0.0.tar.gz
drwxr-xr-x  7 1169  1169     8192 Jun 16  2021 pcre-8.45
-rw-r--r--  1 root root   2096552 Nov 11 10:05 pcre-8.45.tar.gz
drwxr-xr-x 14  501 games     4096 Jan 16  2017 zlib-1.2.11
-rw-r--r--  1 root root    607698 Nov 11 10:12 zlib-1.2.11.tar.gz
```

执行编译

```shell
[root@localhost nginx-install]# cd pcre-8.45/
[root@localhost pcre-8.45]# ./configure
[root@localhost pcre-8.45]# make
[root@localhost pcre-8.45]# make install
[root@localhost pcre-8.45]# cd ../openssl-3.0.0/
[root@localhost openssl-3.0.0]# ./config
[root@localhost openssl-3.0.0]# make
[root@localhost openssl-3.0.0]# make install
[root@localhost openssl-3.0.0]# cd ../zlib-1.2.11/
[root@localhost zlib-1.2.11]# ./configure
[root@localhost zlib-1.2.11]# make
[root@localhost zlib-1.2.11]# make install
[root@localhost zlib-1.2.11]# cd ../nginx-1.19.9/
[root@localhost nginx-1.19.9]# ./configure
[root@localhost nginx-1.19.9]# make
[root@localhost nginx-1.19.9]# make install

[root@localhost nginx-1.19.9]# whereis nginx
nginx: /usr/local/nginx

[root@localhost nginx-1.19.9]# cd /usr/local/nginx

[root@localhost nginx]# ll
total 0
drwxr-xr-x 2 root root 333 Feb  9 15:22 conf
drwxr-xr-x 2 root root  40 Feb  9 15:22 html
drwxr-xr-x 2 root root   6 Feb  9 15:22 logs
drwxr-xr-x 2 root root  19 Feb  9 15:22 sbin

[root@localhost nginx]# cd conf/
[root@localhost conf]# ll
total 68
-rw-r--r-- 1 root root 1077 Feb  9 15:22 fastcgi.conf
-rw-r--r-- 1 root root 1077 Feb  9 15:22 fastcgi.conf.default
-rw-r--r-- 1 root root 1007 Feb  9 15:22 fastcgi_params
-rw-r--r-- 1 root root 1007 Feb  9 15:22 fastcgi_params.default
-rw-r--r-- 1 root root 2837 Feb  9 15:22 koi-utf
-rw-r--r-- 1 root root 2223 Feb  9 15:22 koi-win
-rw-r--r-- 1 root root 5231 Feb  9 15:22 mime.types
-rw-r--r-- 1 root root 5231 Feb  9 15:22 mime.types.default
-rw-r--r-- 1 root root 2656 Feb  9 15:22 nginx.conf
-rw-r--r-- 1 root root 2656 Feb  9 15:22 nginx.conf.default
-rw-r--r-- 1 root root  636 Feb  9 15:22 scgi_params
-rw-r--r-- 1 root root  636 Feb  9 15:22 scgi_params.default
-rw-r--r-- 1 root root  664 Feb  9 15:22 uwsgi_params
-rw-r--r-- 1 root root  664 Feb  9 15:22 uwsgi_params.default
-rw-r--r-- 1 root root 3610 Feb  9 15:22 win-utf

[root@localhost conf]# cd ../sbin/
[root@localhost sbin]# ll
total 3792
-rwxr-xr-x 1 root root 3881592 Feb  9 15:22 nginx
```

nginx的命令

```shell
./nginx 
./nginx -s stop
./nginx -s quit // 优雅关闭
./nginx -s reload
```

查看nginx进程

```shell
[root@localhost sbin]# ps aux|grep nginx
root     27588  0.0  0.0  20572   612 ?        Ss   15:32   0:00 nginx: master process ./nginx
nobody   27589  0.0  0.0  23092  1612 ?        S    15:32   0:00 nginx: worker process
```

nginx启动之后，通过浏览器访问该服务器的IP地址，页面显示

># Welcome to nginx!
>
>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
>
>For online documentation and support please refer to [nginx.org](http://nginx.org/).
>Commercial support is available at [nginx.com](http://nginx.com/).
>
>*Thank you for using nginx.*

即为安装成功。































