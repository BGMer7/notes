# Distributed File System

## MinIO

### MinIO configuration

#### MinIO的硬件配置要求







### MinIO deploy

[Minio的单节点/集群部署及JAVA的SDK访问](https://blog.csdn.net/alanzy123/article/details/115169035)

MinIO可以单点部署，也可以分布式集群部署，部署操作方便，支持扩容。

#### Docker单点部署

首先拉取镜像，将最新版本的MinIO下载到Linux环境

```shell
docker pull minio/minio
```

首先关闭防火墙

```shell
systemctl stop firewalld
```

或者开启端口

```shell
sudo firewall-cmd --zone=public --add-port=9000/tcp --permanent
sudo firewall-cmd --reload
```

启动容器，启动端口为 9000 “-v /mnt/data:/data”，指定宿主机指向容器的存储地址，上传的文件保存在该路径；

“server /data” 启动的命令，指定容器内部存储地址为/data

```shell
docker run -p 9000:9000 --name minio1 \
  -v /mnt/data:/data \
  -v /mnt/config:/root/.minio \
  minio/minio server /data
```

启动成功之后，通过浏览器访问http://ip_address:9000，登录时，选用accessKey和secretKey，docker容器启动默认都是 “minioadmin” 。



#### Linux二进制单点部署

如果可以直接访问外网，可以直接使用命令

```shell
wget https://dl.min.io/server/minio/release/linux-amd64/minio
```

在安装包目录下，启动，指定存储路径是/data

```shell
export MINIO_ACCESS_KEY=minioadmin
export MINIO_SECRET_KEY=minioadmin
chmod +x minio
./minio server /data
```

启动成功， 访问与操作同上。



#### Linux分布式集群部署

[三.minio 的分布式部署、单节点多磁盘、多节点模式](https://blog.csdn.net/xixiyuguang/article/details/119456729)

##### Linux分布式部署的优势

1. 数据保护
   - 分布式 Minio 采用纠删码来防范多个节点宕机和位衰减。
   - 分布式 Minio 至少需要 4 个节点（4台服务器），使用分布式 Minio 就 自动引入了纠删码功能。
   - 纠删码是一种恢复丢失和损坏数据的数学算法， Minio 采用 Reed-Solomon code 将对象拆分成 N/2 数据和 N/2 奇偶校验块。 这就意味着如果是 12 块盘，一个对象会被分成 6 个数据块、6 个奇偶校验块，你可以丢失任意 6 块盘（不管其是存放的数据块还是奇偶校验块），你仍可以从剩下的盘中的数据进行恢复。
   - 纠删码的工作原理和 RAID 或者复制不同，像 RAID 可以在损失两块盘的情况下不丢数据，而 Minio 纠删码可以在丢失一半的盘的情况下，仍可以保证数据安全。 而且 Minio 纠删码是作用在对象级别，可以一次恢复一个对象，而RAID 是作用在卷级别，数据恢复时间很长。 Minio 对每个对象单独编码，存储服务一经部署，通常情况下是不需要更换硬盘或者修复。Minio 纠删码的设计目标是为了性能和尽可能的使用硬件加速。
   - 位衰减又被称为数据腐化 Data Rot、无声数据损坏 Silent Data Corruption ，是目前硬盘数据的一种严重数据丢失问题。硬盘上的数据可能会神不知鬼不觉就损坏了，也没有什么错误日志。正所谓明枪易躲，暗箭难防，这种背地里犯的错比硬盘直接故障还危险。 所以 Minio 纠删码采用了高速 HighwayHash 基于哈希的校验和来防范位衰减。
2. 高可用
   - 单机 Minio 服务存在单点故障，相反，如果是一个 N 节点的分布式 Minio ,只要有 N/2 节点在线，你的数据就是安全的。不过你需要至少有 N/2+1 个节点来创建新的对象。
   - 例如，一个 8 节点的 Minio 集群，每个节点一块盘，就算 4 个节点宕机，这个集群仍然是可读的，不过你需要 5 个节点才能写数据。
3. 限制
   - 分布式 Minio 单租户存在最少 4 个盘最多 16 个盘的限制（受限于纠删码）。这种限制确保了 Minio 的简洁，同时仍拥有伸缩性。如果你需要搭建一个多租户环境，你可以轻松的使用编排工具（Kubernetes）来管理多个Minio实例。
   - 注意，只要遵守分布式 Minio 的限制，你可以组合不同的节点和每个节点几块盘。比如，你可以使用 2 个节点，每个节点 4 块盘，也可以使用 4 个节点，每个节点两块盘，诸如此类。
4. 一致性
   - Minio 在分布式和单机模式下，所有读写操作都严格遵守 read-after-write 一致性模型。

##### 分布式部署的步骤

> MinIO可以保证高可用的问题，但是单机Minio服务存在单点故障。
>
> 相反，如果是一个有N块硬盘的分布式Minio,只要有N/2硬盘在线，你的数据就是安全的。
>
> 不过你**需要至少有N/2+1个硬盘来创建新的对象**。
>
> **分布式Minio至少需要4个硬盘**，使用分布式Minio自动引入了纠删码功能

以多节点多硬盘不可扩容的方式部署，使用官方示例：

>分部署部署最少需要四个示例，不管是四个服务器，每台部署一个实例。还是一个服务器部署四个实例。
>存储的文件会以n/2个节点内存分配到每个实例上，比如创建8个实例，上传的文件大小80MB，那么每个实例存在20MB



#### 分布式部署实际操作

[Minio分布式集群搭建 | DevOps (centoscn.vip)](https://www.centoscn.vip/1480.html)

CentOS 7，使用两台服务器，每台服务器挂载两块磁盘，模拟四个节点。

```shell
[root@localhost minio]# cat /etc/redhat-release
CentOS Linux release 7.9.2009 (Core)

ip: 10.189.66.196
ip: 10.189.66.197
```



##### 磁盘的挂载

首先创建两个用于存放MinIO存储数据的目录，

```shell
[root@minio1 ~]# mkdir -p /home/data1
[root@minio1 ~]# mkdir -p /home/data2
[root@minio2 ~]# mkdir -p /home/data1
[root@minio2 ~]# mkdir -p /home/data2
```

这两个目录创建好之后分别挂载一个磁盘。

磁盘的挂载是一个老生常谈的问题了，通过`df -h`可以查看已经挂载的目录，也就是整个系统的文件系统。

通过`fdisk - l`可以查看目前服务器上的所有装置分区，可以通过`fdisk -l`查看

```shell
[root@localhost minio]# fdisk -l

Disk /dev/sda: 107.4 GB, 107374182400 bytes, 209715200 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000c01ce

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200   209469439   103685120   8e  Linux LVM

Disk /dev/sdb: 107.4 GB, 107374182400 bytes, 209715200 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xfc308637

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048   209715199   104856576   83  Linux

Disk /dev/mapper/centos-root: 97.7 GB, 97710505984 bytes, 190840832 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 8455 MB, 8455716864 bytes, 16515072 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sdc: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x71c2e252

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    20971519    10484736   83  Linux

Disk /dev/sdd: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x32828e30

   Device Boot      Start         End      Blocks   Id  System
/dev/sdd1            2048    20971519    10484736   83  Linux
```

此时是已经挂载了的状态，如果有空的还没有挂载的磁盘，例如该磁盘名叫`/dev/sde1`，那么就使用命令`fdisk /dev/sde1`进入磁盘挂载流程，根据提示输n、p、1、1、wq，回车，创建分区。

然后将该分区格式化，为挂载做准备。

```shell
mkfs.ext4 /dev/sde1
```

直接挂载，在上述例子中，已经将`/dev/sdc1`和`/dev/sdd1`挂载在两个目录下，使用的命令是

```shell
[root@minio1 ~]# mount /dev/sdc1 /home/data1
[root@minio1 ~]# mount /dev/sdd1 /home/data2
```

挂载结束之后就可以通过`df -h`看到在文件系统中已经有了挂载

```shell
[root@minio1 ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 7.9G     0  7.9G   0% /dev
tmpfs                    7.9G     0  7.9G   0% /dev/shm
tmpfs                    7.9G  800M  7.1G  10% /run
tmpfs                    7.9G     0  7.9G   0% /sys/fs/cgroup
/dev/mapper/centos-root   91G   17G   75G  18% /
/dev/sda1               1014M  141M  874M  14% /boot
tmpfs                    1.6G     0  1.6G   0% /run/user/0
overlay                   91G   17G   75G  18% /var/lib/docker/overlay2/00af9b52653d18671c207aadb8e4f43a2a961b440ccef05d0d5f5aeb9582a1f7/merged
/dev/sdb1                296G  1.5G  279G   1% /data
/dev/sdc1                9.8G   39M  9.2G   1% /home/data1
/dev/sdd1                9.8G   39M  9.2G   1% /home/data2
```

磁盘挂载完成。



##### 创建脚本目录

`/opt/minio`是启动脚本目录

`/etc/minio`是集群配置目录

```shell
[root@minio1 ~]# mkdir -p /opt/minio
[root@minio2 ~]# mkdir -p /opt/minio


[root@minio1 ~]# mkdir -p /etc/minio
[root@minio2 ~]# mkdir -p /etc/minio
```



##### 下载minio执行文件

由于是内网环境下的测试服务器，无法连接互联网，因此直接下载下来的二进制可执行文件，将文件通过WinSCP上传到服务器。

并存放在`/opt/minio`目录下。



##### 编写启动脚本

其中，“MINIO_ACCESS_KEY”为用户名，“MINIO_SECRET_KEY”为密码，密码不能设置过于简单，不然minio会启动失败，“–config-dir”指定集群配置文件目录

```shell
[root@minio1 ~]# vim /opt/minio/run.sh
#!/bin/bash
export MINIO_ACCESS_KEY=minioAdmin
export MINIO_SECRET_KEY=minioAdmin
 
/opt/minio/minio server --config-dir /etc/minio \
http://10.189.66.196/home/data1 http://10.189.66.196/home/data2 \
http://10.189.66.197/home/data1 http://10.189.66.197/home/data2 \

[root@minio2 ~]# vim /opt/minio/run.sh
#!/bin/bash
export MINIO_ACCESS_KEY=minioAdmin
export MINIO_SECRET_KEY=minioAdmin
 
/opt/minio/minio server --config-dir /etc/minio \
http://10.189.66.196/home/data1 http://10.189.66.196/home/data2 \
http://10.189.66.197/home/data1 http://10.189.66.197/home/data2 \
```

**这里没有输出到log，可以将日志输入到log中**



##### 编写服务脚本

```shell
[root@minio1 ~]# vim /usr/lib/systemd/system/minio.service
[Unit]
Description=Minio service
Documentation=https://docs.minio.io/
 
[Service]
WorkingDirectory=/opt/minio/
ExecStart=/opt/minio/run.sh
 
Restart=on-failure
RestartSec=5
 
[Install]
WantedBy=multi-user.target



[root@minio2 ~]# vim /usr/lib/systemd/system/minio.service
[Unit]
Description=Minio service
Documentation=https://docs.minio.io/
 
[Service]
WorkingDirectory=/opt/minio/
ExecStart=/opt/minio/run.sh
 
Restart=on-failure
RestartSec=5
 
[Install]
WantedBy=multi-user.target
```



##### 添加执行权限

节点1和节点2都需要添加可执行权限，包括MinIO的二进制可执行文件、启动脚本、服务脚本

```shell 
[root@minio1 minio]# chmod +x minio
[root@minio1 minio]# chmod +x /opt/minio/run.sh

[root@minio2 minio]# chmod +x minio
[root@minio2 minio]# chmod +x /opt/minio/run.sh

[root@minio1 ~]# chmod +x /usr/lib/systemd/system/minio.service
[root@minio2 ~]# chmod +x /usr/lib/systemd/system/minio.service
```



##### 启动服务

```shell
[root@minio1 minio]# systemctl daemon-reload
[root@minio1 minio]# systemctl start minio
[root@minio1 minio]# systemctl enable minio
Created symlink from /etc/systemd/system/multi-user.target.wants/minio.service to /usr/lib/systemd/system/minio.service.
```

通过status查看service的状态

```shell
systemctl status minio.service -l
```

启动服务最好各个节点之间启动时间的间隙不超过十五分钟

```shell
[root@minio1 minio]# systemctl daemon-reload
[root@minio1 minio]# systemctl start minio
[root@minio1 minio]# systemctl enable minio
Created symlink from /etc/systemd/system/multi-user.target.wants/minio.service to /usr/lib/systemd/system/minio.service.
[root@minio1 minio]# systemctl status minio.service -l
```

启动成功之后的MinIO服务状态

```shell
[root@localhost minio]# systemctl status minio.service -l
● minio.service - Minio service
   Loaded: loaded (/usr/lib/systemd/system/minio.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2022-02-08 16:02:42 CST; 3h 49min ago
     Docs: https://docs.minio.io/
 Main PID: 19048 (run.sh)
   CGroup: /system.slice/minio.service
           ├─19048 /bin/bash /opt/minio/run.sh
           └─19049 /opt/minio/minio server --config-dir /etc/minio http://10.189.66.196/home/data1 http://10.189.66.196/home/data2 http://10.189.66.197/home/data1 http://10.189.66.197/home/data2

Feb 08 16:02:42 localhost.localdomain run.sh[19048]: Please use MINIO_ROOT_USER and MINIO_ROOT_PASSWORD
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: Waiting for all MinIO sub-systems to be initialized.. lock acquired
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: Automatically configured API requests per node based on available memory on the system: 92
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: All MinIO sub-systems initialized successfully
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: Waiting for all MinIO IAM sub-system to be initialized.. lock acquired
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: Status:         4 Online, 0 Offline.
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: API: http://10.189.66.197:9000  http://127.0.0.1:9000
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: Console: http://10.189.66.197:17200 http://127.0.0.1:17200
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: Documentation: https://docs.min.io
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: WARNING: Console endpoint is listening on a dynamic port (17200), please use --console-address ":PORT" to choose a static port.
```



##### 访问管理界面

在status查看的状态中，可以看到访问控制台界面的IP和端口，

实际上使用任何节点的任何IP+9000端口都会自动重定向到console所指定的端口

```shell
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: Status:         4 Online, 0 Offline.
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: API: http://10.189.66.197:9000  http://127.0.0.1:9000
Feb 08 16:02:42 localhost.localdomain run.sh[19048]: Console: http://10.189.66.197:17200 http://127.0.0.1:17200
```



##### 查看MinIO状态

![image-20220208200026502](C:\Users\Caijinyang\AppData\Roaming\Typora\typora-user-images\image-20220208200026502.png)



![image-20220208200042166](C:\Users\Caijinyang\AppData\Roaming\Typora\typora-user-images\image-20220208200042166.png)



##### 配置Nginx负载均衡

[minio搭建单机/集群 - 简书 (jianshu.com)](https://www.jianshu.com/p/bfde2495aa94)

首先安装Nginx，部署完成之后修改Nginx的配置文件。

通过查看进程和端口等方式确认Nginx已经在运行中

```shell
[root@localhost sbin]# ps aux | grep nginx
root      4980  0.0  0.0  20572   620 ?        Ss   16:56   0:00 nginx: master process ./nginx
nobody    4981  0.0  0.0  23092  1624 ?        S    16:56   0:00 nginx: worker process

[root@localhost sbin]# netstat -nltp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:9999            0.0.0.0:*               LISTEN      4980/nginx: master
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      3066/sshd
tcp6       0      0 :::9000                 :::*                    LISTEN      19049/minio
tcp6       0      0 :::17200                :::*                    LISTEN      19049/minio
tcp6       0      0 :::22                   :::*                    LISTEN      3066/sshd
```

主要的配置修改在`/usr/local/nginx/conf/nginx.conf`

```shell
[root@localhost nginx]# cd conf/
[root@localhost conf]# ll
total 68
-rw-r--r-- 1 root root 1077 Feb  9 14:56 fastcgi.conf
-rw-r--r-- 1 root root 1077 Feb  9 14:56 fastcgi.conf.default
-rw-r--r-- 1 root root 1007 Feb  9 14:56 fastcgi_params
-rw-r--r-- 1 root root 1007 Feb  9 14:56 fastcgi_params.default
-rw-r--r-- 1 root root 2837 Feb  9 14:56 koi-utf
-rw-r--r-- 1 root root 2223 Feb  9 14:56 koi-win
-rw-r--r-- 1 root root 5231 Feb  9 14:56 mime.types
-rw-r--r-- 1 root root 5231 Feb  9 14:56 mime.types.default
-rw-r--r-- 1 root root 2870 Feb  9 16:56 nginx.conf
-rw-r--r-- 1 root root 2656 Feb  9 14:56 nginx.conf.default
-rw-r--r-- 1 root root  636 Feb  9 14:56 scgi_params
-rw-r--r-- 1 root root  636 Feb  9 14:56 scgi_params.default
-rw-r--r-- 1 root root  664 Feb  9 14:56 uwsgi_params
-rw-r--r-- 1 root root  664 Feb  9 14:56 uwsgi_params.default
-rw-r--r-- 1 root root 3610 Feb  9 14:56 win-utf
```

修改完成之后的nginx.conf如下：

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

    upstream minio {
        least_conn;
        #ip_hash;
        server 10.189.66.196:9000;
        #server 10.189.66.196:9001;
        server 10.189.66.197:9000;
        #server 10.189.66.197:9001;
    }



    server {
        listen       9999;
        server_name  minio;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass http://minio;
            proxy_set_header Host $http_host;
            client_max_body_size 1000m;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
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

主要的修改内容在

```shell
upstream minio {
    least_conn;
    #ip_hash;
    server 10.189.66.196:9000;
    #server 10.189.66.196:9001;
    server 10.189.66.197:9000;
    #server 10.189.66.197:9001;
}

location / {
    proxy_pass http://minio;
    proxy_set_header Host $http_host;
    client_max_body_size 1000m;
}
```

>在Nginx配置MinIO代理中，通过upstream配置了ip和端口，访问的时候会出现以下问题：
>
>1. 浏览器的ip地址栏会重新加载，但是通过Nginx负载均衡是反向代理，用户不应该会看到IP的改变，10.189.66.197:9999就是这个ip，而不应该跳转到别的端口。（存疑）
>2. 由于upstream中还有一个server在10.189.66.196:9000，访问10.189.66.196:9000的时候MinIO又会将这个请求重定向到10.189.66.196:14546这个网页服务器上，也就是MinIO的H5控制台，所以最终的Nginx效果变成了访问10.189.66.197:14546，那么这个结果自然不对了。
>3. 但是单独访问10.189.66.196:9000还是会重定向到10.189.66.199:14546，网页还是正常打开。














