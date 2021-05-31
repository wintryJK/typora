# ETCD简介及基础使用

作者：杨健康



## 一、简介

etcd 是一个分布式、可靠 key-value 存储的分布式系统，它不仅仅用于存储，还提供共享配置及服务发现。



## 二、etcd vs Zookeeper

- 一致性协议： etcd 使用 Raft 协议，Zookeeper 使用 ZAB（类PAXOS协议），前者容易理解，方便工程实现

- 运维方面：etcd 方便运维，Zookeeper 难以运维

- 数据存储：etcd 多版本并发控制（MVCC）数据模型 ， 支持查询先前版本的键值对

- 项目活跃度：etcd 社区与开发活跃

- API：etcd 提供 HTTP+JSON, gRPC 接口，跨平台跨语言，Zookeeper 需要使用其客户端

- 访问安全方面：etcd 支持 HTTPS 访问，Zookeeper 在这方面缺失

  

## 三、官方网址

> https://etcd.io/



## 四、etcd安装

1. 环境搭建

   |  节点  |      ip      |
   | :----: | :----------: |
   | master | 172.20.10.11 |
   | node1  | 172.20.10.12 |
   | node2  | 172.20.10.13 |

   

2. 下载etcd

   - 方式一

     **网址**：https://github.com/etcd-io/etcd/releases/

     这里最新版本的v3.4.15安装包有问题，assets下的etcd-v3.4.15-linux-amd64.tar解压完以后其实是arm64，执行命令时会报错：

     ```sh
     -bash: ./etcdctl: 无法执行二进制文件
     ```

     （官网最新版给出的安装建议是git clone以后使用./build，这里需要go1.9以上版本，而且git clone方式太慢，所以采用下载prebuild版本，安装文件在assets下）

     **解压**

     ```shell
     sudo tar -zxvf etcd-v3.3.10-linux-amd64.tar -C /opt/
     ```

   - 方式二

     使用yum安装，版本较老但稳定。

     **安装wget**

     ```shell
     sudo yum -y install wget
     ```

     **安装epel源**

     ```shell
     sudo wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
     sudo rpm -ivh epel-release-latest-7.noarch.rpm
     ```

     **安装etcd**

     ```shell
     sudo yum install etcd
     ```

     

3. 修改API版本

   系统默认是v2版本，修改为v3

   ```shell
   sudo vi /etc/profile
   ```

   末尾追加

   ```shell
   export ETCDCTL_API=3
   ```

   执行配置

   ```shell
   source /etc/profile
   ```

     

4. 设置配置文件

   ```shell
   sudo vi /etc/etcd/etcd.conf
   ```

   ```sh
   # master
   ETCD_NAME="master"
   ETCD_DATA_DIR="/var/lib/etcd/etcd-cluster"
   ETCD_LISTEN_PEER_URLS="http://172.20.10.11:2380"
   ETCD_LISTEN_CLIENT_URLS="http://172.20.10.11:2379,http://127.0.0.1:2379"
   ETCD_INITIAL_ADVERTISE_PEER_URLS="http://172.20.10.11:2380"
   ETCD_ADVERTISE_CLIENT_URLS="http://172.20.10.11:2379"
   ETCD_INITIAL_CLUSTER="master=http://172.20.10.11:2380,node1=http://172.20.10.12:2380,node2=http://172.20.10.13:2380"
   ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
   ETCD_INITIAL_CLUSTER_STATE="new"
   
   # node1
   ETCD_NAME="node1"
   ETCD_DATA_DIR="/var/lib/etcd/etcd-cluster"
   ETCD_LISTEN_PEER_URLS="http://172.20.10.12:2380"
   ETCD_LISTEN_CLIENT_URLS="http://172.20.10.12:2379,http://127.0.0.1:2379"
   ETCD_INITIAL_ADVERTISE_PEER_URLS="http://172.20.10.12:2380"
   ETCD_ADVERTISE_CLIENT_URLS="http://172.20.10.12:2379"
   ETCD_INITIAL_CLUSTER="master=http://172.20.10.11:2380,node1=http://172.20.10.12:2380,node2=http://172.20.10.13:2380"
   ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
   ETCD_INITIAL_CLUSTER_STATE="new"
   
   # node2
   ETCD_NAME="node2"
   ETCD_DATA_DIR="/var/lib/etcd/etcd-cluster"
   ETCD_LISTEN_PEER_URLS="http://172.20.10.13:2380"
   ETCD_LISTEN_CLIENT_URLS="http://172.20.10.13:2379,http://127.0.0.1:2379"
   ETCD_INITIAL_ADVERTISE_PEER_URLS="http://172.20.10.13:2380"
   ETCD_ADVERTISE_CLIENT_URLS="http://172.20.10.13:2379"
   ETCD_INITIAL_CLUSTER="master=http://172.20.10.11:2380,node1=http://172.20.10.12:2380,node2=http://172.20.10.13:2380"
   ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
   ETCD_INITIAL_CLUSTER_STATE="new"
   ```

   





