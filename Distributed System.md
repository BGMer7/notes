# Distributed System

分布式系统是由一组通过网络进行通信、为了完成共同的任务而协调工作的计算机节点组成的系统。分布式系统的出现是为了用廉价的、普通的机器完成单个计算机无法完成的计算、存储任务。其目的是**利用更多的机器，处理更多的数据**。

首先需要明确的是，只有当单个节点的处理能力无法满足日益增长的计算、存储任务的时候，且硬件的提升（加内存、加磁盘、使用更好的CPU）高昂到得不偿失的时候，应用程序也不能进一步优化的时候，我们才需要考虑分布式系统。



在很多文章中，主要讲分布式系统分为分布式计算（computation）与分布式存储（storage）。计算与存储是相辅相成的，计算需要数据，要么来自实时数据（流数据），要么来自存储的数据；而计算的结果也是需要存储的。在操作系统中，对计算与存储有非常详尽的讨论，分布式系统只不过将这些理论推广到多个节点罢了。

那么分布式系统怎么将任务分发到这些计算机节点呢，很简单的思想，分而治之，即分片（**partition）**。对于计算，那么就是对计算任务进行切换，每个节点算一些，最终汇总就行了，这就是MapReduce的思想；对于存储，更好理解一下，每个节点存一部分数据就行了。当数据规模变大的时候，Partition是唯一的选择，同时也会带来一些好处：

1. 提升性能和并发，操作被分发到不同的分片，相互独立
2. 提升系统的可用性，即使部分分片不能用，其他分片不会受到影响

 理想的情况下，有分片就行了，但事实的情况却不大理想。原因在于，分布式系统中有大量的节点，且通过网络通信。单个节点的故障（进程crash、断电、磁盘损坏）是个小概率事件，但整个系统的故障率会随节点的增加而指数级增加，网络通信也可能出现断网、高延迟的情况。在这种一定会出现的“异常”情况下，分布式系统还是需要继续稳定的对外提供服务，即需要较强的容错性。最简单的办法，就是冗余或者复制集（**Replication**），即多个节点负责同一个任务，最为常见的就是分布式存储中，多个节点复杂存储同一份数据，以此增强可用性与可靠性。同时，Replication也会带来性能的提升，比如数据的locality可以减少用户的等待时间。



Partition和Replication是解决分布式系统问题的一记组合拳，很多具体的问题都可以用这个思路去解决。但这并不是银弹，往往是为了解决一个问题，会引入更多的问题，比如为了可用性与可靠性保证，引用了冗余（复制集）。有了冗余，各个副本间的一致性问题就变得很头疼，**一致性在系统的角度和用户的角度又有不同的等级划分**。**如果要保证强一致性，那么会影响可用性与性能，在一些应用（比如电商、搜索）是难以接受的。**如果是最终一致性，那么就需要处理数据冲突的情况。**CAP、FLP这些理论告诉我们，在分布式系统中，没有最佳的选择，都是需要权衡，做出最合适的选择。**



**可扩展性**：分布式系统的根本目标就是为了处理单个计算机无法处理的任务，当任务增加的时候，分布式系统的处理能力需要随之增加。简单来说，要比较方便的通过增加机器来应对数据量的增长，同时，当任务规模缩减的时候，可以撤掉一些多余的机器，达到动态伸缩的效果

**可用性与可靠性**：一般来说，分布式系统是需要长时间甚至7*24小时提供服务的。可用性是指系统在各种情况对外提供服务的能力，简单来说，可以通过不可用时间与正常服务时间的必知来衡量；而可靠性而是指计算结果正确、存储的数据不丢失。

高性能：不管是单机还是分布式系统，大家都非常关注性能。不同的系统对性能的衡量指标是不同的，最常见的：高并发，单位时间内处理的任务越多越好；低延迟：每个任务的平均时间越少越好。这个其实跟操作系统CPU的调度策略很像

**一致性**：分布式系统为了提高可用性可靠性，一般会引入冗余（复制集）。那么如何保证这些节点上的状态一致，这就是分布式系统不得不面对的一致性问题。一致性有很多等级，一致性越强，对用户越友好，但会制约系统的可用性；一致性等级越低，用户就需要兼容数据不一致的情况，但系统的可用性、并发性很高很多。



<img src="https://images2017.cnblogs.com/blog/1089769/201711/1089769-20171110170521606-1076859931.png" alt="img" style="zoom:67%;" />



















## etcd 

etcd由Go编写，需要Go环境

### golang install

1. Extract the archive you downloaded into /usr/local, creating a Go tree in /usr/local/go.

   **Important:** This step will remove a previous installation at /usr/local/go, if any, prior to extracting. Please back up any data before proceeding.

   For example, run the following as root or through `sudo`:

   ```
   rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.7.linux-amd64.tar.gz
   ```

