# redis

# 数据结构
- 字符串
    - 动态字符串
        - 单独记录字符串长度 
        - 杜绝缓存区溢出
        - 减少修改字符串长度时所需的内存重分配次数
        - 二进制安全
        - 兼容部分C字符串函数
- 链表
    - 双向链表
    - 头尾都指向null
    - 单独的链表长度计数器
- 字典
    - 基于hash表来实现
    - 有两个hash表 ,一个做正常使用 ,一个在rehash时使用
- set
- zset
    - 基于跳表实现

- 跳表
    - 通过在每个节点中维持多个指向其他节点的指针,从而达到快速访问节点的目的.平均O(logN) 最坏 O(N)复杂度的节点查找,还可以通过顺序性操作来批量处理节点
    - zskiplist 保存跳跃表的信息
        - header 头节点
        - tail  尾节点
        - level  层数最大的那个节点的层数
        - length 记录跳表的长度
    - zskiplistNode  跳跃表节点
        - level 节点中用L1 L2 L3等字样标记节点的各个层  每个层都包含两个属性 前进指针的跨度,就是这一层,对应下一个节点有多远  作用类似多级索引,加快查找速度
        - bw指针 它指向位于当前节点的前一个节点.后退指针在程序从表尾向表头遍历时使用
        - 分值 各个节点中的 1.0 2.0 3.0是节点所保存的分值.在跳跃表,在跳跃表中,节点按各自所保存的分值从小到大排列.
        - 成员对象 各个节点中的o1 o2 o3是节点所保存的成员对象

- 整数集合
    - 集合键的底层实现之一,当一个集合只包含整数值元素,并且这个集合的元素数量不多是,redis就会使用整数集合作为集合键的底层实现
    - 记录了 编码方式 元素数量 保存元素的数组
    - 升级
        - 根据新元素的类型,扩展整数集合底层数组的空间大小,并为新元素分配空间
        - 将底层数组现有的所有元素都转换成与新元素相同的类型, 并将转换后的元素放到正确的位置上
        - 将新元素放到底层数组里面
        - 提升了灵活性 节约内存 无法降级

- 压缩链表
    - 压缩链表是列表键和hash键的底层实现之一,当一个列表键只包含少量的列表项,并且每个列表项要么是小整数值,要么就是长度比较短的字符串, 那么redis就会启用 压缩列表
    - 组成部分
        - zlbytes 整个链表占用内存字节数
        - zltail 链表尾相对链表开始的偏移量
        - zllen 压缩列表的节点数量 大于65535 就需要自己遍历
        - entry 节点内容
            - previous_entry_length前一个节点的长度, 方便遍历
            - encoding 节点的content数据类型和长度 
            - content
            - 会出现连锁更新的问题,因为要维护上一个节点的大小,而上一个节点的大小又会影响这个属性占用的大小 但是发生可能性不大
        - zlend 标记列表的末端

# redis 对象
   - redis用对象来保存数据库中的键和值,每次新建一个键值对的时候,都会创建两个对象,一个是key 一个是value
   - redis object 存储 type encoding ptr
    - key永远是字符串对象
    - value 可以能是五种基本类型
    - 底层实现可以根据场景动态决定
        - string   
            - int
            - embstr 使用embstr编码的简单动态字符串实现的字符串对象 减少内存分配次数 但是是只读的,会转化为raw进行修改
            - 使用简单动态字符串实现的字符串对象
            - 浮点数会被转换为字符串保存
            - 只有string对象会被其他类型对象嵌套使用
        - list 
            - 使用压缩链表实现的列表对象 两个条件 每个字符串都小于64字节 元素小于512个 可配置修改
            - 使用双端链表实现的列表对象
        - hash
            - 使用压缩列表实现的hash对象  推入key再推入value 保证两个相邻 条件同上
            - 使用字典实现的hash对象
        - set
            - 使用整数集合实现的集合对象  都是数字并且不超过512个
            - 使用字典实现的集合对象 key是元素 value为null
         - zset
            - 使用压缩列表实现的有序集合对象  数据和分值连续放在一起
            - 使用跳跃表和字典实现的有序集合对象 结合字典的查询效率和跳跃表的范围操作性能

# 命令
 - llen 多态
 - del

# 过期键删除策略
  - 定时删除 每个键有对应的timer  内存友好 cpu不友好
  - 惰性删除 放任键过期不管,但是每次键空间中获取键时,都检查取得的键是否过期 cpu最友好,内存不友好
  - 定期删除 每隔一段时间,程序就对数据库进行检查 删除里面的过期键 制定合适的定期策略
  - RDB 不会加载过期键  slave不会判断
  - AOF 追加一条DEL指令
  - AOF 重写(做一些aof日志的整合) 不会记录过期键
  - 复制 不会删除过期键,只会接受主服务器的del命令式处理

# 主从复制
  - 同步 用于将从服务器的数据库状态更新至主服务器当前所处的数据库状态
  - 命令传播操作 则用于在主服务器的数据库状INFO命令接受回复 一个是订阅连接 订阅主服务器的sentinel:hello频道



# 锁
- https://www.cnblogs.com/niceyoo/p/13711149.html
- setnx
- lua脚本
- redisson
    - 可重入
    - 公平锁
    - 连锁
        - 基于Redis的分布式RedissonMultiLock对象将多个RLock对象分组，并将它们作为一个锁处理。每个RLock对象可能属于不同的Redisson实例。
    - 红锁
        - 为了解决主从同步问题还有脑裂问题,引入redlock
        - 假设有5个redis节点，这些节点之间既没有主从，也没有集群关系。客户端用相同的key和随机值在5个节点上请求锁，请求锁的超时时间应小于锁自动释放时间。当在3个（超过半数）redis上请求到锁的时候，才算是真正获取到了锁。如果没有获取到锁，则把部分已锁的redis释放掉。
    - 读写锁
    - Semaphore

# 数据结构  
   -  sds 动态字符串  
   - 不以/0结尾,单独记录可用可用空间和剩余空间
   - 容易获取长度
   - 杜绝缓冲期溢出
   - 惰性释放空间,减少内存重分配
   - 二进制安全,不会被/0截断
   - 兼容部分C函数



# 布隆过滤器 
- 实际上是一个很长的二进制向量和一些列随机映射函数，布隆过滤器可以用于检索一个元素是否在一个集合中。他的优点是空间效率和查询时间都比一般的算法好很多，缺点是有一定的误识别率和删除困难
- 根据定义，布隆过滤器可以检查值是"可能在集合中"还是"绝对不在集合中" 可能，表示有一定的概率，也就是有误判率  如果不在，一定不在；如果在，有可能是hash碰撞，其实是不在的，发生了误判
- 多个hash函数结果，填入一个长度为m的位向量或位列表 0000000
 一个值放进来，多个hash函数计算未知，写入向量
 查找时，计算向量，然后看向量中是否存在，存在就有可能在，不存在一定不在


## 数据一致性
-  先更新数据库 再cache 有可能数据不一致 低并发场景
-  先删除缓存,再改数据库 会有间隙导致脏数据 低并发
-  更新数据库 延迟异步删除cache 弱一致性
- 更新cache,延迟异步入库
- https://note.dolyw.com/cache/00-DataBaseConsistency.html#%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88