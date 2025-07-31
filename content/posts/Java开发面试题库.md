+++
title = "Java开发面试题库"
date = "2025-07-31T01:16:47+07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "connor"
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
+++

# Java 面试题库（完整版，含标准答案）

---

## 模块一：Java 基础（共 10 题）

### 1. Java 的基本数据类型有哪些？
Java 有 8 种基本数据类型：byte、short、int、long、float、double、char、boolean。

### 2. 面向对象的三大特性是什么？
封装、继承、多态。

### 3. String 为什么是不可变的？
因为 String 被 final 修饰，底层使用 char 数组存储，一旦创建不可更改，提高安全性和效率（如在 HashMap 中）。

### 4. ArrayList 和 LinkedList 的区别？
- ArrayList：基于数组，查找快，插入删除慢。
- LinkedList：基于链表，插入删除快，查找慢。

### 5. 重载和重写的区别？
- 重载（Overload）：同一类中方法名相同，参数不同。
- 重写（Override）：子类重写父类方法，参数和返回值相同。

### 6. 抽象类和接口的区别？
- 抽象类可以包含实现的方法，接口只能包含抽象方法（Java 8 以后允许 default 方法）。
- 一个类只能继承一个抽象类，但可以实现多个接口。

### 7. Java 中的异常体系？
- Checked Exception（受检异常）：如 IOException。
- Unchecked Exception（运行时异常）：如 NullPointerException。

### 8. final、finally 和 finalize 的区别？
- final：修饰不可变内容。
- finally：异常处理中始终执行的代码块。
- finalize：垃圾回收前调用的方法（不推荐使用）。

### 9. Java 中的包装类有哪些？
Boolean、Byte、Character、Short、Integer、Long、Float、Double。

### 10. Java 如何实现反射？
通过 `Class.forName()`、`getDeclaredMethods()`、`getConstructor()` 等方法反射获取类信息并动态操作。

---

## 模块二：JVM 与性能优化（共 10 题）

### 1. JVM 内存模型？
程序计数器、虚拟机栈、本地方法栈、堆、方法区。

### 2. 什么是类加载器？
BootstrapClassLoader、ExtensionClassLoader、AppClassLoader，遵循双亲委派模型。

### 3. JVM 垃圾回收器有哪些？
Serial、Parallel、CMS、G1、ZGC、Shenandoah 等。

### 4. Minor GC 和 Full GC 的区别？
- Minor GC 发生在新生代，速度快。
- Full GC 会回收整个堆，包括老年代，速度慢。

### 5. 判断对象是否可以被 GC 回收的依据？
引用计数法、可达性分析算法（GC Roots）。

### 6. Java 中的强、软、弱、虚引用？
- 强引用：默认引用类型。
- 软引用：内存不足时回收。
- 弱引用：下一次 GC 时就回收。
- 虚引用：仅用于追踪对象是否被 GC。

### 7. 如何查看 Java 堆栈信息？
使用 jps、jstack、jmap、VisualVM 工具。

### 8. 内存泄漏和内存溢出的区别？
- 泄漏：对象不再使用但未被回收。
- 溢出：申请的内存超出最大限制。

### 9. 方法区和堆的区别？
- 堆存放实例对象。
- 方法区（元空间）存储类结构、常量池等。

### 10. JVM 调优常用参数？
如 `-Xms`, `-Xmx`, `-XX:+UseG1GC` 等。

---

## 模块三：多线程与并发（共 10 题）

### 1. 创建线程的方式有哪些？
继承 Thread 类、实现 Runnable 接口、实现 Callable 接口。

### 2. synchronized 的作用？
可用于修饰方法或代码块，确保线程安全。

### 3. volatile 的作用？
保证变量的可见性，不保证原子性。

### 4. 什么是线程死锁？如何避免？
多个线程互相等待资源，避免策略包括锁顺序、定时锁、死锁检测机制等。

### 5. ThreadLocal 的作用？
为每个线程提供独立的变量副本，适用于线程封闭。

### 6. 线程池的构造参数？
corePoolSize、maximumPoolSize、keepAliveTime、workQueue、threadFactory、handler。

### 7. Java 并发包 J.U.C 有哪些工具类？
CountDownLatch、CyclicBarrier、Semaphore、ReentrantLock、BlockingQueue、Atomic 系列类等。

