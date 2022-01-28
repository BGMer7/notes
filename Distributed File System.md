# Distributed File System

## MinIO

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

示例1: 启动分布式Minio实例，8个节点，每节点1块盘，需要在8个节点上都运行下面的命令。

```shell
export MINIO_ACCESS_KEY=<ACCESS_KEY>
export MINIO_SECRET_KEY=<SECRET_KEY>
minio server http://192.168.1.11/export1 http://192.168.1.12/export2 \
               http://192.168.1.13/export3 http://192.168.1.14/export4 \
               http://192.168.1.15/export5 http://192.168.1.16/export6 \
               http://192.168.1.17/export7 http://192.168.1.18/export8
```

实例2：启动分布式Minio实例，4节点，每节点4块盘，需要在4个节点上都运行下面的命令。

```shell
export MINIO_ACCESS_KEY=<ACCESS_KEY>
export MINIO_SECRET_KEY=<SECRET_KEY>
minio server http://192.168.1.11/export1 http://192.168.1.11/export2 \
               http://192.168.1.11/export3 http://192.168.1.11/export4 \
               http://192.168.1.12/export1 http://192.168.1.12/export2 \
               http://192.168.1.12/export3 http://192.168.1.12/export4 \
               http://192.168.1.13/export1 http://192.168.1.13/export2 \
               http://192.168.1.13/export3 http://192.168.1.13/export4 \
               http://192.168.1.14/export1 http://192.168.1.14/export2 \
               http://192.168.1.14/export3 http://192.168.1.14/export4

```

实例3：非官方实例，使用2个服务器分别挂载2个节点来模拟4个节点。1

1. 首先配置hostname

   ```shell
   hostnamectl --static set-hostname CENTOS-NODE01
   hostnamectl --static set-hostname CENTOS-NODE02
   ```

2. 配置/etc/hosts

   ```shell
   vi /etc/hosts
   192.168.xx.xxx   CENTOS-NODE01
   192.168.xx.xxx   CENTOS-NODE02
   ```

3. 配置/etc/profile

   ```shell
   vim /etc/profile
   export MINIO_ACCESS_KEY="accesskey"
   export MINIO_SECRET_KEY="secretkeys"
   ```

4. 配置每个节点的启动脚本

   对于每台服务的第一个节点（即 9000端口），配置如下

   ```shell
   #!/bin/bash
   export MINIO_ACCESS_KEY=accesskey
   export MINIO_SECRET_KEY=secretkeys
   nohup /opt/minio/minio server --address :9000 \
   http://CENTOS-NODE01/minio-data1 \
   http://CENTOS-NODE01/minio-data2 \
   http://CENTOS-NODE02/minio-data1 \
   http://CENTOS-NODE02/minio-data2 \
   > /opt/minio/log/minio.log 2>&1 &
   ```

   对于每台服务的第二个节点（即 9001端口），配置如下

   ```shell
   #!/bin/bash
   export MINIO_ACCESS_KEY=accesskey
   export MINIO_SECRET_KEY=secretkeys
   nohup /opt/minio-2/minio server --address :9001 \
   http://CENTOS-NODE01/minio-data1 \
   http://CENTOS-NODE01/minio-data2 \
   http://CENTOS-NODE02/minio-data1 \
   http://CENTOS-NODE02/minio-data2 \
   > /opt/minio-2/log/minio.log 2>&1 &
   ```

5. 按照单节点启动的方式启动集群每个节点

   如果部署过单机节点，如要在相同环境上部署集群，要注意清空目录后再重新部署！

























