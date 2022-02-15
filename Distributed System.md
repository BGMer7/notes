# Distributed System

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