### 8. CAS 是什么？
比较并交换，无锁并发的核心，原子性操作的基础。

### 9. 什么是 AQS？
AbstractQueuedSynchronizer，用于构建锁和同步器的框架。

### 10. Java 中的锁有哪些？
公平锁/非公平锁、可重入锁、读写锁、偏向锁、轻量级锁、重量级锁。

---

## 模块四：Spring & Spring Boot（共 10 题）

### 1. Spring 的核心模块有哪些？
Core、Context、Beans、AOP、Web、ORM 等。

### 2. Spring Bean 生命周期？
实例化 -> 设置属性 -> 初始化（@PostConstruct）-> 使用 -> 销毁（@PreDestroy）。

### 3. IoC 和 DI 是什么？
- IoC：控制反转。
- DI：依赖注入，是实现 IoC 的一种方式。

### 4. AOP 实现原理？
基于动态代理（JDK、CGLIB）实现横切逻辑。

### 5. Spring Boot 的自动配置原理？
基于 @EnableAutoConfiguration 和 META-INF/spring.factories。

### 6. @Autowired 和 @Resource 区别？
- @Autowired：按类型注入。
- @Resource：按名称注入。

### 7. Spring 常见注解？
@Component、@Service、@Repository、@Controller、@RestController、@Bean、@Configuration。

### 8. Spring MVC 请求流程？
DispatcherServlet -> HandlerMapping -> Controller -> ViewResolver。

### 9. Spring 中的事务管理？
使用 @Transactional，支持声明式和编程式事务。

### 10. 如何排查 Spring 启动失败？
查看日志、排查依赖冲突、使用 `--debug` 模式。

---

## 模块五：数据库与缓存（共 10 题）

### 1. 索引的底层原理？
一般使用 B+ 树，便于范围查询和排序。

### 2. 主键索引和普通索引区别？
主键索引唯一且不能为 null，普通索引可重复。

### 3. Redis 常见数据结构？
String、List、Set、ZSet、Hash、Bitmap、HyperLogLog。

### 4. Redis 过期策略？
定时删除、惰性删除、定期删除。

### 5. Redis 缓存雪崩、击穿、穿透？
- 雪崩：大量 key 同时失效。
- 击穿：热点 key 失效。
- 穿透：请求不存在的数据。

### 6. MySQL 的事务隔离级别？
Read Uncommitted、Read Committed、Repeatable Read、Serializable。

### 7. InnoDB 和 MyISAM 区别？
- InnoDB 支持事务和外键，支持行级锁。
- MyISAM 不支持事务，锁粒度较大。

### 8. Redis 持久化方式？
RDB（快照）、AOF（追加日志）、混合持久化。

### 9. 什么是慢查询？如何优化？
执行时间超过指定阈值的 SQL，可通过索引、优化 SQL、加缓存等方法优化。

### 10. 分库分表的常见策略？
按用户 ID 哈希、时间分表、水平垂直分拆等。

---

## 模块六：分布式系统设计（共 10 题）

### 1. 什么是微服务架构？
将单体应用拆分为多个小型服务，通过接口进行通信。

### 2. 常见的注册中心有哪些？
Eureka、Consul、Zookeeper、Nacos。

### 3. 分布式系统中的一致性问题如何解决？
使用分布式事务、最终一致性、幂等性处理。

### 4. 分布式锁的实现方式？
基于 Redis（SETNX + EX）、Zookeeper（临时顺序节点）。

### 5. 什么是服务雪崩？如何避免？
服务依赖失败导致系统整体崩溃，可通过熔断、限流、降级机制避免。

### 6. CAP 原理与 BASE 理论？
- CAP：一致性、可用性、分区容忍性。
- BASE：基本可用、软状态、最终一致性。

### 7. 常见的服务调用方式？
REST、RPC（如 Dubbo、gRPC）、消息队列异步调用。

### 8. 微服务的配置管理怎么做？
使用 Nacos、Apollo、Spring Cloud Config 等。

### 9. 网关的作用和常见实现？
统一入口、安全认证、路由转发，如 Nginx、Spring Cloud Gateway。

### 10. 如何实现链路追踪？
使用 Sleuth、Zipkin、SkyWalking、Jaeger 等组件。

