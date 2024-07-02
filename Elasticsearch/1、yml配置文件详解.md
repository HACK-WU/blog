```shell
#是不是有资格成为主节点
node.master: true
#表示该节点可以存储索引数据，默认为true
node.data: true
#指定ES可监听网络地址范围
#0.0.0.0 表示任何ip地址都可访问
#127.0.0.1 表示只能本地地址访问
network.host: 0.0.0.0
http.port: 9200
#ES节点使用的传输层TCP端口，就是ES用于与其他es节点通信的端口
transport.tcp.port: 9636
#discovery.zen.minimum_master_nodes参数用于设置在进行主节点选举时所需的最小主节点数量。
#默认情况下，该值是1，表示只需要一个主节点就可以进行选举。
#但是，在具有更大规模和高可用性要求的集群中，建议将该值设置为大部分可用主节点数的一半加1。
#例如，如果您的集群中有3个可用主节点，则将discovery.zen.minimum_master_nodes设置为2
discovery.zen.minimum_master_nodes: 2
#discovery.zen.ping.unicast.hosts参数用于设置集群中的主机列表，以便ES节点通过单播方式进行集群发现。
#您可以将该参数设置为一个包含集群中其他节点的IP地址或主机名的字符串数组。
discovery.zen.ping.unicast.hosts: ["10.32.2.99", "10.32.2.100"]
#thread_pool.bulk.queue_size是Elasticsearch（ES）的配置参数，用于设置Bulk线程池队列的大小。
#Bulk线程池用于处理索引和删除请求，它负责对大量的文档进行批量操作。
#当有大量的索引或删除请求发送到ES时，这些请求将被放入Bulk线程池的队列中进行处理。
#thread_pool.bulk.queue_size参数指定了该队列的最大容量。
thread_pool.bulk.queue_size: 200
#thread_pool.index.queue_size是Elasticsearch（ES）的配置参数，用于设置Index线程池队列的大小。
#Index线程池用于处理索引请求，它负责将文档写入索引。
#当有大量的索引请求发送到ES时，这些请求将被放入Index线程池的队列中进行处理。
#thread_pool.index.queue_size参数指定了该队列的最大容量。
thread_pool.index.queue_size: 1000
#action.destructive_requires_name是Elasticsearch（ES）的配置参数，用于限制具有破坏性操作
#（如删除索引、删除文档等）的执行。当action.destructive_requires_name设置为true时，执行此类操作时必须提
#供名称以确认操作的意图。
#这个配置参数旨在防止意外的数据丢失或损坏，因为它要求在执行破坏性操作之前明确确认操作名称。
action.destructive_requires_name: true
#indices.recovery.max_bytes_per_sec是Elasticsearch（ES）的配置参数，用于限制索引恢复过程中的网络吞吐量。
#它控制每秒从远程源节点传输到恢复目标节点的最大数据量。
indices.recovery.max_bytes_per_sec: 10mb
#indices.requests.cache.size是Elasticsearch（ES）的配置参数，用于设置请求缓存的大小。该参数控制可以缓存的搜索请求结果的容量。
#在ES中，请求缓存是一个用于缓存搜索请求结果的机制。当执行相同的搜索请求时，如果缓存中已存在该请求的结果，则可以直接从缓存中获取结果，
#而无需再次执行搜索操作。这样可以提高搜索性能和响应时间，特别是对于重复性较高的搜索请求。
#在以下示例中，我们将indices.requests.cache.size配置为5%，表示请求缓存的大小为索引总容量的5%。
indices.requests.cache.size: 5%
#indices.queries.cache.size 设置为 5%，表示查询缓存将使用可用堆内存的 5% 作为其最大大小。
查询缓存是一种用于缓存经常使用的查询结果的机制，以提高查询性能和降低对底层存储的负载。当相同或类似的查询被频繁执行时，
#Elasticsearch 可以从查询缓存中返回已缓存的结果，而无需再次执行实际的查询操作。
indices.queries.cache.size: 5%
#indices.queries.cache.count: 20000
indices.queries.cache.count: 20000
#在 Elasticsearch（ES）服务配置中，local.terms.lookup.cache.size: 1G 是一个用于配置本地术语查找缓存大小的参数。它指定了用于存储术语查找结果的内存容量。
local.terms.lookup.cache.size: 1G
#本地存储节点的最大数量限制为1
node.max_local_storage_nodes: 1
#指定用于存储索引数据的路径
path.data: /opt/data
#指定日志文件的存储路径
path.log: /opt/logs
#path.repo是Elasticsearch（ES）的配置参数，用于指定用于备份和还原操作的仓库路径。通过配置
#path.repo，您可以为ES指定一个或多个本地或远程路径，作为仓库存储位置。
path.repo: ["/backup/repo1", "/backup/repo2"]
#bootstrap.memory_lock 设置为true时，表示Elasticsearch将尝试锁定分配给其堆内存的物理内存，以防止被交换到磁盘上的交换空间。
#这对于确保ES的性能和稳定性非常有用。但要启用此功能，需要以root用户或具有特定权限的用户身份运行Elasticsearch进程。
bootstrap.memory_lock: true
#bootstrap.system_call_filter: false：设置为false时，表示Elasticsearch将禁用针对系统调用的过滤和限制。
#默认情况下，ES会使用Seccomp进行系统调用过滤，以提高安全性和稳定性。禁用此功能可能会降低一些安全性，
#但也可能在某些特定情况下解决一些与系统调用过滤相关的问题。
bootstrap.system_call_filter: false
# head 插件需要这打开这两个配置
http.cors.allow-origin: "*"
http.cors.enabled: true
http.max_content_length: 200mb
#es7.x 之后新增的配置，初始化一个新的集群时需要此配置来选举 master
cluster.initial_master_nodes: ["node-1"]
#es7.x 之后新增的配置，节点发现
#用于设置节点在初始启动时用于发现其他节点的种子主机列表。
discovery.seed_hosts: ["linux1:9300","linux2:9300","linux3:9300"]
gateway.recover_after_nodes: 2
network.tcp.keep_alive: true
network.tcp.no_delay: true
transport.tcp.compress: true
#集群内同时启动的数据任务个数，默认是 2 个
cluster.routing.allocation.cluster_concurrent_rebalance: 16
#添加或删除节点及负载均衡时并发恢复的线程个数，默认 4 个
cluster.routing.allocation.node_concurrent_recoveries: 16
#初始化数据恢复时，并发恢复线程的个数，默认 4 个
cluster.routing.allocation.node_initial_primaries_recoveries: 16
```