2. Add /usr/local/go/bin to the PATH environment variable.

   You can do this by adding the following line to your $HOME/.profile or /etc/profile (for a system-wide installation):

   ```
   export PATH=$PATH:/usr/local/go/bin
   ```

   **Note:** Changes made to a profile file may not apply until the next time you log into your computer. To apply the changes immediately, just run the shell commands directly or execute them from the profile using a command such as `source $HOME/.profile`.

3. Verify that you've installed Go by opening a command prompt and typing the following command:

   ```
   $ go version
   ```

4. Confirm that the command prints the installed version of Go.

```shell
[root@localhost ~]#  cd /home/golang-install/
[root@localhost golang-install]# rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.7.linux-amd64.tar.gz
[root@localhost golang-install]# export PATH=$PATH:/usr/local/go/bin
[root@localhost golang-install]# go version
go version go1.17.7 linux/amd64
```



### etcd install

```shell
[root@localhost etcd-install]# tar -zxf etcd-v3.3.0-linux-amd64.tar.gz
[root@localhost etcd-install]# ll
total 15768
-drwxr-xr-x 3 1000 1000      123 Feb  2  2018 etcd-v3.3.0-linux-amd64
-rw-r--r-- 1 root root 11268992 Feb 15 17:24 etcd-v3.3.0-linux-amd64.tar.gz
[root@localhost etcd-install]# mv etcd-v3.3.0-linux-amd64/etcd* /$GOPATH/bin
[root@localhost etcd-install]# ll
total 11008
drwxr-xr-x 3 1000 1000       96 Feb 15 17:26 etcd-v3.3.0-linux-amd64
-rw-r--r-- 1 root root 11268992 Feb 15 17:24 etcd-v3.3.0-linux-amd64.tar.gz
[root@localhost etcd-install]# etcd -version
etcd Version: 3.3.0
Git SHA: c23606781
Go Version: go1.9.3
Go OS/Arch: linux/amd64
```



### etcd service