## 五、启动

1. 查看版本号

   ```shell
   etcdctl version
   ```

   ```shell
   # 查看别的节点的版本号可加参数
   etcdctl --endpoints=127.0.0.1:2379 version
   ```

   

2. 启动

   ```shell
   # 各个节点
   systemctl start etcd
   ```

   

3. 查看集群成员信息

   ```
   etcdctl member list 
   ```

   

4. 查看集群状态（Leader节点）

   ```
   V3版本  etcdctl --write-out=table endpoint status
   V2版本  etcdctl cluster-health
   ```

   ```
   --write-out=table   V3版本的命令行参数，以表格形式打印
   ```

   

5. 查看Leader状态

   ```shell
   curl http://127.0.0.1:2379/v2/stats/leader
   ```

   

6. 查看自己状态

   ```sh
   curl http://127.0.0.1:2379/v2/stats/self
   ```



## 六、主要命令

1. 键值写入，读取，删除

   ```shell
   # 写入
   V3版本  etcdctl put /message Hello
   V2版本  etcdctl set /message Hello
   
   # 读取
   etcdctl get /message
   etcdctl get mess --prefix
   curl http://127.0.0.1:2379/v2/keys/message
   
   # 获取key列表
   curl http://127.0.0.1:2379/v2/keys
   
   # 删除
   V3版本 ./ectdctl del /message
   V2版本 ./etcdctl rm /message
         curl -X DELETE http://127.0.0.1:2379/v2/keys/message
   ```

   

2. 过期时间

   ```sh
   V2版本 ./etcdctl set -ttl 10 /message Hello
   ```

   

3. 观察者

   ```shell
   # 在一个终端
   curl http://127.0.0.1:2379/v2/keys/foo?wait=true
   # 该终端会进入等待返回状态
   
   # 在另一个终端改变它的值
   curl http://127.0.0.1:2379/v2/keys/foo -XPUT -d value=bar
   
   # 返回第一个终端观察变化，也可以等待指定的index，这个index时node属性中的modifiedIndex
   curl 'http://127.0.0.1:2379/v2/keys/foo?wait=true&waitIndex=14'
   ```

   

4. 原子操作

   ```shell
   # 当条件成立时设置key值
   curl http://127.0.0.1:2379/v2/keys/foo?prevExist=false -XPUT -d value=three
   # 支持的判断条件有：prevValue，prevIndex，prevExist
   
   # 当条件成立时删除key
   curl http://127.0.0.1:2379/v2/keys/foo?prevValue=two -XDELETE
   # 支持的判断条件有：prevValue，preIndex
   ```

     

5. 租约（V3版本）

   ```shell
   # 租约（lease），etcd支持申请定时器，比如：申请一个TTL=10s的租约，会返回一个lease ID的标识定时器。可以在put一个key的同时携带lease ID，那么就实现了一个自动过期的key。在etcd中，一个lease可以关联给任意多的key，当lease过期后所有关联的key都将被自动删除。
   
   # 生成
   etcdctl lease grant 300
   lease 2be7547fbc6a5afa granted with TTL(300s)
   
   # 关联租约到key
   etcdctl put test_lease 300 --lease=2be7547fbc6a5afa
   
   # 维持租约
   etcdctl keep-alive 2be7547fbc6a5afa
   
   # 撤销租约
   etcdctl lease revoke 2be7547fbc6a5afa
   ```

   



## 错误解决

1. ```shell
   systemctl start etcd
   # 报错
   Job for etcd.service failed because a timeout was exceeded. See "systemctl status etcd.service" and "journalctl -xe" for details.
   
   # 关闭防火墙
   systemctl stop firewalld.service
   systemctl disable firewalld.service
   # 关闭seliunx
   sudo vi /etc/selinux/config 
   改成disabled
   
   ```

   