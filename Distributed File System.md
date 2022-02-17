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

[Centos 安装Nginx - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1924770)

**有yum源**

1. 由于Nginx是基于C/C++编写的框架，所以需要有gcc/g++环境，一般来说Linux都会包含，如果没有的话需要用yum安装

   ```shell
   yum install -y gcc gcc-c++
   ```

2. 安装Nginx需要有三个前置依赖包：pcre、zlib、openssl

   ```shell
   yum install -y zlib pcre openssl
   ```

   这三个模块直接通过yum源安装即可。



**无yum源**

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





### MinIO expand

#### 通过LVM逻辑卷扩容

官方并不推荐这种做法

[minio分布式解决方案 - 偷得浮生 (zhangzhuo.ltd)](https://zhangzhuo.ltd/articles/2021/09/16/1631782856124.html)

例如：集群部署时使用4块1T的磁盘使用逻辑卷的方式部署四个数据目录，之后扩容时再安装4块1T磁盘把四个数据目录扩容到2T，相应的minio集群容量也扩充1倍



#### 对等扩容

[漫谈 MinIO 集群扩容方法-CSDN博客](https://blog.csdn.net/csdnnews/article/details/114860023)

首先，MinIO的极简设计理念使得MinIO分布式集群并不支持向集群中添加单个节点并进行自动调节的扩容方式，这是因为加入单个节点后所引发的数据均衡以及纠删组划分等问题会为整个集群带来复杂的调度和处理过程，并不利于维护。因此，MinIO提供了一种对等扩容的方式，即要求增加的节点数和磁盘数均需与原集群保持对等。

例如原集群包含4个节点4块磁盘，则在扩容时必须同样增加4个节点4块磁盘（或为其倍数），以便系统维持相同的数据冗余SLA，从而极大地降低扩容的复杂性。如上例，在扩容后，MinIO集群并不会对全部的8个节点进行完全的数据均衡，而是将原本的4个节点视作一个区域，新加入的4节点视作另一区域，当有新对象上传时，集群将依据各区域的可用空间比例确定存放区域，在各区域内仍旧通过哈希算法确定对应的纠删组进行最终的存放。此外，集群进行一次对等扩容后，还可依据扩容规则继续进行对等扩容，但出于安全性考虑，集群的最大节点数一般不得超过32个。

对等扩容的优点在于配置操作简单易行，通过一条命令即可完成扩容（注意：推荐使用连续的节点IP，并参照MinIO官网在扩容命令中使用{}）。而对等扩容的局限性在于：①扩容需重启；②扩容存在限制，集群节点数一般不超过32个，这是由于MinIO集群通过分布式锁保证强一致性，若集群节点数过大，维护强一致性将带来性能问题。



#### 联邦扩容

[MinIO分布式集群搭建不完全指南-包含联邦扩容模式的使用 - 兮陌 (simaek.com)](https://www.simaek.com/archives/196/)

正式的生产环境中，需要提前考虑扩容的问题。常见的扩容方式有两种：水平扩容和垂直扩容。水平扩容是指通过增加节点数量来扩展系统性能；而垂直扩容则指提升各节点自身的性能，例如添加磁盘。但是Minio官方并不支持这样的做法。MinIO主要支持两种水平方式的扩容：对等扩容和联邦扩容。

对等扩容需要新增与现有集群相同数量的节点和硬盘，局限性很大，并且MinIO建议的集群最大节点数量不能超过32个，这也是为了集群性能和稳定性考虑。

所以，比较好的选择就是采用联邦扩容的方式。通过引入etcd，将多个MinIO集群在逻辑上组成一个联邦，对外以一个整体提供服务。联邦扩容相比较对等扩容，有如下的优点：

- 组成联邦的各个集群不要求节点数和磁盘数的对等
- 联邦可以通过加入新的集群实现无限扩容
- 组成联邦的集群中，如果有部分集群出现故障，并不会影响其他集群的正常工作

如果要说联邦扩容的缺点，那就是需要引入etcd，配置较为复杂。但这算MinIO的缺点么？不，这是我们运维的缺点！手动Doge。

下面将介绍联邦扩容的配置方法，我在这里建议，在第一次搭建MinIO集群的时候，即使只有一个集群，也要引入etcd，方便以后扩容。

##### **1、资源分配**

我们假定正式环境中，先创建了一个MinIO集群，并且引入etcd。而后又有了扩容的需求，需要向联邦中添加新一个的集群。

**etcd集群**

etcd是一个开源的分布式键值对存储服务，在联邦中用于记录bucket的IP地址，联邦内的各个集群的数据存储以及一致性维护仍然由各个集群自行管理，联邦只是对外提供一个整体逻辑视图。通过连接到联邦中任一集群的任一节点，可以查询并访问联邦内所有集群的全部数据。

当一个应用访问MinIO的时候，联邦通过etcd定位到bucket实际所在的集群，在进行数据访问，联邦对外屏蔽了bucket的IP查找和定位过程，在逻辑上形成统一的整体提供服务。你可以理解为etcd实现了类似路由寻址的功能。

为了避免单点故障，etcd集群建议配置为三个节点。

| Name  | IP             |
| :---- | :------------- |
| etcd1 | 192.168.88.100 |
| etcd2 | 192.168.88.101 |
| etcd3 | 192.168.88.102 |

**MinIO集群1**

首次创建的集群为4个节点，4块硬盘。

| Host   | Address       | Disk             |
| :----- | :------------ | :--------------- |
| minio1 | 192.168.88.11 | /data/minio_data |
| minio2 | 192.168.88.12 | /data/minio_data |
| minio3 | 192.168.88.13 | /data/minio_data |
| minio4 | 192.168.88.14 | /data/minio_data |

**MinIO集群2**

第二次创建的，用于扩容的集群为2个节点，4块硬盘。

| Host   | Address       | Disk                    |
| :----- | :------------ | :---------------------- |
| minio1 | 192.168.88.20 | /data/minio_data{1...2} |
| minio2 | 192.168.88.21 | /data/minio_data{1...2} |

##### 2、etcd集群部署

> etcd部署见Distributed System.md

CentOS可直接使用Yum进行安装，这里也以此为例。没有网络的环境下，也可以下载[etcd发行版](http://github.com/etcd-io/etcd/releases)或者RPM包进行手动安装。

```
yum install -y etcd
```

修改配置文件`/etc/etcd/etcd.conf`，以**etcd1**为例。

```
#[Member]
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="http://0.0.0.0:2380"
ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0:2379"
ETCD_NAME="etcd1"

#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://192.168.100:2380"
ETCD_ADVERTISE_CLIENT_URLS="http://192.168.100:2379"
ETCD_INITIAL_CLUSTER="etcd1=http://192.168.100:2380,etcd2=http://192.168.101:2380,etcd3=http://192.168.102:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
```

> ETCD_DATA_DIR=数据存放位置
>
> ETCD_LISTEN_PEER_URLS=监听的etcd节点URL，最好是指填写本集群内的所有节点，0.0.0.0表示监听全部地址
>
> ETCD_LISTEN_CLIENT_URLS=监听的客户端URLs，最好填写MinIO集群的地址，只允许MinIO访问，但是考虑到未来扩容，这里监听全部
>
> ETCD_NAME=etcd节点的名称，需要唯一
>
> ETCD_INITIAL_ADVERTISE_PEER_URLS=广播节点URL
> ETCD_ADVERTISE_CLIENT_URLS=广播客户端URL
> ETCD_INITIAL_CLUSTER=集群内所有节点
> ETCD_INITIAL_CLUSTER_TOKEN=令牌，每个节点需要相同
> ETCD_INITIAL_CLUSTER_STATE=集群状态

所有的节点都进行上述配置。参数`ETCD_INITIAL_CLUSTER`填写的节点信息，必须与各节点上配置的`ETCD_NAME`参数以及`ETCD_INITIAL_ADVERTISE_PEER_URLS`参数进行对应。

配置完成后，在各节点启动etcd。

```
systemctl start etcd
systemctl enable etcd
```

在任意一个节点上，输入如下命令查看集群状态。

```
etcdctl member list
```

应该能看到有三个节点，说明集群运行正常。

##### 3、使用联邦模式配置MinIO集群1

MinIO单个集群的部署按照之前的步骤自行部署，这里不再赘述。唯一不同的是启动MinIO集群的脚本需要添加如下配置。所以我想聪明的你一定能想到，可以只接修改之前的集群来支持联邦模式，只不过这样需要重启整个集群，这在生产环境中是非常不方便的。所以还是建议，在一开始就配置好。

```
export MINIO_ACCESS_KEY=minio
export MINIO_SECRET_KEY=test@minio

export MINIO_ETCD_ENDPOINTS="http://192.168.88.100:2379,http://192.168.88.101:2379,http://192.168.88.102:2379"
export MINIO_PUBLIC_IPS=192.168.88.11,192.168.88.12,192.168.88.13,192.168.88.14
export MINIO_DOMAIN=cluster1.minio.com

/opt/minio/minio server http://192.168.88.{11...14}/data/minio_data
```

**注意：**`MINIO_ETCD_ENDPOINTS`参数需与搭建的ETCD集群所有节点IP相对应；`MINIO_PUBLIC_IPS`参数则为该集群的所有节点IP；`MINIO_DOMAIN`参数必须进行配置，即使你并不通过域名访问存储桶，否则联邦无法生效，只有`MINIO_DOMAIN`参数值相同的集群，才会组成联盟。

##### 4、将MinIO集群2加入联邦

集群2中的各个节点进行同样的配置，启动后，进行如下测试。

- 首先连接至集群1中的任一节点，创建Bucket，名称自定义，上传一个对象。
- 连接至任一etcd节点，通过`ETCDCTL_API=3 etcdctl get --from-key ''`命令查看etcd中是否已经写入刚刚创建的Bucket的相关记录
- 最后连接至集群2中的任一节点，查看能否访问到刚刚在集群1中创建的Bucket以及上传的对象。

如果一切正常，说明联邦模式创建成功。

##### 5、后续的扩容

后续若需进行扩容，只需要将新创建的集群，加入现有的etcd服务，这些集群将自动加入联邦，从而实现集群的无限扩展。

最后我还是建议优先考虑联邦扩容，并且首次进行单个集群部署时就引入etcd联邦模式，这样后续进行联邦扩容时能简化很多流程。

除非你真的确定，比如原集群节点数和磁盘数较少、系统数据量增长较慢、后续扩容操作不频繁、服务可中断的情况下，可考虑对等扩容

##### 6、其他

为了安全，生产可能需要关闭自带的浏览器，在配置文件添加参数。

```
MINIO_BROWSER=off
```











