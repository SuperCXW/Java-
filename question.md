1. redis压力大
 > 1.减少big key的情况    
 > 2.检查慢命令，检查复杂命令的使用情况
 > 3.主从+cluster分片
 > 4.集中过期
 > 5.内存占用高，和磁盘发生swap
 > 6.后台持久化备份影响，调整备份周期
 > 7.检查网络情况
2. 序列化的作用
  > 可以将对象数据进行传输
  > 跨平台存储
3. dubbo机制
  > 1.SPI加载功能点
  > 2.actipive 动态加载SPI
4. mvcc
  >
5. SynchronousQueue使用场景
  > 线程间传递数据，生产消费者模型
6. 幂等性怎么解决
  > 前端重复提交 mq重复消费 超时重试
  > 添加流水日志,或者token验证
  > 数据库唯一索引
  > 加乐观锁 
  > 分布式锁
  > 状态机控制
7. 线程安全的List Map 其他List
  >  CopyOnWriteArrayList  读写分离，写的时候，加lock，复制一个数组，结束后指向新容器，解lock，读操作无锁
8. spring循环依赖
> a依赖b b依赖a  
  三级缓存   
    1.singletonObjects 一级缓存，存储单例对象，Bean 已经实例化，初始化完成。
    2.earlySingletonObjects  二级缓存，存储 singletonObject，这个 Bean 实例化了，还没有初始化。
    3.singletonFactories  三级缓存，存储 singletonFactory。
    首先，依次从singletonObjects，earlySingletonObjects，singletonFactories队列中去寻找a对象，发现都没有，返回了null。那么这时候就需要创建B对象
