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
