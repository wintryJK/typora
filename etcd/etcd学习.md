# ETCD原理

作者：杨健康

## 一、配置

1. 配置标记

   对于标记 `--my-flag` 环境变量的格式是 `ETCD_MY_FLAG`。 适用于所有标记。

   [正式的ectd端口](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml?search=etcd) 是 2379 用于客户端连接，而 2380 用于伙伴通讯。etcd 端口可以设置为接受 TLS 通讯，non-TLS 通讯，或者同时有 TLS 和 non-TLS 通讯。

   为了在 linux 启动时使用自定义设置自动启动 etcd ，强烈推荐使用 [systemd](http://freedesktop.org/wiki/Software/systemd/)单元。

2. 成员标记

   **--name**

   - 成员的可读性的名字.
   - 默认: “default”
   - 环境变量: ETCD_NAME
   - 这个值被作为这个节点自己的入口中被引用， 在 `--initial-cluster` 标记(例如, `default=http://localhost:2380`)中列出。如果使用 [static bootstrapping](https://www.bookstack.cn/read/etcd/documentation-op-guide-clustering.md#static),这需要匹配在标记中使用的key。当使用发现时，每个成员必须有唯一名字。`Hostname` 或 `machine-id` 可以是一个好选择。

   **--data-dir**

   - 到数据目录的路径.
   - 默认: “${name}.etcd”
   - 环境变量: ETCD_DATA_DIR

   **--wal-dir**

   - 到专用的 wal 目录的路径。如果这个标记被设置，etcd将写 WAL 文件到 walDIR 而不是 dataDIR。这容许使用专门的硬盘，并帮助避免日志和其他IO操作之间的IO竞争。
   - 默认: “”
   - 环境变量: ETCD_WAL_DIR

   **--snapshot-count**

   - 触发快照到硬盘的已提交事务的数量.
   - 默认: “10000”
   - 环境变量: ETCD_SNAPSHOT_COUNT

   **--heartbeat-interval**

   - 心跳间隔时间 (单位 毫秒).
   - 默认: “100”
   - 环境变量: ETCD_HEARTBEAT_INTERVAL

   **--election-timeout**

   - 选举的超时时间(单位 毫秒). 阅读 [Documentation/tuning.md](https://github.com/coreos/etcd/blob/master/Documentation/tuning.md#time-parameters) 得到更多详情.
   - 默认: “1000”
   - 环境变量: ETCD_ELECTION_TIMEOUT

   **--listen-peer-urls**

   用于监听伙伴通讯的URL列表。这个标记告诉 etcd 在特定的 scheme://IP:port 组合上从它的伙伴接收进来的请求。scheme 可是 http 或者 https。如果IP被指定为0.0.0.0,etcd 在所有接口上监听给定端口。如果给定IP地址和端口，etcd 将监听在给定端口和接口上。多个URL可以用来指定多个地址和端口来监听。etcd将从任何列出来的地址和端口上应答请求。

   - 默认: “[http://localhost:2380](http://localhost:2380/)“
   - 环境变量: ETCD_LISTEN_PEER_URLS
   - 例子: “[http://10.0.0.1:2380](http://10.0.0.1:2380/)“
   - 无效例子: “[http://example.com:2380](http://example.com:2380/)“ (对于绑定域名无效)

   **--listen-client-urls**

   用于监听客户端通讯的URL列表。这个标记告诉 etcd 在特定的 scheme://IP:port 组合上从客户端接收进来的请求。scheme 可是 http 或者 https。如果IP被指定为 0.0.0.0,etcd 在所有接口上监听给定端口。如果给定IP地址和端口，etcd 将监听在给定端口和接口上。多个 URL 可以用来指定多个地址和端口来监听。etcd 将从任何列出来的地址和端口上应答请求。

   - 默认: “[http://localhost:2379](http://localhost:2379/)“
   - 环境变量: ETCD_LISTEN_CLIENT_URLS
   - 例子: “[http://10.0.0.1:2379](http://10.0.0.1:2379/)“
   - 无效例子: “[http://example.com:2379](http://example.com:2379/)“ (对于绑定域名无效)

   **--max-snapshots**

   - 保持的快照文件的最大数量 (0 表示不限制)
   - 默认: 5
   - 环境变量: ETCD_MAX_SNAPSHOTS
   - 对于 windows 用户默认不限制，而且推荐手工降低到5（或者某些安全偏好)。

   **--max-wals**

   - 保持的 wal 文件的最大数量 (0 表示不限制)
   - 默认: 5
   - 环境变量: ETCD_MAX_WALS
   - 对于windows用户默认不限制，而且推荐手工降低到5（或者某些安全偏好)。

   **--cors**

   - 逗号分割的 origin 白名单，用于 CORS (cross-origin resource sharing/跨 origin 资源共享).
   - 默认: none
   - 环境变量: ETCD_CORS

3. 集群标记

   `--initial` 前缀标记用于启动([static bootstrap](https://www.bookstack.cn/read/etcd/documentation-op-guide-clustering.md#static), [discovery-service bootstrap])(clustering.md#discovery) 或 [runtime reconfiguration](https://www.bookstack.cn/read/etcd/documentation-op-guide-runtime-configuration.md)) 新成员, 然后当重新启动一个已有的成员时被忽略。

   `--discovery` 前缀标记在使用[发现服务](https://www.bookstack.cn/read/etcd/documentation-op-guide-clustering.md#discovery)需要设置.

   **--initial-advertise-peer-urls**

   列出这个成员的伙伴 URL 以便通告给集群的其他成员。这些地方用于在集群中通讯 etcd 数据。至少有一个必须对所有集群成员可以路由的。这些 URL 可以包含域名。

   - 默认: “[http://localhost:2380](http://localhost:2380/)“
   - 环境变量: ETCD_INITIAL_ADVERTISE_PEER_URLS
   - 例子: “[http://example.com:2380](http://example.com:2380/), [http://10.0.0.1:2380](http://10.0.0.1:2380/)“

   **--initial-cluster**

   为启动初始化集群配置。

   - 默认: “default=[http://localhost:2380](http://localhost:2380/)“
   - 环境变量: ETCD_INITIAL_CLUSTER
   - key是每个提供的节点的 `--name` 标记的值. 默认为这个 key 使用 `default` 因为这是 `--name` 标记的默认值.

   **--initial-cluster-state**

   初始化集群状态(“new” or “existing”)。在初始化静态(initial static)或者 DNS 启动 (DNS bootstrapping) 期间为所有成员设置为 `new` 。如果这个选项被设置为 `existing` , etcd 将试图加入已有的集群。如果设置为错误的值，etcd 将尝试启动但安全失败。

   - 默认: “new”
   - 环境变量: ETCD_INITIAL_CLUSTER_STATE

   **--initial-cluster-token**

   在启动期间用于 etcd 集群的初始化集群记号(cluster token)。

   - 默认: “etcd-cluster”
   - 环境变量: ETCD_INITIAL_CLUSTER_TOKEN

   **--advertise-client-urls**

   列出这个成员的客户端URL，通告给集群中的其他成员。这些 URL 可以包含域名。

   - 默认: “[http://localhost:2379](http://localhost:2379/)“
   - 环境变量: ETCD_ADVERTISE_CLIENT_URLS
   - 例子: “[http://example.com:2379](http://example.com:2379/), [http://10.0.0.1:2379](http://10.0.0.1:2379/)“

   小心，如果来自集群成员的通告 URL 比如 [http://localhost:2379](http://localhost:2379/) 正在使用 etcd 的 proxy 特性。这将导致循环，因为代理将转发请求给它自己直到它的资源(内存，文件描述符)最终耗尽。

   **--discovery**

   用于启动集群的发现URL。

   - 默认: none
   - 环境变量: ETCD_DISCOVERY

   **--discovery-srv**

   用于启动集群的 DNS srv 域名。

   - 默认: none
   - 环境变量: ETCD_DISCOVERY_SRV

   **--discovery-fallback**

   当发现服务失败时的期待行为(“exit” 或 “proxy”). “proxy” 仅支持 v2 API.

   - 默认: “proxy”
   - 环境变量: ETCD_DISCOVERY_FALLBACK

   **--discovery-proxy**

   用于请求到发现服务的 HTTP 代理。

   - 默认: none
   - 环境变量: ETCD_DISCOVERY_PROXY

   **--strict-reconfig-check**

   拒绝将导致法定人数丢失的重配置请求。

   - 默认: false
   - 环境变量: ETCD_STRICT_RECONFIG_CHECK

   **--auto-compaction-retention**

   自动压缩用于 mvcc 键值存储的保持力(注：应该指多版本保存)，单位小时。 0 表示关闭自动压缩。

   - 默认: 0
   - 环境变量: ETCD_AUTO_COMPACTION_RETENTION

   > 注： 对于服务注册等只保存运行时动态信息的场合，建议开启。完全没有理由损失存储空间和效率来保存之前的版本信息。推荐设置为1,每小时压缩一次。