```
Spring解决循环依赖的核心思想在于提前曝光：




a的创建的准备：在创建之前，将a放入到singletonsCurrentlyInCreation队列，表明a正在进行创建。
开始创建a:通过反射创建对象a。
进行创建后的处理：创建a对象以后，将a放入到singletonFactories和registeredSingletons队列,并从earlySingletonObjects中移除。然后进行依赖注入工作，发现有依赖B对象。

这时候进入了B对象的注入过程
首先，依次从singletonObjects，earlySingletonObjects，singletonFactories队列中去寻找b对象，发现都没有，返回了null。那么这时候就需要创建B对象
b的创建的准备工作：在创建之前，将b放入到singletonsCurrentlyInCreation队列，表明b正在进行创建
开始创建b：通过反射创建对象b。
进行创建后的处理：将b放入到singletonFactories和registeredSingletons队列,并从earlySingletonObjects中移除。然后进行依赖注入工作，发现有依赖 A对象。

这时候进入A的注入过程。。。
从singletonObjects中查找a，发现a不存在但是singletonsCurrentlyInCreation队列中有a，那么这时候说明a是在创建过程中的，此处又需要创建，属于循环依赖了。然后去earlySingletonObjects查找，也没发现。那么这时候去singletonFactories队列中去寻找a对象，找到了。这时候将a对象放入到earlySingletonObjects队列，并从singletonFactories中移除。因为发现了a对象，这里直接返回a，此时完成了b对象对A的依赖注入了
b实例化完成，而且依赖也注入完成了，那么进行最后的处理。将b实例从singletonsCurrentlyInCreation队列移除，表明b对象实例化结束。然后将b放入到singletonObjects和registeredSingletons队列，并从singletonFactories和earlySingletonObjects队列移除。最后将b对象注入到a对象中。然后a完成了创建过程。
a实例化完成，而且依赖也注入完成了，那么进行最后的处理。将a实例从singletonsCurrentlyInCreation队列移除，表明a对象实例化结束。然后将a放入到singletonObjects和registeredSingletons队列，并从singletonFactories和earlySingletonObjects队列移除。此时完成了a对象的创建。

A半成品加入第三级缓存
A填充属性注入B -> 创建B对象 -> B半成品加入第三级缓存
B填充属性注入A -> 创建A代理对象，从第三级缓存移除A对象，A代理对象加入第二级缓存（此时A还是半成品，B注入的是A代理对象）
创建B代理对象（此时B是完成品） -> 从第三级缓存移除B对象，B代理对象加入第一级缓存
A半成品注入B代理对象
从第二级缓存移除A代理对象，A代理对象加入第一级缓存


如果 Spring 选择二级缓存来解决循环依赖的话，那么就意味着所有 Bean 都需要在实例化完成之后就立马为其创建代理，而 Spring 的设计原则是在 Bean 初始化完成之后才为其创建代理。所以，Spring 选择了三级缓存。但是因为循环依赖的出现，导致了 Spring 不得不提前去创建代理，因为如果不提前创建代理对象，那么注入的就是原始对象，这样就会产生错误。

```
9. redis快的原因
> 内存存储：Redis是使用内存(in-memeroy)存储,没有磁盘IO上的开销
> 单线程实现：Redis使用单个线程处理请求，避免了多个线程之间线程切换和锁资源争用的开销
> 非阻塞IO：Redis使用多路复用IO技术，在poll，epool，kqueue选择最优IO实现
> 优化的数据结构：Redis有诸多可以直接应用的优化数据结构的实现，应用层可以直接使用原生的数据结构提升性能
10. redis多线程在哪儿
11. 自我介绍
12. 最有技术难度的一件事儿
13. java8特性
14. 乐观锁，悲观锁
15. 云原生
16. redis最新版本
17. 学习途径
18. 分工
19. 写一个sql  查一个班成绩前10名学生，设计一下sql
20. 请设计一个类似微信抢红包系统
21. map的区别与使用场景：hashMap concurrentHashMap  LinkedHashMap  treeMap
22. hashtable与concurrentHashMap的变种？为什么concurrentHashMap查询效率要高于hashtable
23. 讲一讲java并发控制的几个关键字 volitile syschronsize cas
24. java多线程的几个包几个类了解吗?使用场景
reentrolocak  countdownlatch semephore syclebarrier
25. 双亲委派类加载：
26. 线程池参数如何设置：
27. lru的缺点：LRU(Least Recently Used ):淘汰最后被访问时间最久的元素。
缺点：可能会由于一次冷数据的批量查询而误导大量热点的数据。
LFU(Least Frequently Used)：淘汰最近访问频率最小的元素。
缺点：1. 最新加入的数据常常会被踢除，因为其起始方法次数少。 2. 如果频率时间度量是1小时，则平均一天每个小时内的访问频率1000的热点数据可能会被2个小时的一段时间内的访问频率是1001的数据剔除掉 
28. hash一致性协议：
29. oauth2
30. 高可用实现
31. 限流
32. 降级
33. spring bean的生命周期
34. spring 循环依赖
35. redis单线程，所有的方法是不是线程安全的
36. 是不是单线程就是线程安全
37. redis保证性能的方式
38. redis的NIO
39. memcache和redis区别 redis底层怎么分配内存
40. 1.8 中hashmap的改进
41. ArrayList.sort使用的排序算法
42. 快排双指针优化
43. foreach删除  iterator
44. 秒杀系统
45. 怎么设计底层接口表
46. lamada
47. 常见的单例
48. volitile
49. 写一段死锁的代码
50. 多线程打印ABC
51. 红包算法，输入红包金额和领取人数，返回每个人领取的红包金额,金额单位为分
52. 用java代码实现LinkedList的add()和remove()方法。要求自行设计LinkedList数据结构，不要外部类库和辅助函数来处理。
53. beanfactory和ApplicationContext
> beanfactory是一个用来访问 Spring 容器的 root 接口，要访问 Spring 容器，我们将使用 Spring 依赖注入功能，使用 BeanFactory 接口和它的子接口  
ApplicationContext 是 Spring 应用程序中的中央接口，用于向应用程序提供配置信息
它继承了 BeanFactory 接口，所以 ApplicationContext 包含 BeanFactory 的所有功能以及更多功能！它的主要功能是支持大型的业务应用的创建
特性：

Bean instantiation/wiring
Bean 的实例化/串联
自动的 BeanPostProcessor 注册
自动的 BeanFactoryPostProcessor 注册
方便的 MessageSource 访问（i18n）
ApplicationEvent 的发布
与 BeanFactory 懒加载的方式不同，它是预加载，所以，每一个 bean 都在 ApplicationContext 启动之后实例化
这里是 ApplicationContext 的使用例子：

54. varchar和char的区别
```
char 定长 效率高 一般用于固定长度的表单提交数据存储
varchar 不定长 效率低 只能指定最大长度 最后会有一位用来存储字符串长度
```
55. 并发编程三要素 
> 原子 可见 有序  

