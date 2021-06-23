# RocketMQ
- 核心组件
    - consumer
    > 推和拉消费模式，支持集群消费，广播消息  
    - provider
    - nameserver
    > 主要负责对于源数据的管理，包括了对于topic和路由信息的管理  
    > 类似zk 更加轻量级。每个节点之间是独立，没有交互    
    > 每个节点都有完整的路由信息  无状态节点   
    > 两大功能 broker管理 路由管理  
    - broker
    > 消息中转角色，负责存储消息，转发消息  
    > broker是具体提供业务的服务器，单个broker节点与所有的nameserver节点保持长连接和心跳，定时注册topic信息到nameserver，基于netty实现  注册的时候，需要向每一个nameserver去注册
    > 负责消息存储 以topic为维度支持轻量级的队列，单机支持上万队列
    > remoting modulebroker 实体 处理clients端请求
    > client manager 负责管理客户端 和维护topic的订阅情况
    > store service 提供简单的api接口处理消息存储到物理磁盘和查询功能
    > HA Service 高可用提供master-slave之间的同步
    > index service 根据特定的message key 对投递到broker的消息进行索引服务，以提供消息的快速查询
- 大致流程
    - broker启动会去nameserver注册，定时发送心跳，producer在启动时会到nameserver上拉取topic所属的broker地址，然后，向具体的broker发送消息
- 领域模型
    - message
    - topic
    > topic 表示消息的第一级类型，区分不同的消息，最细粒度的订阅单位，group可以订阅多个topic  tag用来二级类型
    - queue
    > 消息的物理管理单位 ，一个topic下可以有多个queue，提供了水品扩展的能力
    - offset
    - group
    > 订阅多个topic
- 特性
    - 顺序消息
    > 相同id的消息放到同一个队列，同一个队列的消息只能由一个消费者来消费  
    > 全局顺序 对于同一个topic，严格按照FIFO消息进行发布和消费
    > 分区顺序 对于一个topic，所有消息根据sharding key进区块分区，同一个分区内的消息按照严格的FIFO顺序来发布消费，shardingkey是消息中用来区分不同分区的字段，
    - 重复消息
    > Qos(服务质量)定义  最多一次 至少一次 仅一次  
    > 1. 业务保证幂等性  
    > 2. 去重策略  保证每条信息都有唯一的流水号
    - 消息可靠性
    > 消息持久化，主从同步，保证可靠性
    - 回溯消费 如果消息需要重新消费，消息会保留，等待下次消费
    - 事务消息
    - 定时消息
    - 消息重试
- 存储
    - 单个broker下所有对立共用一个日志数据文件(commitlog)  针对producer和consumer分别采用了数据和索引部分相分离的存储结构
    - 页缓存是OS对文件的缓存，用于加速文件的读写，一般来说，文件顺序读写速度接近内存读取速度，主要原因是OS使用page cache对读写访问操作进行了性能优化，将一部分的内存用作page cache，对于数据的写入，OS会先写入到cache内，然后异步方式通过内核线程写道物理磁盘上。 对于数据的读取，如果一次读取文件时出现未命中pagecache的情况，会顺序对其他相邻块的数据预读取   
    另外Mmap的方式，减少了内存和磁盘写入映射的时间
    - 刷盘  
        - 同步刷盘
        > 消息持久化到磁盘，才给producer一个ack，可靠性高
        - 异步刷盘
        > 写入pagecache就ack，性能高
- 通信
    - borker启动后需要把自己注册到nameserver，之后每隔30s定时提交topic信息
    - producer作为客户端发送消息时，需要根据消息的topic从本地缓存的topicpublishinfotable获取路由信息，如果没有则更新路由信息，从nameserver上重新拉取，默认30s去更新一次
    - producer根据nameserver拿到的路由信息，选择一个队列发送消息，broker接收消息，落盘
    - consumer 根据nameserver中的路由信息，负载均衡后，选择一个或几个队列拉取消息消费
       