```shell
[root@localhost etcd-v3.3.0-linux-amd64]# cd /usr/bin
[root@localhost bin]# ./etcd
2022-02-15 17:38:06.581152 I | etcdmain: etcd Version: 3.3.0
2022-02-15 17:38:06.581212 I | etcdmain: Git SHA: c23606781
2022-02-15 17:38:06.581217 I | etcdmain: Go Version: go1.9.3
2022-02-15 17:38:06.581220 I | etcdmain: Go OS/Arch: linux/amd64
2022-02-15 17:38:06.581224 I | etcdmain: setting maximum number of CPUs to 4, total number of available CPUs is 4
2022-02-15 17:38:06.581234 W | etcdmain: no data-dir provided, using default data-dir ./default.etcd
2022-02-15 17:38:06.581616 I | embed: listening for peers on http://localhost:2380
2022-02-15 17:38:06.581750 I | embed: listening for client requests on localhost:2379
2022-02-15 17:38:06.582803 I | etcdserver: name = default
2022-02-15 17:38:06.582814 I | etcdserver: data dir = default.etcd
2022-02-15 17:38:06.582819 I | etcdserver: member dir = default.etcd/member
2022-02-15 17:38:06.582823 I | etcdserver: heartbeat = 100ms
2022-02-15 17:38:06.582826 I | etcdserver: election = 1000ms
2022-02-15 17:38:06.582830 I | etcdserver: snapshot count = 100000
2022-02-15 17:38:06.582838 I | etcdserver: advertise client URLs = http://localhost:2379
2022-02-15 17:38:06.582842 I | etcdserver: initial advertise peer URLs = http://localhost:2380
2022-02-15 17:38:06.582850 I | etcdserver: initial cluster = default=http://localhost:2380
2022-02-15 17:38:06.583445 I | etcdserver: starting member 8e9e05c52164694d in cluster cdf818194e3a8c32
2022-02-15 17:38:06.583473 I | raft: 8e9e05c52164694d became follower at term 0
2022-02-15 17:38:06.583491 I | raft: newRaft 8e9e05c52164694d [peers: [], term: 0, commit: 0, applied: 0, lastindex: 0, lastterm: 0]
2022-02-15 17:38:06.583496 I | raft: 8e9e05c52164694d became follower at term 1
2022-02-15 17:38:06.584903 W | auth: simple token is not cryptographically signed
2022-02-15 17:38:06.585676 I | etcdserver: starting server... [version: 3.3.0, cluster version: to_be_decided]
2022-02-15 17:38:06.587182 I | etcdserver/membership: added member 8e9e05c52164694d [http://localhost:2380] to cluster cdf818194e3a8c32
2022-02-15 17:38:06.683819 I | raft: 8e9e05c52164694d is starting a new election at term 1
2022-02-15 17:38:06.683849 I | raft: 8e9e05c52164694d became candidate at term 2
2022-02-15 17:38:06.683865 I | raft: 8e9e05c52164694d received MsgVoteResp from 8e9e05c52164694d at term 2
2022-02-15 17:38:06.683877 I | raft: 8e9e05c52164694d became leader at term 2
2022-02-15 17:38:06.683884 I | raft: raft.node: 8e9e05c52164694d elected leader 8e9e05c52164694d at term 2
2022-02-15 17:38:06.684191 I | etcdserver: setting up the initial cluster version to 3.3
2022-02-15 17:38:06.684288 I | etcdserver: published {Name:default ClientURLs:[http://localhost:2379]} to cluster cdf818194e3a8c32

#节点名称
2022-02-15 17:38:06.684368 I | embed: ready to serve client requests
2022-02-15 17:38:06.684385 N | etcdserver/membership: set the initial cluster version to 3.3

[Unit]
2022-02-15 17:38:06.684424 E | etcdmain: forgot to set Type=notify in systemd service file?
2022-02-15 17:38:06.684440 I | etcdserver/api: enabled capabilities for version 3.3
2022-02-15 17:38:06.684909 N | embed: serving insecure client requests on 127.0.0.1:2379, this is strongly discouraged!

[root@localhost bin]# ./etcd --version
etcd Version: 3.3.0
Git SHA: c23606781
Go Version: go1.9.3
Go OS/Arch: linux/amd64
[root@localhost bin]# mkdir -p /var/lib/etcd/
[root@localhost bin]# mkdir -p /opt/etcd/config/
[root@localhost bin]# vim /opt/etcd/config/etcd.conf
[root@localhost bin]# cat /opt/etcd/config/etcd.conf
#节点名称
ETCD_NAME=$(hostname -s)
#数据存放位置
ETCD_DATA_DIR=/var/lib/etcd

[root@localhost bin]# vim /etc/systemd/system/etcd.service
[root@localhost bin]# cat /etc/systemd/system/etcd.service
[Unit]
Description=Etcd Server
Documentation=https://github.com/coreos/etcd
After=network.target

[Service]
User=root
Type=notify
EnvironmentFile=-/opt/etcd/config/etcd.conf
ExecStart=/usr/bin/etcd
Restart=on-failure
RestartSec=10s
LimitNOFILE=40000

[Install]
WantedBy=multi-user.target
[root@localhost bin]# chmod +x /etc/systemd/system/etcd.service
[root@localhost bin]# systemctl daemon-reload
[root@localhost bin]# systemctl enable etcd
Created symlink from /etc/systemd/system/multi-user.target.wants/etcd.service to /etc/systemd/system/etcd.service.
[root@localhost bin]# systemctl start etcd
[root@localhost bin]# systemctl status etcd.service -l
● etcd.service - Etcd Server
   Loaded: loaded (/etc/systemd/system/etcd.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2022-02-15 18:02:55 CST; 3s ago
     Docs: https://github.com/coreos/etcd
 Main PID: 2290 (etcd)
    Tasks: 9
   Memory: 3.4M
   CGroup: /system.slice/etcd.service
           └─2290 /usr/bin/etcd

Feb 15 18:02:55 localhost.localdomain etcd[2290]: 8e9e05c52164694d received MsgVoteResp from 8e9e05c52164694d at term 2
Feb 15 18:02:55 localhost.localdomain etcd[2290]: 8e9e05c52164694d became leader at term 2
Feb 15 18:02:55 localhost.localdomain etcd[2290]: raft.node: 8e9e05c52164694d elected leader 8e9e05c52164694d at term 2
Feb 15 18:02:55 localhost.localdomain etcd[2290]: setting up the initial cluster version to 3.3
Feb 15 18:02:55 localhost.localdomain etcd[2290]: published {Name:$(hostname -s) ClientURLs:[http://localhost:2379]} to cluster cdf818194e3a8c32
Feb 15 18:02:55 localhost.localdomain etcd[2290]: ready to serve client requests
Feb 15 18:02:55 localhost.localdomain etcd[2290]: set the initial cluster version to 3.3
Feb 15 18:02:55 localhost.localdomain etcd[2290]: enabled capabilities for version 3.3
Feb 15 18:02:55 localhost.localdomain systemd[1]: Started Etcd Server.
Feb 15 18:02:55 localhost.localdomain etcd[2290]: serving insecure client requests on 127.0.0.1:2379, this is strongly discouraged!
[root@localhost bin]#
```