56. wait/notify范式
```
synchroized (obj) {
  whilt(<condition>) {
    obj.wait
  }
}

sychroized(obj) {
  obj.notify
}
```

57. 单例
```
public class Singleton {
    private volatile static Singleton singleton;

    private Singleton() {
    }

    public static Singleton getSingleton() {
        if (singleton == null) {
            synchronized (Singleton04.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
                return singleton;
            }
        }
        return singleton;
    }
}
```

58. coforeach
> 实现了Iterable就能使用该语法糖

59. redis为什么这么快
> 1. 内存操作
> 2. 单线程 单进程 减少cpu切换，没有多线程协同
> 3. 相较关系型数据库，数据结构简单，
> 4. 网络层基于io多路复用

60. io五种模型
> 1. 阻塞 IO 客户端请求，等待系统态收到数据，返回数据
> 2. 非阻塞IO， 客户端请求， 系统态不阻塞，返回错误，然后客户端轮询获取数据
> 3. IO多路复用 通过一种新的系统调用，一个进程可以监视多个文件描述符，一旦某个描述符就绪，内核kernel能够通知程序进行相应的IO调用，实际上就是某个线程轮询select/epoll来获取可读写的socket连接，和NIO不同，这种轮询，交给了内核态，减少了用户态和内核态之间的交互切换  channle会有四个标志 连接 阻塞 可读 可写，轮询channel根据标志来进行后续操作  
  ```
  1. select 
    > copy_from_user从用户空间拷贝fd_set到内核空间    注册回调函数__pollwait
      遍历所有fd，调用对应的poll方法
  2. poll 解决了select最大连接数的限制
  3. epoll
    event poll 事件驱动，回调网卡设备
  ```
> 4. 异步IO 用户态，调用io，等待内核态完成io后，回调用户态线程
> 5. 信号驱动io模型 数据准备好，内核向用户5态发送一个信号，用户态来read

61. BIO NIO AIO
> 1. BIO 线程阻塞受理IO请求，同步阻塞
> 2. NIO  
    - 实际是linux的nio和多路复用结合
    - 时间驱动，
    - buffer channel  selector  
    - selector 轮询channel这一堆缓冲区，看哪个是可读可写的
    - redis就是这么处理的，selector收集事件，转给后端线程 
> 3. AIO  异步非阻塞
    - 异步IO，其他类型，IO发生时，还是当前线程同步进行，但是AIO是IO读写完成后回调处理的
  
62. jvm锁优化
    - 对于内部锁, jvm会自动进行优化,提升锁使用的效率
      - 锁消除, 基于逃逸分析,分析一个资源会不会被多个线程使用,如果不会的话,去掉这个锁
      > 逃逸是指在方法之内创建的对象，除了在方法体之内被引用之外，还在方法体之外被其他变量引用。也就是，在方法体之外引用方法内的对象。在方法执行完毕之后，方法中创建的对象应该被 GC 回收，但由于该对象被其他变量引用，导致 GC 无法回收.这个无法回收的对象称为“逃逸”对象。Java 中的逃逸分析，就是对这种对象的分析。  
      - 锁粗化 一个代码块中连续使用多个锁进行控制,jit分析后会把这些锁进行合并,减少开销,也避免锁的间隙发生冲突
      - 轻量锁

      - 适应锁
