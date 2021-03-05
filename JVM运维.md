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