# Docker

## Why Docker

### Visualization

虚拟化的核心是对于资源的抽象。



从大分类来说，虚拟化技术可以分为基于硬件的虚拟化和基于软件的虚拟化，但是总体来说，也很少有完全基于硬件的虚拟化。

基于软件的虚拟化技术从对象所处的层次，又可以分为应用虚拟化和平台虚拟化，应用虚拟化可以分为以下几个子类：

1. 完全虚拟化：虚拟机模拟完整的底层硬件环境和特权指令的执行过程，客户无需调整。典型的应用-VMware Workstation、VisualBox。
2. 硬件辅助虚拟化：利用硬件（主要是CPU的支持Intel-VT和AMD-V）
3. 部分虚拟化
4. 超虚拟化
5. 操作系统虚拟化



### Docker & Visualization

**Docker与虚拟机的比较**

1. Docker容器很快，可以在秒级实现启动和停止，虚拟机可能需要分钟级。
2. Docker对于系统资源的需求很少，一台主机上可以同时运行上千个Docker容器。但是虚拟机需要大量的主机资源。
3. Docker使用Git的思想和操作方便用户来获取、分发和更新应用镜像，存储复用、增量更新。
4. Docker使用Dockerfile支持灵活的自动化创建和部署机制，以提高工作效率，并且标准化流程。

传统的虚拟机运行N个不同应用就需要启动N个虚拟机（每个虚拟机都需要单独分配独占的内存和硬盘资源），但是Docker只需要启动N个隔离很薄的容器，然后将应用放入容器内即可，应用获得的是接近原生的运行性能。





## command

### docker pull



### docker images

```shell
docker images
docker image ls
```

```shell
docker inspect python:latest
```

```shell
docker history python:latest
```



### docker search



### docker rm



### docker prune

使用Docker一段时间之后，可能会遗留一些临时的镜像文件，以及一些没有被使用过的镜像，可以使用docker image prune删除。



### docker commit 

制作自己的docker容器一般有以下两种方式：

1. 通过docker run命令启动容器
2. 修改





## docker compose





## docker overlay2

overlay2空间清理办法

1. 查看磁盘占用
    df -h
    
2. Docker 的内置 CLI 指令docker system df
    可用于查询镜像（Images）、容器（Containers）和本地卷（Local Volumes）等空间使用大户的空间占用情况。
    
    ```shel
    docker system df
    ```
    
    查看详细
    
    ```shell
    docker system df -v
    ```
    
3. 空间清理
    【1】通过 Docker 内置的 CLI 指令docker system prune来进行自动空间清理。

    ```shell
    docker system prune --help
    ```


    该指令默认会清除所有如下资源：
        已停止的容器（container）
        未被任何容器所使用的卷（volume）
        未被任何容器所关联的网络（network）
        所有悬空镜像（image）。
        
    该指令默认只会清除悬空镜像，未被使用的镜像不会被删除。添加-a 或 --all参数后，可以一并清除所有未使用的镜像和悬空镜像。
    可以添加-f 或 --force参数用以忽略相关告警确认信息。
    【2】另外除了system级别的，还有针对容器或是镜像级别的删除命令：
    docker image prune：删除悬空的镜像。
    docker container prune：删除无用的容器。
      --默认情况下docker container prune命令会清理掉所有处于stopped状态的容器
      --如果不想那么残忍统统都删掉，也可以使用--filter标志来筛选出不希望被清理掉的容器。例子：清除掉所有停掉的容器，但24内创建的除外：
      --$ docker container prune --filter "until=24h"  

    docker volume prune：删除无用的卷。
    docker network prune：删除无用的网络
    【3】手动清除
        对于悬空镜像和未使用镜像可以使用手动进行个别删除：

    1. 删除所有悬空镜像，不删除未使用镜像：

       ```shell
       docker rmi $(docker images -f "dangling=true" -q)
       ```

    2. 删除所有未使用镜像和悬空镜像

       ```shell
       docker rmi $(docker images -q)
       ```

    3. 清理卷

       如果卷占用空间过高，可以清除一些不使用的卷，包括一些未被任何容器调用的卷（-v 详细信息中若显示 LINKS = 0，则是未被调用）：
           删除所有未被容器引用的卷：

       ```shell
       docker volume rm $(docker volume ls -qf dangling=true)
       ```

    4. 容器清理

       如果发现是容器占用过高的空间，可以手动删除一些：
       删除所有已退出的容器：

       ```shell
       docker rm -v $(docker ps -aq -f status=exited)
       ```

       删除所有状态为dead的容器

       ```shell
       docker rm -v $(docker ps -aq -f status=dead)
       ```

       **查找系统中的大文件【以上三步仍然不可以的时候执行】**

       ```shell
       find /var/lib/docker/overlay2/ -type f -size +100M -print0 | xargs -0 du -h | sort -nr
       ```


       新建或修改/etc/docker/daemon.json，添加log-dirver和log-opts参数

       ```
       vi /etc/docker/daemon.json
       {
       "log-driver":"json-file",
       "log-opts": {"max-size":"3m", "max-file":"1"}
       }
       ```

       重启docker的守护线程

       ```shell
       systemctl daemon-reload
       systemctl restart docker
       ```

4. docker 清理空间命令
    删除悬空镜像

    ```shell
    docker rmi $(docker images -f "dangling=true" -q)
    docker image prune -a -f
    ```

5. 显示所有的容器，过滤出Exited状态的容器，取出这些容器的ID，

    ```shell
    sudo docker ps -a|grep Exited|awk '{print $1}'
    ```

6. 查询所有的容器，过滤出Exited状态的容器，列出容器ID，删除这些容器

    ```shell
    sudo docker rm `docker ps -a|grep Exited|awk '{print $1}'`
    ```

    方法二： 
    删除所有未运行的容器（已经运行的删除不了，未运行的就一起被删除了）

    ```shell
    sudo docker rm $(sudo docker ps -a -q)
    ```

    方法三：
    根据容器的状态，删除Exited状态的容器

    ```shell
    sudo docker rm $(sudo docker ps -qf status=exited)
    ```

    方法四：

    Docker 1.13版本以后，可以使用 docker containers prune 命令，删除孤立的容器。

    ```shell
    sudo docker container prune
    ```

    删除所有镜像

    ```shell
    sudo docker rmi $(docker images -q)
    ```

7. 实在没办法，只有把/var目录下所有日志文件清空
    for i in `find /var -name *.log*`;do >$i;done
    然后重启node节点，因为有些日志文件被占用，清空后空间仍然无法释放
    