```
https://www.cnblogs.com/wade-luffy/p/5969418.html
偏向锁
大多数情况下锁不仅不存在多线程竞争，而且总是由同一线程多次获得。偏向锁的目的是在某个线程获得锁之后，消除这个线程锁重入（CAS）的开销，看起来让这个线程得到了偏护。另外，JVM对那种会有多线程加锁，但不存在锁竞争的情况也做了优化，听起来比较拗口，但在现实应用中确实是可能出现这种情况，因为线程之前除了互斥之外也可能发生同步关系，被同步的两个线程（一前一后）对共享对象锁的竞争很可能是没有冲突的。对这种情况，JVM用一个epoch表示一个偏向锁的时间戳（真实地生成一个时间戳代价还是蛮大的，因此这里应当理解为一种类似时间戳的identifier）

偏向锁的获取

当一个线程访问同步块并获取锁时，会在对象头和栈帧中的锁记录里存储锁偏向的线程ID，以后该线程在进入和退出同步块时不需要花费CAS操作来加锁和解锁，而只需简单的测试一下对象头的Mark Word里是否存储着指向当前线程的偏向锁，如果测试成功，表示线程已经获得了锁，如果测试失败，则需要再测试下Mark Word中偏向锁的标识是否设置成1（表示当前是偏向锁），如果没有设置，则使用CAS竞争锁，如果设置了，则尝试使用CAS将对象头的偏向锁指向当前线程。

偏向锁的撤销

偏向锁使用了一种等到竞争出现才释放锁的机制，所以当其他线程尝试竞争偏向锁时，持有偏向锁的线程才会释放锁。偏向锁的撤销，需要等待全局安全点（在这个时间点上没有字节码正在执行），它会首先暂停拥有偏向锁的线程，然后检查持有偏向锁的线程是否活着，如果线程不处于活动状态，则将对象头设置成无锁状态，如果线程仍然活着，拥有偏向锁的栈会被执行，遍历偏向对象的锁记录，栈中的锁记录和对象头的Mark Word，要么重新偏向于其他线程，要么恢复到无锁或者标记对象不适合作为偏向锁，最后唤醒暂停的线程。

偏向锁的设置

关闭偏向锁：偏向锁在Java 6和Java 7里是默认启用的，但是它在应用程序启动几秒钟之后才激活，如有必要可以使用JVM参数来关闭延迟-XX：BiasedLockingStartupDelay = 0。如果你确定自己应用程序里所有的锁通常情况下处于竞争状态，可以通过JVM参数关闭偏向锁-XX:-UseBiasedLocking=false，那么默认会进入轻量级锁状态。

自旋锁
线程的阻塞和唤醒需要CPU从用户态转为核心态，频繁的阻塞和唤醒对CPU来说是一件负担很重的工作。同时我们可以发现，很多对象锁的锁定状态只会持续很短的一段时间，例如整数的自加操作，在很短的时间内阻塞并唤醒线程显然不值得，为此引入了自旋锁。

所谓“自旋”，就是让线程去执行一个无意义的循环，循环结束后再去重新竞争锁，如果竞争不到继续循环，循环过程中线程会一直处于running状态，但是基于JVM的线程调度，会出让时间片，所以其他线程依旧有申请锁和释放锁的机会。

自旋锁省去了阻塞锁的时间空间（队列的维护等）开销，但是长时间自旋就变成了“忙式等待”，忙式等待显然还不如阻塞锁。所以自旋的次数一般控制在一个范围内，例如10,100等，在超出这个范围后，自旋锁会升级为阻塞锁。

轻量级锁
加锁

线程在执行同步块之前，JVM会先在当前线程的栈桢中创建用于存储锁记录的空间，并将对象头中的Mark Word复制到锁记录中，官方称为Displaced Mark Word。然后线程尝试使用CAS将对象头中的Mark Word替换为指向锁记录的指针。如果成功，当前线程获得锁，如果失败，则自旋获取锁，当自旋获取锁仍然失败时，表示存在其他线程竞争锁(两条或两条以上的线程竞争同一个锁)，则轻量级锁会膨胀成重量级锁。

解锁

轻量级解锁时，会使用原子的CAS操作来将Displaced Mark Word替换回到对象头，如果成功，则表示同步过程已完成。如果失败，表示有其他线程尝试过获取该锁，则要在释放锁的同时唤醒被挂起的线程。

重量级锁
重量锁在JVM中又叫对象监视器（Monitor），它很像C中的Mutex，除了具备Mutex(0|1)互斥的功能，它还负责实现了Semaphore(信号量)的功能，也就是说它至少包含一个竞争锁的队列，和一个信号阻塞队列（wait队列），前者负责做互斥，后一个用于做线程同步。
```


63. 1.8的变化
  > Lambda表达式：Lambda允许把函数作为一个方法的参数（函数作为参数传递到方法中）。
    方法引用：方法引用提供了非常有用的语法，可以直接引用已有Java类或对象（实例）的方法或构造器。与lambda联合使用，方法引用可以使语言的构造更紧凑简洁，减少冗余代码。
    默认方法：默认方法就是一个在接口里面有了一个实现的方法。
    新工具：新的编译工具，如：Nashorn引擎 jjs、 类依赖分析器jdeps。
    Stream API：新添加的Stream API（java.util.stream） 把真正的函数式编程风格引入到Java中。
    Date Time API：加强对日期与时间的处理。
    Optional类：Optional 类已经成为 Java 8 类库的一部分，用来解决空指针异常。
    Nashorn，JavaScript引擎：JDK1.8提供了一个新的Nashorn javascript引擎，它允许我们在JVM上运行特定的javascript应用。

