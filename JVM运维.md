### jinfo [option] [args] LVMID 输出参数
**option**
```
-flag: 输出指定args的值
-flags 输出所有JVM参数
-sysprops  输出系统属性  = System.getProperties()
```

### jstack [option] LVMID 输出堆栈  
**option**
```
-F 线程未响应，直接强制输出
-l 堆栈 + 锁
-m 如果有native方法，显示C/C++堆栈
```

### jhat [dumpfile] 分析dump文件
```
-stack false|true
关闭对象分配调用栈跟踪(tracking object allocation call stack)。 如果分配位置信息在堆转储中不可用. 则必须将此标志设置为 false. 默认值为 true.>
-refs false|true
关闭对象引用跟踪(tracking of references to objects)。 默认值为 true. 默认情况下, 返回的指针是指向其他特定对象的对象,如反向链接或输入引用(referrers or incoming references), 会统计/计算堆中的所有对象。>
-port port-number
设置 jhat HTTP server 的端口号. 默认值 7000.>
-exclude exclude-file
指定对象查询时需要排除的数据成员列表文件(a file that lists data members that should be excluded from the reachable objects query)。 例如, 如果文件列列出了 java.lang.String.value , 那么当从某个特定对象 Object o 计算可达的对象列表时, 引用路径涉及 java.lang.String.value 的都会被排除。>
-baseline exclude-file
指定一个基准堆转储(baseline heap dump)。 在两个 heap dumps 中有相同 object ID 的对象会被标记为不是新的(marked as not being new). 其他对象被标记为新的(new). 在比较两个不同的堆转储时很有用.>
-debug int
设置 debug 级别. 0 表示不输出调试信息。 值越大则表示输出更详细的 debug 信息.>
-version
启动后只显示版本信息就退出>
-J< flag >
因为 jhat 命令实际上会启动一个JVM来执行, 通过 -J 可以在启动JVM时传入一些启动参数. 例如, -J-Xmx512m 则指定运行 jhat 的Java虚拟机使用的最大堆内存为 512 MB. 如果需要使用多个JVM启动参数,则传入多个 -Jxxxxxx.
```  

### jmap [option]
***option***
```
dump 生成堆栈转储快照
finalizerinfo : 显示在F-Queue队列等待Finalizer线程执行finalizer方法的对象
heap : 显示Java堆详细信息
histo : 显示堆中对象的统计信息
permstat : 打印Java堆内存的永久保存区域的类加载器的智能统计信息。对于每个类加载器而言，它的名称、活跃度、地址、父类加载器、它所加载的类的数量和大小都会被打印。此外，包含的字符串数量和大小也会被打印。
F : 当-dump没有响应时，强制生成dump快照 
```
常用dump方法: -dump:live,format=b,file=<filename> pid 

### jps [option] [hostid] 显示虚拟机进程
***option***
```
-l 输出主类名或者jar路径
-q 只输出 LVMID
-m 输出JVM启动时传给main的参数
-V 输出JVM启动时指定的JVM参数
```

### jstat [option] LVMID [interval][count]
```
option: 操作参数
interval:连续输出时间间隔
count:连续输出次数
```
#### option参数
> -class: 监视类装载、卸载数量、总空间以及耗费的时间  

> -compiler: 输出JIT编译过的方法数量耗时等  

> -gc: 垃圾回收堆的行为统计  
>> S0C:survivor0区的总容量  
>> S1C:survivor1区的总容量  
>> S0U:survivor0区的已使用容量  
>> S1U:survivor1区的已使用容量  
>> EC:eden区的总容量  
>> EU:eden区的使用量  
>> OC:old区的总容量  
>> OU:old区的已使用容量  
>> PC:当前perm的总容量  
>> PU:当前perm的已使用容量  
>> YGC:young gc次数  
>> YGCT:young gc时间  
>> FGC:full gc次数  
>> FGTT:full gc时间  
>> GCT:GC总时间  

> -gccapacity 同-gc 同时显示各区域的最大最小空间  
>> NGCMN:新生代占用的最小空间  
>> NGCMX:新生代占用的最大空间  
>> OGCMN:老年代占用的最小空间  
>> OGCMX:老年代占用的最大空间  
>> OGC:当前老年代容量  
>> OG:当前老年代空间  
>> PGCMN:perm占用的最小空间
>> PGCMX:perm占用的最大空间

>  -gcutil 同-gc，不过输出的是已使用空间占总空间百分比

> -gccause 垃圾收集统计概述 附加最近两次gc发生的原因
>> LGCC: 最近垃圾回收的原因
>> GCC: 当前垃圾回收的原因

>-gcnew 统计新生代的行为
>> TT:Tenuring threshold 提升阈值
>> MTT:最大的TT
>> DSS:survivor区域大小

> -gcnewcapacoty
> gc与其相对应的内存空间统计
>> NGC:当前年轻代的容量
>> S0CMX:最大的S0空间 
>> S0C:当前S0空间 
>> ECMX:最大eden空间
>> EC:当前eden空间

>-gcold 统计老年代的行为

> -gcoldcapacoty 统计老年代的空间

> -gcpermcapacoty 统计perm的空间

> -printcompilation hotspot编译方法统计
>>Compiled 被执行的编译任务数量
>>Size 方法字节码的字节数
>>Type编译类型
>>Method 编译方法的类名和方法名
