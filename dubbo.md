### dubbo
    > java高性能开源RPC分布式服务框架
- netty高性能
- zk高可用
- dubbo分层
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
    - 轮询，按公约后的权重 设置 轮询比例
    - 最少活跃调用数，相同活跃数随机调用
    - 一致性hash 相同的请求，总是发给同一提供者
        - 一致性hash
            > 普通hash算法，在分布式场景下，hash值，映射规则可能会发生变化
            > 满足了平衡性，单调性，分散性
            > 通过一个环来实现，顺时针查找距离这个对象hash值最近的机器， 新机器的加减 自动hash到最近的路径
            > 但是这样的话，还是没解决负载不均衡的问题，通过在环上找一组的虚拟节点，达到对称来解决这个问题
- SPI
    - 未使用java的SPI，重新实现了一下 ExtensionLoader来加载SPI
    - 路径 META-INF/dubbo
- 自适应扩展
    - 在 Dubbo 中，很多拓展都是通过 SPI 机制进行加载的，比如 Protocol、Cluster、LoadBalance 等。有时，有些拓展并不想在框架启动阶段被加载，而是希望在拓展方法被调用时，根据运行时参数进行加载。这听起来有些矛盾。拓展未被加载，那么拓展方法就无法被调用（静态方法除外）。拓展方法未被调用，拓展就无法被加载。对于这个矛盾的问题，Dubbo 通过自适应拓展机制很好的解决了。自适应拓展机制的实现逻辑比较复杂，首先 Dubbo 会为拓展接口生成具有代理功能的代码。然后通过 javassist 或 jdk 编译这段代码，得到 Class 类。最后再通过反射创建代理类，整个过程比较复杂。
    - Dubbo 自适应扩展机制解决了在调用扩展接口方法时，扩展类未被加载方法不能调用的问题。
    - 在生成代理动态类的时候，直接生成对应的代理代码，并编译出来代理类
- 服务导出
    - 和spring结合 响应spring的ContextRefreshedEvent时间，来注册所有的接口
    - 配置检查
        - export 是否到处
        - delay 延迟时间
        - ====属性
    - URL装配（采用URL作为配置信息的统一格式，所有扩展点都通过传递URL携带配置信息）
    - 加载注册中心 
    - 创建一个invoker，放入到exporter里面
        - invoker是实体域，dubbo的核心模型
        - 放入前调用注册中心的导出接口，在zk创建节点，完成导出
- 服务引入
    - spring装配的时候，就生成对应的代理类装配 ReferBean
- 服务调用过程
    - 

- 踢出失效的服务
    
- 服务引入

- dubbo分层
    - 接口服务层 Service 业务逻辑API
    - 配置层 对外配置接口 ServiceConfig ReferenceConfig为中心
    - 服务代理层 服务接口透明代理 生成服务的客户端Stub和服务端的Skeleton，以ServiceProxy为中心，
    - 服务注册层 封装服务地址的注册和发现
    - 路由层 封装多个提供者的路由和负载均衡
    - 监控层 RPC调用次数和调用时间监控
    - 远程调用层 封装RPC调用 
    - 信息交换层
    - 网络传输层
    - 数据序列化层

- 注册中心
    - zk 基于watch来做
    - multiCast不需要中心，直接广播地址
    - Redis
    - simple注册中心

- 通信
    - 基于Netty

- 支持协议
    - dubbo 长连接+NIO
    - RMI JDK的RMI协议
    - WebService
    - Http
    - Hessian
    - Memcache
    - Redis