64. Redis满了怎么办
  > https://juejin.cn/post/6844904166154846221淘汰 + 集群部署 (三种, 客户端 代理 服务端)
65. redis部署方式
  > redis分片+主从
66. 击穿雪崩穿透
  - 击穿 查询大量无效key 设置空白值防止击穿
  - 穿透 热点key失效 大量并发过来, 全到数据库 永久key 或者 并发锁控制,其他请求降级
  - 雪崩 大量key同时失效
67. gc算法优缺点
  - https://www.cnblogs.com/zhqin/p/12218864.html
68. 线上的jvm参数怎么设置
69. 如果请求很慢,但是缓存,数据库优化都做了,怎么办
70. Jvm内存模型
  - https://www.jianshu.com/p/bf158fbb2432
71. threadlocal 数据维护在哪里
  - 
72. mysql如何保证acid
73. 怎么处理幻读
74. 分库分表策略
  - hash
  - 地域
  - id分段
  - 时间分片
75. Hash分库分表缺点
76. https://tech.meituan.com/2016/12/02/performance-tunning.html系统如何优化
77. 单例实现 http://c.biancheng.net/view/1338.html
78. https://tech.meituan.com/2016/06/24/java-hashmap.html hashmap
# dubbo
--- 

1、Dubbo是什么？

2、为什么要用Dubbo？

3、Dubbo 和 Spring Cloud 有什么区别？

4、dubbo都支持什么协议，推荐用哪种？

5、Dubbo需要 Web 容器吗？

6、Dubbo内置了哪几种服务容器？

7、Dubbo里面有哪几种节点角色？

8、画一画服务注册与发现的流程图

9、Dubbo默认使用什么注册中心，还有别的选择吗？

10、Dubbo有哪几种配置方式？

11、Dubbo 核心的配置有哪些？

12、在 Provider 上可以配置的 Consumer 端的属性有哪些？

13、Dubbo启动时如果依赖的服务不可用会怎样？

14、Dubbo推荐使用什么序列化框架，你知道的还有哪些？

15、Dubbo默认使用的是什么通信框架，还有别的选择吗？

16、Dubbo有哪几种集群容错方案，默认是哪种？

17、Dubbo有哪几种负载均衡策略，默认是哪种？

18、注册了多个同一样的服务，如果测试指定的某一个服务呢？

19、Dubbo支持服务多协议吗？

20、当一个服务接口有多种实现时怎么做？

21、服务上线怎么兼容旧版本？

22、Dubbo可以对结果进行缓存吗？

23、Dubbo服务之间的调用是阻塞的吗？

24、Dubbo支持分布式事务吗？

25、Dubbo telnet 命令能做什么？
> ls 列出提供的服务名  
> invoke 发起调用  
> ps 显示服务端口 服务地址  
> cd 设置缺省服务  
> pwd 显示缺省服务  
> trace 显示服务调用情况  
> count统计服务调用数量  
> shutdown 关闭dubbo应用
> status 显示汇总情况

26、Dubbo支持服务降级吗？

27、Dubbo如何优雅停机？

28、服务提供者能实现失效踢出是什么原理？
> zk的临时节点机制

29、如何解决服务调用链过长的问题？
> 业内使用zipkin来做soa的分布式日志追踪

30、服务读写推荐的容错策略是怎样的？

31、Dubbo必须依赖的包有哪些？
>JDK

32、Dubbo的管理控制台能做什么？

33、说说 Dubbo 服务暴露的过程。

34、Dubbo 停止维护了吗？

35、Dubbo 和 Dubbox 有什么区别？

36、你还了解别的分布式框架吗？

37、Dubbo 能集成 Spring Boot 吗？

38、在使用过程中都遇到了些什么问题？

39、你读过 Dubbo 的源码吗？

40、你觉得用 Dubbo 好还是 Spring Cloud 好？  

---