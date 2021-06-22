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
  > 添加流水日志,或者token验证
  > 数据库唯一索引
  > 加锁
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
> 4. 异步IO 用户态，调用io，等待内核态完成io后，回调用户态线程
> 5. 信号驱动io模型 数据准备好，内核向用户5态发送一个信号，用户态来read

61. BIO NIO AIO
> 1. BIO 线程阻塞受理IO请求，同步阻塞
> 2. NIO  
    - 时间驱动，
    - buffer channel  selector  
    - selector 轮询channel这一堆缓冲区，看哪个是可读可写的
    - redis就是这么处理的，selector收集事件，转给后端线程 
> 3. AIO  异步非阻塞
    - 异步IO，其他类型，IO发生时，还是当前线程同步进行，但是AIO是IO读写完成后回调处理的

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