---

**提示：** 本题库适用于笔试、自测、面试准备，也适合整理为答题卡和知识地图。


# Java开发面试题库

## 一、Java基础
### ✅ 基础语法
**Q: Java中==与equals的区别？**
A: `==` 比较的是两个对象的引用地址是否相同，`equals()` 比较的是对象的内容是否相同（前提是该类重写了equals方法）。

**Q: String为什么是不可变的？**
A: 因为String类被final修饰，内部字符数组也是final，且没有提供修改的方法，这样可以保证安全性、线程安全，并能用于常量池优化。

**Q: String、StringBuilder、StringBuffer的区别？**
A:
- String：不可变，适合少量拼接。
- StringBuilder：可变，单线程高效。
- StringBuffer：可变，线程安全但效率较低。

**Q: 抽象类与接口的区别？**
A:
- 抽象类可以包含成员变量和构造函数；接口只能有常量。
- 一个类只能继承一个抽象类，但可以实现多个接口。
- 接口更侧重于行为约定，抽象类是类层次结构的一部分。

**Q: final关键字的使用场景？**
A:
- final变量：不可被重新赋值。
- final方法：不可被子类重写。
- final类：不可被继承。

## 二、集合框架
**Q: HashMap的底层实现？**
A: 底层由数组 + 链表/红黑树组成。Java 8之后，当链表长度超过8时转为红黑树。

**Q: ConcurrentHashMap如何实现线程安全？**
A: Java 8中使用 CAS + synchronized 实现分段锁保护节点，提高并发性能。

**Q: Set如何保证元素不重复？**
A: 依赖元素的 `hashCode()` 和 `equals()` 方法判断重复。

## 三、并发编程
**Q: volatile关键字的原理与使用？**
A: 保证可见性，禁止指令重排序，不保证原子性。适用于状态标志。

**Q: 死锁产生的条件及解决办法？**
A: 四个条件：互斥、不可剥夺、请求与保持、循环等待。
解决方案：打破其中一个条件，例如通过资源顺序获取避免循环等待。

**Q: ThreadLocal作用与底层原理？**
A: 为每个线程提供独立变量副本。底层通过Thread类中的ThreadLocalMap实现。

## 四、JVM与性能优化
**Q: JVM内存结构？**
A: 包括程序计数器、虚拟机栈、本地方法栈、堆、方法区。

**Q: Minor GC与Full GC区别？**
A: Minor GC主要回收新生代，Full GC会回收整个堆（包括老年代）与方法区，开销更大。

## 五、Spring相关
**Q: Bean的生命周期？**
A: 实例化 -> 设置属性 -> BeanNameAware -> BeanFactoryAware -> PostConstruct -> InitializingBean -> 自定义init方法 -> 使用 -> PreDestroy -> DisposableBean -> 自定义destroy方法。

**Q: Spring的AOP底层实现？**
A: 基于动态代理（JDK Proxy 或 CGLIB），使用切点表达式和通知方法完成横切逻辑织入。

## 六、数据库与MyBatis
**Q: MySQL索引底层结构？**
A: 使用B+树结构，叶子节点存储数据，所有查询都从根节点向下查找。

**Q: #{} 和 ${} 的区别？**
A: `#{}`是预编译处理，防止SQL注入；`${}`是字符串拼接，可能会有注入风险。

## 七、网络与分布式
**Q: TCP三次握手过程？**
A:
1. 客户端发送SYN请求；
2. 服务器返回SYN+ACK；
3. 客户端发送ACK，连接建立。

**Q: CAP理论解释？**
A: 一致性（C）、可用性（A）、分区容错性（P），在分布式系统中三者不可兼得，只能选择其中两者优先。

**Q: 分布式锁的实现方式？**
A:
- 基于数据库（悲观锁）
- Redis实现：setnx + 过期时间
- ZooKeeper临时顺序节点

## 八、项目经验相关
**Q: 项目中遇到的最大挑战是什么？**
A: 举例：高并发下系统响应慢，采用缓存+异步队列+数据库优化方式解决，响应速度提升50%。

---

> 建议：熟练掌握每个问题的底层原理和应用场景，在面试中主动引导话题更容易脱颖而出。

