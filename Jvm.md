### 双亲委派模型
- 类加载的生命周期  
 - 加载 验证 准备 解析 初始化 使用 卸载
    - 验证 准备 解析  -> 连接
    - 加载 
      > 三个步骤 通过全类名获取此类的二进制字节流，将这个字节流所代表的静态存储结构转化为方法去的运行时数据结构， 内存中生成一个对应的class对象
    - 验证 
      > 1.文件格式验证：验证字节流是否符合Class文件格式的规范；例如：是否以魔术0xCAFEBABE开头（当class文件以二进制形式打开，会看到这个文件头，cafebabe）、主次版本号是否在当前虚拟机的处理范围之内、常量池中的常量是否有不被支持的类型。
      > 2. 元数据验证：对字节码描述的信息进行语义分析（注意：对比javac编译阶段的语义分析），以保证其描述的信息符合Java语言规范的要求；例如：这个类是否有父类，除了java.lang.Object之外
      > 3.字节码验证：通过数据流和控制流分析，确定程序语义是合法的、符合逻辑的。
      > 4.符号引用验证：确保解析动作能正确执行。
    - 准备
      > 准备阶段是正式为类变量分配内存并设置类变量初始值的阶段，这些变量所使用的内存都将在方法区中进行分配。这时候进行内存分配的仅包括类变量（被static修饰的变量），而不包括实例变量，实例变量将会在对象实例化时随着对象一起分配在堆中。其次，这里所说的初始值通常情况下是数据类型的零值。
    - 解析
        > 解析阶段是虚拟机将常量池内的符号引用替换为直接引用的过程。解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用点限定符7类符号引用进行。
    - 初始化
        > 
- 双亲委派机制 
 - 类加载器收到加载请求的时候，先委托给父类加载器，加载不到，委托给子类
 - 等级 
    - BootStrap ClassLoader
        > Bootstrap ClassLoader:这个加载器不是一个Java类，而是由底层的c++实现，负责在虚拟机启动时加载Jdk核心类库（如：rt.jar、resources.jar、charsets.jar等）以及加载后两个类加载器。这个ClassLoader完全是JVM自己控制的，需要加载哪个类，怎么加载都是由JVM自己控制，别人也访问不到这个类
    - ExtClassLoader
        > Extension ClassLoader:是一个普通的Java类，继承自ClassLoader类，负责加载{JAVA_HOME}/jre/lib/ext/目录下的所有jar包。
    - AppClassLoader
        >App ClassLoader：是Extension ClassLoader的子对象，负责加载应用程序classpath目录下的所有jar和class文件。
 - 类加载的两种方式
    - 形式上类似于Class.forName(name,true,currentLoader)。 综上所述，Class.forName 如果调用成功会初始化数据
    - ClassLoader.loadClass 只是装载class，不初始化
    - 通过synchroized防止并发
- 破坏双亲委派模型
    - 从META-INFO/services/获取实现