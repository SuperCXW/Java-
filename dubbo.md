### dubbo
    > java高性能开源RPC分布式服务框架
- netty高性能
- zk高可用
- 参与节点
    - priovider
    - consumer
    - contianer
    - registry
    - monitor
- 核心配置
    - dubbo:service
    - dubbo:reference
    - dubbo:protocol
    - dubbo:application
    - dubbo:module
    - dubbo:registry
    - dubbo:monitor
    - dubbo:provider
    - dubbo:consumer
    - dubbo:method
    - dubbo:argument
- provider配置
    - timeout
    - retries
    - loadbalance
    - actives 最大并发限制
- 启动会检查provider是否正常
- 序列化，默认hessian
- 默认netty 还有mina grizzly
- 高可用
    - 负载均衡
    - 集群容错
    - 服务路由
- 集群容错方案
    - failover Cluster失败自动切换
    - failfast Cluster快速失败 只调用一次
    - failSafe Cluster失败安全 出现异常直接忽略
    - failback Cluster 失败自动恢复 记录失败请求，定时重发
    - Forking Cluster 并行调用多个服务提供者
    - Broadcast Cluster 广播逐个调用提供者
- 默认负载均衡策略
    - 随机 按权重设置随机概率
    - 轮询，按公约后的权重设置轮询比例
    - 最少活跃调用数，相同活跃数随机调用
    - 一致性hash 相同的请求，总是发给同一提供者
        - 一致性hash
            > 普通hash算法，在分布式场景下，hash值，映射规则可能会发生变化
            > 满足了平衡性，单调性，分散性
            > 通过一个环来实现，顺时针查找距离这个对象hash值最近的机器， 新机器的加减 自动hash到最近的路径
            > 但是这样的话，还是没解决负载不均衡的问题，通过在环上找一组的虚拟节点，达到对称来解决这个问题