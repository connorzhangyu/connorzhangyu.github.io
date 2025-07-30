---
title: Java
tags:
  - Java
  - Spring
categories:
  - Java
  - Java
cover: https://image.runtimes.cc/202404051529974.png
abbrlink: 20128
date: 2022-11-19 18:25:00
updated: 2023-11-28 11:10:30
---
# 基础知识点

**循环标签**

如果不在嵌套循环中使用,用了跳转,也就相当于`continue`一样,没有区别

在嵌套循环中使用才有效果,用了`innerLoop`之后,可以看到

* j=2 的时候是不打印
* j=3,程序不会到j=3的时候,因为j=2的时候就已经跳出去了

```java
/**
 * 0====0
 * 0====1
 * 1====0
 * 1====1
 * 2====0
 * 2====1
 * -------------------
 * 0====0
 * 0====1
 * 0====2
 * 0====3
 * 1====0
 * 1====1
 * 1====2
 * 1====3
 * 2====0
 * 2====1
 * 2====2
 * 2====3
 */
public static void main(String[] args) {


    innerLoop:
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 4; j++) {
            if (j == 2) {
                continue innerLoop;
            }
            System.out.println(i+"===="+j);
        }
    }
}
```



# Java常用的API方法

## 集合/列表/数组/Map

### 数组拼接

```java
// 把数组风格,再循环添加逗号
String[] split = result.split(" ");
StringJoiner stringJoiner = new StringJoiner(",");
Arrays.stream(split).forEach(stringJoiner::add);

// 字符串数组数字,转成int类型,再转成数组
Arrays.stream(split).mapToInt(Integer::parseInt).toArray()

// BigDecimal想加,然后如果为空,就给个0
BigDecimal winAmount = list.stream()
                  .map(UserBetOrderRecord::getWinAmount)
                  .reduce(BigDecimal::add)
                  .map(bigDecimal -> bigDecimal.multiply(new BigDecimal("-1")))
                  .orElse(BigDecimal.ZERO);
```

### 数组转集合

```java
public static void test() {
 
  	int[] arr = new int[]{4, 3, 2, 1, 12, 21, 11};
  
  	// 这样转是有问题的,不是想要的
  	// java.util.array
    List<int[]> list = Arrays.asList(arr);
    System.out.println(list);
    
   	// 使用Hutool的方法,也不死想要的
  	// cn.hutool.core.collection
	  List<int[]> ints = CollectionUtil.newArrayList(arr);
    System.out.println(ints);
  
  	// 使用Spring的工具包,需要强转,强迫症受不了这种方法
  	// org.springframework.util.CollectionUtils
    List<Integer> objects = (List<Integer>) CollectionUtils.arrayToList(arr);
    System.out.println(objects);
}
```

### 打印数组/集合/Map

```java
private static void test() {

    int[] arr = {1, 2, 3, 4, 5};
    System.out.println(arr);    // [I@17211155
    System.out.println(Arrays.toString(arr));   // [1, 2, 3, 4, 5]

    List<Integer> integers = new ArrayList<>();
    integers.add(1);
    integers.add(2);
    integers.add(3);
    System.out.println(integers);   // [1, 2, 3]

    HashMap<Integer, String> integerStringHashMap = new HashMap<>();
    integerStringHashMap.put(1, "a");
    integerStringHashMap.put(2, "b");
    integerStringHashMap.put(3, "c");
    System.out.println(integerStringHashMap);   // {1=a, 2=b, 3=c}

}
```

### 数组截取

```java
// java.util.Arrays
// 从0开始,取出6个数
private static void test() {
    int[] arr = {1, 2, 3, 4, 5, 6};
    int[] ints = Arrays.copyOfRange(arr, 0, 6);
    System.out.println(Arrays.toString(ints));
}
```

## 格式化

### 数值格式化

```java
private static void test() {
    String format = String.format("%02d", 2);
    System.out.println(format);  // 02

    String format2 = String.format("%02d", 20);
    System.out.println(format2);	// 20
}
```

## 数学

### 余数/取模

```java
private static void test() {

    int a = 5;
    int b = 3;

    // 使用 % 运算符取余数
    int remainder = a % b;
    System.out.println(remainder); // 2

    // 使用 Math.floorMod() 方法取余数
    int floorMod = Math.floorMod(a, b);
    System.out.println(floorMod); // 2

}
```

### BigDecimal比较

```java
public static void main(String[] args) {
      BigDecimal a = new BigDecimal(10);
      BigDecimal b = new BigDecimal(5);

      int i = a.compareTo(b);
      switch (i) {
          case -1: // 小于
              System.out.println("a < b");
              break;
          case 0: // 等于
              System.out.println("a = b");
              break;
          case 1: // 大于
              System.out.println("a > b");
              break;
      }

      if (a.compareTo(b) != 0) {
          System.out.println("a != b");
      }

      if (a.compareTo(b) != -1){
          System.out.println("a >= b");
      }

      if (a.compareTo(b) != 1) {
          System.out.println("a <= b");
      }
  }
```

## 时间类型比较

```java
public static void main(String[] args) throws ParseException {

      SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
      Date begin = sdf.parse("2020-01-01 00:01:00");
      Date end = sdf.parse("2020-01-01 00:02:00");

      // 使用compareTo方法
      int i = begin.compareTo(end);
      switch (i) {
          case -1:
              System.out.println("开始时间大于结束时间");
              break;
          case 0:
              System.out.println("开始时间等于结束时间");
              break;
          case 1:
              System.out.println("开始时间小于结束时间");
              break;
      }

      // 使用before和after方法
      boolean b = begin.before(end);
      System.out.println(b);

      boolean c = begin.after(end);
      System.out.println(c);
  }
```

## 值传递和引用传递

```java
public class Demo {
    public static void main(String[] args) {
        HashMap<Integer, User> ingUserHashMap = new HashMap<Integer, User>();

        User user = new User();
        user.setId(1);
        ingUserHashMap.put(1, user);

        // {1=User(id=1, bigDecimal=10)}
        System.out.println(ingUserHashMap);

      	// 从map里取值,然后再修改
        User user1 = ingUserHashMap.get(1);
        user1.setBigDecimal(BigDecimal.ONE);

      	// 打印map是修改了的
        // {1=User(id=1, bigDecimal=1)}
        System.out.println(ingUserHashMap);
    }
}

@Data
@ToString
class User{
    int id;
    BigDecimal bigDecimal = BigDecimal.TEN;
}
```

## Lambda
给集合分组
```java
public class Demo {

    public static void main(String[] args) {
        ArrayList<User> list = new ArrayList<>();
        list.add(new User(1, "anthony"));
        list.add(new User(2, "anthony"));
        list.add(new User(1, "didid"));

        // 按照用户Id分组
        Map<Integer, List<User>> collect = list.stream().collect(Collectors.groupingBy(User::getId));
        // {1=[User(id=1, name=anthony), User(id=1, name=didid)], 2=[User(id=2, name=anthony)]}
        System.out.println(collect);

        // 1==[User(id=1, name=anthony), User(id=1, name=didid)]
        // 2==[User(id=2, name=anthony)]
        collect.forEach((id,userList)->{
            System.out.println(id+"=="+userList);
        });
    }
}
@AllArgsConstructor
@Data
class User{
    Integer id;
    String name;
}
```



# ThreadLocal

[掘金](https://juejin.cn/post/7126708538440679460#heading-13)



# HashMap

面试八股文....

**HashMap和HashTable的区别**

|        | HashMap                                                      | HashTable                 |
| ------ | ------------------------------------------------------------ | ------------------------- |
| null值 | 允许key,value为null,但最多允许一条记录的key为null            | 不允许有null              |
| 安全性 | 不安全,<br />Collections.synchronizedMap方法，使HashMap具有线程安全的能力 | 线程安全的                |
| 遍历   | Iterator 进行遍历                                            | 使用 Enumeration 进行遍历 |

**负载因子**

负载因子和扩容机制有关,意思是如果当前容器的容量,达到我们设置的定最大值,就要开始扩容

> 比如说当前的容器容量是16，负载因子是0.75,16\*0.75=12，也就是说，当容量达到了12的时候就会进行扩容操作。

负载因子是1的时候:

只有当数组的16个一直都填满了,才回扩容,因此在填满的过程中,Hash冲突是避免不了的,也就意味着会出现大量的Hash冲突,底层的红黑树就变得复杂,这种就牺牲了时间,保证空间的利用率

负载因为是0.5的时候:

当到达数组一半的就开始扩容,既然填充的元素少了,Hash冲突就变少,那么底层的链表长度和红黑数也就变少,查询效率就高, 原本存储1M的数据,现在需要2M,时间效率提升了,但是空间利用率降低了

**不同JDK版本HashMap的区别**

|      |                                                              |                                        |
| ---- | ------------------------------------------------------------ | -------------------------------------- |
| 组成 | 数组+链表                                                    | 数组+链表+红黑树                       |
|      | hash 值我们能够快速定位到数组的具体下标，但是之后的话， 需要顺着链表一个个比较下去才能找到我们需要的，时间复杂度取决于链表的长度 | 当满足一定条件时，会将链表转换为红黑树 |

**链表转红黑树要满足的条件 **

1. 链表中的元素个数>=8,为什么?

   和hashcode碰撞次数的泊松分布有关,主要是为了寻找一种时间和空间的平衡

   红黑数的TreeNode 是 链表中的Node所占空间的2倍,虽然红黑树的查询效率要高于链表,
   但是,当链表长度比较小的时候,即使全部遍历,时间复杂度也不会太高

   为什么是8,JDK源码也没有说,只是说做了很多时间,经验的问题
   


   红黑树转链表的阀值是6,因为:避免红黑树和链表频繁转换

2. 当前数组的长度>=64,为什么?

   链表转为红黑树的目的是为了解决链表过长,导致查询和插入的效率慢的问题

   如果要解决这个问题,也可以通过数组扩容,把链接缩短也可以解决问题

   所以在数组长度还不太长的情况,可以先通过数组扩容来解决链表过长的问题

满足这两个条件才会变成红黑树

**HashMap的数组的大小是2的幂**

> TODO 还是没有搞明白
>
> 只有数组长度是2的幂次方倍才能够确保数组中的每一个位置发生hash冲突的概率是相同的，数组长度减一的二进制码必须全部是1，否则会出现部分位置永远不会发生hash冲突而造成资源浪费

**HashMap put流程**

1. 对key进行hash算法，得到一个hashcode
2. 如果数组长度为0或者为null，对数组进行扩容，得到数组长度n
3. 通过 key的hashcode&数组长度n 计算出数组下标
4. 如果数组下标位置中没有数据，将put进来的数据封装Node对象并存到给下标位置
5. 如果数组下标位置有数据p，即发生hash碰撞，如果key存在，覆盖数据
6. 如果数据p属于红黑树，会把新数据插入到红黑树中
7. 如果以上都不是就遍历链表，遍历链表过程中统计链表长度，当链表长度超过8 进行treeifyBin 红黑树化链表
8. treeifyBin树化中如果数组长度小于64或数组为null则进行扩容，否则数组长度大于等于64 链表转红黑树

**HashMap 是如何扩容的？**

1. HashMap的扩容指的就是数组的扩容， 因为数组占用的是连续内存空间，所以数组的扩容其实只能新开一个新的数组，然后把老数组上的元素转移到新数组上来，这样才是数组的扩容
2. 先新建一个2被数组大小的数组
3. 然后遍历老数组上的每一个位置，如果这个位置上是一个链表，就把这个链表上的元素转移到新数组上去
4. 在jdk8中，因为涉及到红黑树，这个其实比较复杂，jdk8中其实还会用到一个双向链表来维护红黑树中的元素，所以jdk8中在转移某个位置上的元素时，会去判断如果这个位置是一个红黑树，那么会遍历该位置的双向链表，遍历双向链表统计哪些元素在扩容完之后还是原位置，哪些元素在扩容之后在新位置，这样遍历完双向链表后，就会得到两个子链表，一个放在原下标位置，一个放在新下标位置，如果原下标位置或新下标位置没有元素，则红黑树不用拆分，否则判断这两个子链表的长度，如果超过8，则转成红黑树放到对应的位置，否则把单向链表放到对应的位置。
5. 元素转移完了之后，在把新数组对象赋值给HashMap的table属性，老数组会被回收到。

# 多线程

## 线程池

### 手写一个线程池

1:编写任务类(MyTask),实现Runnable接口;

```java
public class MyTask implements Runnable {

    private int id;

    public MyTask(int id) {
        this.id = id;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        System.out.println("线程:"+name + "即将开始任务:" + id);
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        System.out.println("线程:"+name + "完成了任务:" + id);
    }

    @Override
    public String toString() {
        return "MyTask{" + "id=" + id + '}';
    }
}
```

2:编写线程类(MyWorker),用于执行任务,需要持有所有任务;

```java
public class MyWorker extends Thread{

    private List<Runnable> tasks;

    public MyWorker(String name, List<Runnable> tasks) {
        super.setName(name);
        this.tasks = tasks;
    }

    @Override
    public void run() {
        // 判断集合中是否有任务,如果有就一直运行
        while (!tasks.isEmpty()) {
            Runnable remove = tasks.remove(0);
            remove.run();
        }
    }
}
```

3:编写线程池类(MyThreadPool),包含提交任务,执行任务的能力;

```java
public class MyThreadPool {

    // 任务队列
    private List<Runnable> tasks = Collections.synchronizedList(new LinkedList<>());
    // 当前线程数量
    private int num;
    // 核心线程数量
    private int corePoolSize;
    // 最大线程数量
    private int maxSize;
    // 任务队列的长度
    private int workSize;

    public MyThreadPool(int corePoolSize, int maxSize, int workSize) {
        this.corePoolSize = corePoolSize;
        this.maxSize = maxSize;
        this.workSize = workSize;
    }

    // 提交方法-将任务添加到队列中
    public void submit(Runnable runnable){
        // 判断当前队列的数量,是否超出了最大任务数量
        if (tasks.size() >= workSize) {
            System.out.println("任务"+runnable+"被丢弃了");
        }else {
            tasks.add(runnable);

            // 执行方法-判断当前线程的数量,决定创建核心线程数量还是非线程数量
            execTask(runnable);
        }
    }

    private void execTask(Runnable runnable) {
        // 判断当前线程中的线程总数量,是否超出了核心线程数
        if (num < corePoolSize) {
            // 创建核心线程
            new MyWorker("核心线程:"+num, tasks).start();
            num++;
        } else if (num < maxSize) {
            // 创建非核心线程
            new MyWorker("非核心线程:"+num, tasks).start();
            num++;
        }else {
            System.out.println("任务:"+runnable+"被缓存了....");
        }
    }
}
```

4:编写测试类(MyTest),创建线程池对象,提交多个任务测试;

```java
public class MyTest {
    public static void main(String[] args) {
        MyThreadPool myThreadPool = new MyThreadPool(2, 4, 20);
        for (int i = 0; i < 10; i++) {
            MyTask myTask = new MyTask(i);
            myThreadPool.submit(myTask);
        }
    }
}
```

### 内置线程池

ThreadPoolExecutor的使用

```java
public ThreadPoolExecutor(int corePoolSize, //核心线程数量
                              int maximumPoolSize,//     最大线程数
                              long keepAliveTime, //       最大空闲时间
                              TimeUnit unit,         //        时间单位
                              BlockingQueue<Runnable> workQueue,   //   任务队列
                              ThreadFactory threadFactory,    // 线程工厂
                              RejectedExecutionHandler handler  //  饱和处理机制
) 
```

1. corePoolSize - 线程池核心线程的数量，即使线程是空闲的，核心线程也不会被销毁，除非设置了 allowCoreThreadTimeOut 为 true。
2. maximumPoolSize - 线程池中允许的最大线程数。
3. keepAliveTime - 非核心线程空闲时的超时时间，超过这个时间就会被销毁。
4. unit - keepAliveTime 参数的时间单位。
5. workQueue - 用于保存等待执行任务的阻塞队列。
   1. **SynchronousQueue:** 一个不存储元素的阻塞队列。每个插入操作必须等待另一个线程的对应移除操作，反之亦然。适合于传递性的请求。
   2. **LinkedBlockingQueue:** 一个无界队列，使用链表实现。如果没有指定容量，默认将是 Integer.MAX_VALUE。当任务数量超过核心线程数量时，多余的任务将被放置在队列中等待执行。
   3. **ArrayBlockingQueue:** 一个有界的阻塞队列，使用数组实现。在创建时需要指定容量大小。适合于有界任务队列的场景，如数据库连接池和消息队列等。
6. threadFactory - 用于创建新线程的工厂。
7. handler - 当线程池中的线程数达到最大值且阻塞队列已满时，用来处理新提交的任务的饱和策略。
   1. `AbortPolicy`: 默认的策略，将抛出`RejectedExecutionException`。
   2. `CallerRunsPolicy`: 当任务被拒绝添加时，会使用当前线程执行被拒绝的任务。
   3. `DiscardPolicy`: 直接丢弃被拒绝的任务，不提供任何反馈。
   4. `DiscardOldestPolicy`: 丢弃等待时间最长的任务，然后尝试提交新的任务。

ExecutorService接口的常用方法:

| 接口名                                        | 作用                                                         |
| --------------------------------------------- | ------------------------------------------------------------ |
| void shutdown()                               | 启动一次顺序关闭，执行以前提交的任务，但不接受新任务。       |
| List<Runnable> shutdownNow()                  | 停止所有正在执行的任务，暂停处理正在等待的任务，并返回等待执行的任务列表。 |
| <T> Future<T> submit(Callable<T> task)        | 执行带返回值的任务，返回一个Future对象。                     |
| Future<?> submit(Runnable task)               | 执行 Runnable 任务，并返回一个表示该任务的 Future。          |
| <T> Future<T> submit(Runnable task, T result) | 执行 Runnable 任务，并返回一个表示该任务的 Future。          |

#### ExecutorService接口的的实现类

| 方法                                                         |
| ------------------------------------------------------------ |
| static ExecutorService newCachedThreadPool()  <br />创建一个默认的线程池对象,里面的线程可重用,且在第一次使用时才创建 |
| static ExecutorService newCachedThreadPool(ThreadFactory threadFactory)  <br />线程池中的所有线程都使用ThreadFactory来创建,这样的线程无需手动启动,自动执行; |
|                                                              |
| static ExecutorService newFixedThreadPool(int nThreads)   <br />创建一个可重用固定线程数的线程池 |
| static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory)  <br />创建一个可重用固定线程数的线程池且线程池中的所有线程都使用ThreadFactory来创建。 |
|                                                              |
| static ExecutorService newSingleThreadExecutor()   <br />创建一个使用单个 worker 线程的 Executor，以无界队列方式来运行该线程。 |
| static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory)  <br />创建一个使用单个 worker 线程的 Executor，且线程池中的所有线程都使用ThreadFactory来创建。 |

#### ScheduledExecutorService任务调度

ScheduledExecutorService是ExecutorService的子接口,具备了延迟运行或定期执行任务的能力

| 方法()                                                       |
| ------------------------------------------------------------ |
| static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)<br />创建一个可重用固定线程数的线程池且允许延迟运行或定期执行任务; |
| static ScheduledExecutorService newScheduledThreadPool(int corePoolSize, ThreadFactory threadFactory)<br />创建一个可重用固定线程数的线程池且线程池中的所有线程都使用ThreadFactory来创建,且允许延迟运行或定期执行任务; |
|                                                              |
| static ScheduledExecutorService newSingleThreadScheduledExecutor() <br />创建一个单线程执行程序，它允许在给定延迟后运行命令或者定期地执行。 |
| static ScheduledExecutorService newSingleThreadScheduledExecutor(ThreadFactory threadFactory) <br />创建一个单线程执行程序，它可安排在给定延迟后运行命令或者定期地执行。 |

ScheduledExecutorService常用方法如下:

| 方法                                                         |
| ------------------------------------------------------------ |
| <V> ScheduledFuture<V> schedule(Callable<V> callable, long delay, TimeUnit unit) <br />延迟时间单位是unit,数量是delay的时间后执行callable。 |
|                                                              |
| ScheduledFuture<?> schedule(Runnable command, long delay, TimeUnit unit) <br />延迟时间单位是unit,数量是delay的时间后执行command。 |
|                                                              |
| ScheduledFuture<?> scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit) <br />延迟时间单位是unit,数量是initialDelay的时间后,每间隔period时间重复执行一次command。 |
|                                                              |
| ScheduledFuture<?> scheduleWithFixedDelay(Runnable command, long initialDelay, long delay, TimeUnit unit) <br />创建并执行一个在给定初始延迟后首次启用的定期操作，随后，在每一次执行终止和下一次执行开始之间都存在给定的延迟。 |

#### Future异步计算结果

|                                                              |
| ------------------------------------------------------------ |
| boolean cancel(boolean mayInterruptIfRunning) <br />试图取消对此任务的执行。 |
|                                                              |
| V get()<br />如有必要，等待计算完成，然后获取其结果。        |
|                                                              |
| V get(long timeout, TimeUnit unit) <br />如有必要，最多等待为使计算完成所给定的时间之后，获取其结果（如果结果可用）。 |
|                                                              |
| boolean isCancelled() <br />如果在任务正常完成前将其取消，则返回 true。 |
|                                                              |
| boolean isDone() <br />如果任务已完成，则返回 true。         |

#### 代码演示ExecutorService

```java
// 演示 newCachedThreadPool
public class Demo {
    public static void main(String[] args) {
//        test1();
//        test2();
    }

    private static void test1() {
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < 10; i++) {
            executorService.submit(new MyRunable(i));
        }
    }
    private static void test2() {
        ExecutorService executorService = Executors.newCachedThreadPool(new ThreadFactory() {
            int i=1;
            @Override
            public Thread newThread(Runnable r) {
                return new Thread(r, "自定义线程名字:" + i++);
            }
        });

        for (int i = 0; i < 10; i++) {
            executorService.submit(new MyRunable(i));
        }
    }
}


class MyRunable implements Runnable {

    int i = 0;

    public MyRunable(int i) {
        this.i = i;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        System.out.println("线程:"+name + "执行了任务:"+i);
    }
}
```

```java
// 演示newFixedThreadPool
public class Demo2 {
    public static void main(String[] args) {
//        test1();
        test2();
    }
    private static void test1() {
        ExecutorService executorService = Executors.newFixedThreadPool(3);

        for (int i = 0; i < 10; i++) {
            executorService.submit(new MyRunable2(i));
        }
    }

    private static void test2() {
        ExecutorService executorService = Executors.newFixedThreadPool(3,new ThreadFactory() {
            int i=1;
            @Override
            public Thread newThread(Runnable r) {
                return new Thread(r, "自定义线程名字:" + i++);
            }

        });

        for (int i = 0; i < 10; i++) {
            executorService.submit(new MyRunable2(i));
        }
    }
}


class MyRunable2 implements Runnable {

    int i = 0;

    public MyRunable2(int i) {
        this.i = i;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        System.out.println("线程:"+name + "执行了任务:"+i);
    }
}
```

```java
// 演示newSingleThreadExecutor
public class Demo3 {
    public static void main(String[] args) {
//        test1();
        test2();
    }
    private static void test1() {
        ExecutorService executorService = Executors.newSingleThreadExecutor();

        for (int i = 0; i < 10; i++) {
            executorService.submit(new MyRunable3(i));
        }
    }

    private static void test2() {
        ExecutorService executorService = Executors.newSingleThreadExecutor(new ThreadFactory() {

            int i=1;
            @Override
            public Thread newThread(Runnable r) {
                return new Thread(r, "自定义线程名字:" + i++);
            }

        });

        for (int i = 0; i < 10; i++) {
            executorService.submit(new MyRunable3(i));
        }
    }
}


class MyRunable3 implements Runnable {

    int i = 0;

    public MyRunable3(int i) {
        this.i = i;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        System.out.println("线程:"+name + "执行了任务:"+i);
    }
}
```

```java
// ExecutorService接口的shutdown(),shutdownNow(),submit()
public class Demo4 {
    public static void main(String[] args) {

//        test1();
        test2();

    }

    private static void test2() {
        ExecutorService executorService = Executors.newSingleThreadExecutor(new ThreadFactory() {
            int i=1;
            @Override
            public Thread newThread(Runnable r) {
                return new Thread(r, "自定义线程名字:" + i++);
            }

        });

        for (int i = 0; i < 10; i++) {
            executorService.submit(new MyRunable4(i));
        }
        // 立刻关闭线程池,如果线程池中的还有缓存任务,没有执行,则取消执行,并返回这些任务
        List<Runnable> runnables = executorService.shutdownNow();
        System.out.println(runnables);
        // 不能再接收新的方法了,会报错
        executorService.submit(new MyRunable4(888));

    }

    private static void test1() {
        ExecutorService executorService = Executors.newSingleThreadExecutor();

        for (int i = 0; i < 10; i++) {
            executorService.submit(new MyRunable4(i));
        }
        // 关闭线程池,仅仅是不再接收新的任务,以前的任务还会记录执行
        executorService.shutdown();
        // 不能再接收新的方法了,会报错
        executorService.submit(new MyRunable4(888));

    }
}

class MyRunable4 implements Runnable {

    int i = 0;

    public MyRunable4(int i) {
        this.i = i;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        System.out.println("线程:"+name + "执行了任务:"+i);
    }

    @Override
    public String toString() {
        return "MyRunable4{" +
                "i=" + i +
                '}';
    }
}
```

#### 代码演示ScheduledExecutorService

```java
/**
 * 演示newScheduledThreadPool,测试延迟执行和重复执行的功能
 */
public class Demo {

    public static void main(String[] args) {
        // 获取具备延迟执行任务的线程池对象
        ScheduledExecutorService es = Executors.newScheduledThreadPool(3);


        for (int i = 0; i < 10; i++) {
            // 创建多个任务,并且提交任务,每个任务延迟2s执行
            es.schedule(new MyRunable(i), 2, TimeUnit.SECONDS);
        }
        System.out.println("结束");


    }
}

class MyRunable implements Runnable {

    int i = 0;

    public MyRunable(int i) {
        this.i = i;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        System.out.println("线程:"+name + "执行了任务:"+i);
    }
}
```

```java
// 演示newScheduledThreadPool
public class Demo2 {

    public static void main(String[] args) {
        // 获取具备延迟执行任务的线程池对象
        ScheduledExecutorService es = Executors.newScheduledThreadPool(3, new ThreadFactory() {

            int n = 1;
            @Override
            public Thread newThread(Runnable r) {
                return new Thread("自定义线程:" + n++);
            }
        });


        // 创建多个任务,并且提交任务,每个任务延迟2s执行
        es.scheduleAtFixedRate(new MyRunable2(1), 1,2, TimeUnit.SECONDS);
        System.out.println("结束");


    }
}

class MyRunable2 implements Runnable {

    int i = 0;

    public MyRunable2(int i) {
        this.i = i;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        try {
            Thread.sleep(1500);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }

        System.out.println("线程:"+name + "执行了任务:"+i);
    }
}
```

```java
// 演示newSingleThreadScheduledExecutor
public class Demo3 {

    public static void main(String[] args) {
        // 获取具备延迟执行任务的线程池对象
        ScheduledExecutorService es = Executors.newSingleThreadScheduledExecutor( new ThreadFactory() {

            int n = 1;
            @Override
            public Thread newThread(Runnable r) {
                return new Thread("自定义线程:" + n++);
            }
        });


        // 创建多个任务,并且提交任务,每个任务延迟2s执行
        es.scheduleWithFixedDelay(new MyRunable2(1), 1,2, TimeUnit.SECONDS);
        System.out.println("结束");


    }
}

class MyRunable3 implements Runnable {

    int i = 0;

    public MyRunable3(int i) {
        this.i = i;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }

        System.out.println("线程:"+name + "执行了任务:"+i);
    }
}
```

#### 代码演示Future

```java
// 
public class Demo5 {

    public static void main(String[] args) throws ExecutionException, InterruptedException, TimeoutException {
        // 创建线程池对象
        ExecutorService executorService = Executors.newCachedThreadPool();
        Future<Integer> submit = executorService.submit(new MyCall(1, 2));
//        test1(submit);
//        test2(submit);
    }

    private static void test2(Future<Integer> submit) throws InterruptedException, ExecutionException, TimeoutException {
        Thread.sleep(1000);
        System.out.println("取消任务执行的结果" + submit.cancel(true));

        // 主线程等待超时
        Integer i = submit.get(1, TimeUnit.SECONDS);
        System.out.println("任务执行的结果:" + i);
    }

    private static void test1(Future<Integer> submit) throws InterruptedException, ExecutionException {
        // 判断任务是否完成
        boolean done = submit.isDone();
        System.out.println("第一次判断任务是否完成:" + done);

        boolean cancelled = submit.isCancelled();
        System.out.println("第一次判断任务是否取消:" + cancelled);

        // 无限期等待
        Integer i = submit.get();
        System.out.println("任务执行的结果:" + i);

        System.out.println("第二  次判断任务是否完成:" + submit.isDone());
        System.out.println("第一次判断任务是否取消:" + submit.isCancelled());
    }
}

class MyCall implements Callable<Integer> {

    int a;
    int b;

    public MyCall(int a, int b) {
        this.a = a;
        this.b = b;
    }

    @Override
    public Integer call() throws Exception {
        String name = Thread.currentThread().getName();
        System.out.println("线程:"+name + "准备开始计算:");
        Thread.sleep(2000);
        System.out.println("线程:"+name + "计算完成:");
        return a+b;
    }
}
```







# JVM

## JVM内存结构

![image-20230825200326130](https://image.runtimes.cc/202404051502261.png)

### 程序计数器

虚拟机指令的执行流程:CPU不是不是直接读二进制字节码 , `解释器`读二进制字节码 转换成 `机器码`,交给CPU执行

![image-20230819130100665](https://image.runtimes.cc/202404051502586.png)

程序计数器的在作用:记住下一跳JVM指令的执行地址

![image-20230819130536093](https://image.runtimes.cc/202404051503947.png)

特点:

线程私有的,

不会存在内存溢出

![image-20230819130807803](https://image.runtimes.cc/202404051502060.png)

### 虚拟机栈

每个栈由多个栈桢组成,对应这每次方法调用的时所占用的内存

每个线程只能有一个活动栈桢,对应着当前正在执行的那个方法

数据结构,像子弹夹,先进后出,所以不需要GC



-Xss 设置每个`线程`栈内存大小,默认1M,内存越大,能使用的线程越少

![image-20230819131121768](https://image.runtimes.cc/202404051502039.png)

演示栈桢

![image-20230819134620208](https://image.runtimes.cc/202404051502380.png)

#### 演示线程安全

方法内的局部变量线程是安全的

```java
public class Demo3 {
    public void test() {
      	// 每个线程,有各自的x
        int x = 0;
        for (int i = 0; i < 1000; i++) {
            x++;
        }
        System.out.println(x);
    }
}
```

![image-20230819135303041](https://image.runtimes.cc/202404051502378.png)

#### 演示线程不安全

如果是静态变量,就不是安全的了

```java
public class Demo3 {

    static int x = 0;

    // 多线程运行
    public void demo() {
        for (int i = 0; i < 1000; i++) {
            x++;
        }
        System.out.println(x);
    }
}
```

![image-20230819135343587](https://image.runtimes.cc/202404051503889.png)

#### 演示栈内存溢出

栈桢过大或者过多会溢出

```java
public class Demo2 {

    private static int count = 0;

    public static void main(String[] args) {
        try {
            method();
        } catch (Throwable e) {
            e.printStackTrace();
	          // 19994
            System.out.println(count);
        }
    }

    private static void  method(){
        count++;
        method();
    }
}


// java.lang.StackOverflowError
// 	at 栈.Demo2.method(Demo2.java:22)
// 	at 栈.Demo2.method(Demo2.java:22)

```

#### 线程诊断

**1.线程CPU占用高**

```shell
# 使用top命令
看哪个进程占用CPU过高,只能看到进程,不能看到线程

# 查看哪个线程占用过高
ps H -eo pid,tid,%cpu | grep 进程id

# jstack 线程id
在具体查找的时候,十进制的线程ID转成16进制
```

**2.运行很长时间,得不到结果**

有可能发生死锁了

### 本地方法栈

native 方法,比如object里的clone方法

### 堆

使用-Xmx 设置堆内存空间大小

特点:

线程共享的,堆中对象需要考虑线程安全的问题

有垃圾回收机制

#### 演示堆内存溢出

```java
public class Demo {
    public static void main(String[] args) {
        int i = 0;
        List<String> List = new ArrayList<>();
        String a = "hello";
        try {
            while (true) {
                List.add(a);
                a = a + a;
                i++;
            }
        } catch (Exception e) {
            e.printStackTrace();
            System.out.println(i);
        }
    }
}
```

#### 演示jmap

```shell
# 查看堆内存使用空间
# 16279是pid
jmap -heap 16279

Error: -heap option used
Cannot connect to core dump or remote debug server. Use jhsdb jmap instead
# 对于jdk8之后的版本，不能再使用jmap -heap pid的命令了
jhsdb jmap --heap --pid 16279
```

#### 演示jconsole

```java
public class Demo2 {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("1.....");
        Thread.sleep(30000);
        byte[] bytes = new byte[1028 * 1024 * 10];
        System.out.println("2.....");
        Thread.sleep(30000);
        bytes = null;
        System.gc();
        System.out.println("3.....");
        Thread.sleep(1000000);
    }
}
```

打印1的时候,是刚开始运行,默认的内存占用

打印2的时候,代码创建了一个10MB数组

打印3的时候,数据被垃圾回收了,还回收了一些默认的创建的空间,所以内存占用就降下来了

![image-20230821154126018](https://image.runtimes.cc/202404051503025.png)

#### 演示jvisualvm

点击gc之后,内存占用不下降的情况

```java

/**
 * 演示jvisualvm
 */
public class Demo3 {
    public static void main(String[] args) throws InterruptedException {

        ArrayList<Persion> persions = new ArrayList<>();
        for (int i = 0; i < 200; i++) {
            persions.add(new Persion());
        }

        Thread.sleep(99999999999999999L);
    }
}

class Persion{
    private byte[] big = new byte[1024 * 1024];
}

```

点击`Heap Dump`

![image-20230821155229892](https://image.runtimes.cc/202404051505544.png)

仔细找找,就能看到那些数据占用的内存比较多

![image-20230821155411806](https://image.runtimes.cc/202404051506269.png)

### 方法区

![image](https://image.runtimes.cc/202404051506319.png)

```shell
演示永久代内存溢出:
# 1.8以前会导致永久代内存溢出
java.lang.OutOfMemoreryError: PermGen space
-XX:MaxPermSize=8m

# 1.8以前会导致元空间内存溢出
java.lang.OutOfMemoreryError: Metaspace
-XX:MaxMetaspaceSize=8m
```

```java
// 没有演示出来
import jdk.internal.org.objectweb.asm.ClassWriter;
import jdk.internal.org.objectweb.asm.Opcodes;

/**
 * 演示元空间内存溢出
 * 方法区内存溢出,1.8才有这个版本
 * -XX:MaxMetaspaceSize=8m
 *
 * ClassLoader:类加载器,可以用来加载类的二进制字节码
 */
public class Demo extends ClassLoader{
    public static void main(String[] args) {
        int j = 0;
        try {
            Demo test = new Demo();
            for (int i = 0; i < 10000; i++, j++) {
                // 生成类的二进制字节码
                ClassWriter cw = new ClassWriter(0);
                // 版本号
                // 访问修饰符
                // 类名
                // 包名
                // 父类
                // 接口名称,这里没有实现接口
                cw.visit(Opcodes.V1_8, Opcodes.ACC_PUBLIC, "Class" + i, null, "java/lang/Object", null);
                // 返回byte数组
                byte[] code = cw.toByteArray();
                // 加载类,生成Class对象
                test.defineClass("Class" + i, code, 0, code.length);
            }
        }finally {
            System.out.println(j);
        }
    }
}
```

运用的场景:

Spring和Mybatis都使用到了CGLIB技术,CGLIB也会像上面演示的使用`ClassWriter`创建对象,创建的对象多了,就会导致方法区异常,所以就移动到直接内存,虽然没啥本质上的作用,只是会影响`OOM`的时间

##### 常量池 

```java
/**
 * 常量池
 * 二进制字节码(类基本信息,常量池,类方法定义,包含了虚拟机指令)
 */
public class Demo3 {
    public static void main(String[] args) {
        System.out.println("Hello world");
    }
}

// 使用命令编译
javap -v Demo3.class
```

编译的结果

```java
// 以下是类的基本信息
Classfile /Users/anthony/Documents/GitHub/study-java/java-base/target/classes/方法区/Demo3.class
  Last modified 2023年8月23日; size 538 bytes
  MD5 checksum 1aca08c8871c6946b2737975bbf15625
  Compiled from "Demo3.java"
public class 方法区.Demo3
  minor version: 0
  major version: 55
  flags: (0x0021) ACC_PUBLIC, ACC_SUPER
  this_class: #5                          // 方法区/Demo3
  super_class: #6                         // java/lang/Object
  interfaces: 0, fields: 0, methods: 2, attributes: 1
    
// 常量池
Constant pool:
   #1 = Methodref          #6.#20         // java/lang/Object."<init>":()V
   #2 = Fieldref           #21.#22        // java/lang/System.out:Ljava/io/PrintStream;
   #3 = String             #23            // Hello world
   #4 = Methodref          #24.#25        // java/io/PrintStream.println:(Ljava/lang/String;)V
   #5 = Class              #26            // 方法区/Demo3
   #6 = Class              #27            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               LocalVariableTable
  #12 = Utf8               this
  #13 = Utf8               L方法区/Demo3;
  #14 = Utf8               main
  #15 = Utf8               ([Ljava/lang/String;)V
  #16 = Utf8               args
  #17 = Utf8               [Ljava/lang/String;
  #18 = Utf8               SourceFile
  #19 = Utf8               Demo3.java
  #20 = NameAndType        #7:#8          // "<init>":()V
  #21 = Class              #28            // java/lang/System
  #22 = NameAndType        #29:#30        // out:Ljava/io/PrintStream;
  #23 = Utf8               Hello world
  #24 = Class              #31            // java/io/PrintStream
  #25 = NameAndType        #32:#33        // println:(Ljava/lang/String;)V
  #26 = Utf8               方法区/Demo3
  #27 = Utf8               java/lang/Object
  #28 = Utf8               java/lang/System
  #29 = Utf8               out
  #30 = Utf8               Ljava/io/PrintStream;
  #31 = Utf8               java/io/PrintStream
  #32 = Utf8               println
  #33 = Utf8               (Ljava/lang/String;)V
                            
// 类的方法定义
{
  public 方法区.Demo3();
    descriptor: ()V
    flags: (0x0001) ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 6: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   L方法区/Demo3;

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: (0x0009) ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=1, args_size=1
        // 虚拟机指令
         0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #3                  // String Hello world
         5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 8: 0
        line 9: 8
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       9     0  args   [Ljava/lang/String;
}
SourceFile: "Demo3.java"
```

##### 串池(StringTable)的特性

常量池中的字符串仅是符号,第一次用到的时候才回变成对象

利用串池的机制,来避免重复创建字符串对象

字符串变量拼接的原理是`StringBuilder`(1.8)

字符串常量拼接的原理是编译期优化

可以使用`intern`方法,主动将串池中的还没有的字符串对象放入串池

```java
public class Demo2 {
    public static void main(String[] args) {
        String s = new String("a") + new String("b");

        // 这是1.8的 讲字符串对象尝试放入到串池中,如果有不会放入,如果没有则会放入串池,会把串池中的对象返回
      	// 1.8和1.6 不一样
        String intern = s.intern();
        // true
        System.out.println(intern=="ab");
        // true
        System.out.println(s=="ab");
    }
}
```

##### 常量池和串池的区别

```java
// StringTable["a","b","ab"]
public class Demo {
    // 反编译之后,常量池中的信息,都会被加载到运行时常量池中,这时都是常量池中的符号,还没有变成Java字符串对象
    // 运行到s1这行代码的时候,就会把a符号变成a字符串对象,存入到stringtable[]中
    // 用到才会创建字符串对象放到串池中,也就是懒加载
    public static void main(String[] args) {

        String s1 = "a"; // 懒加载
        String s2 = "b";
        String s3 = "ab";

        // 字符串相加的原理:new StringBuilder().append(s1).append(s2).toString()
        // 上面s3的位置是在串池中,s4 是在堆内存中
        String s4 = s1 + s2;
        // false
        System.out.println(s3 == s4);

        // javac在编译期间就确定为ab了,运行的时候直接去串池中查找
        String s5 = "a" + "b";
        // true
        System.out.println(s3 == s5);
    }
}
```

证明字符串加载的延迟特性

![image-20230823214436317](https://image.runtimes.cc/202404051506157.png)

##### StringTable的位置

位置的移动,在1.6的时候要使用full gc才能被回收,1.8移动到堆内存了,minco gc 就可以回收

##### StringTable的调优

1.调整-XX:StringTableSize=桶的个数

2.考虑将字符串对象是否入池

比如有很多人的地址,都包含北京市这样的字符串,就应该考虑入池

## 直接内存(Direct Memory)

常见于NIO(`ByteBuffer`)操作时,用户数据缓冲区

分配回收成本较高,读写性能高

不受JVM内存回收管理

![image-20230831150048574](https://image.runtimes.cc/202404051508986.png)

### 演示直接内存溢出

```java
public class Demo {

    static int _100MB = 1024 * 1024 * 100;
    public static void main(String[] args) {

        List<ByteBuffer> objects = new ArrayList<>();

        int i = 0;

        try {
            while (true) {
                ByteBuffer allocate = ByteBuffer.allocateDirect(_100MB);
                objects.add(allocate);
                i++;
            }
        } finally {
            System.out.println(i);
        }

    }
}


Exception in thread "main" java.lang.OutOfMemoryError: Direct buffer memory
	at java.base/java.nio.Bits.reserveMemory(Bits.java:175)
	at java.base/java.nio.DirectByteBuffer.<init>(DirectByteBuffer.java:118)
	at java.base/java.nio.ByteBuffer.allocateDirect(ByteBuffer.java:317)
	at 直接内存.Demo.main(Demo.java:18)
```

### 释放原理

使用UnSafe对象完成了直接内存的分配回收,而且回收需要主动调用freeMemory方法

ByteBuffer的实现类内部,使用了Cleaner(虚引用)来检测ByteBuffer对象,一旦ByteBuffer对象被垃圾回收,就会由ReferenceHandler线程通过Cleaner方法调用freeMemory来释放直接内存

```java
public class Demo2 {
    static int _1GB = 1024 * 1024 * 1024;

    public static void main(String[] args) throws IOException {
        ByteBuffer byteBuffer = ByteBuffer.allocateDirect(_1GB);
        System.out.println("分配完毕"); // 看任务管理器,可以看到这个程序占用1G+
        System.in.read();

        System.out.println("开始释放");
        byteBuffer = null;
        System.gc();  // 可以看到被会回收了,为什么会被回收呢
        System.in.read();
    }
}

// 释放原理
public class Demo3 {
    static int _1GB = 1024 * 1024 * 1024;

    public static void main(String[] args) throws IOException, NoSuchFieldException, IllegalAccessException {
        Unsafe unsafe = getUnsafe();
        // 分配内存
        // base 表示内存地址
        long base = unsafe.allocateMemory(_1GB);
        unsafe.setMemory(base,_1GB,(byte)0);
        System.in.read();

        // 释放内存
        unsafe.freeMemory(base);
        System.in.read();
    }

    public static Unsafe getUnsafe() throws NoSuchFieldException, IllegalAccessException {

        Field declaredField = Unsafe.class.getDeclaredField("theUnsafe");
        declaredField.setAccessible(true);
        return (Unsafe) declaredField.get(null);
    }
}

// 源码
ByteBuffer.allocateDirect(_1GB);
public static ByteBuffer allocateDirect(int capacity) {
    return new DirectByteBuffer(capacity);
}

DirectByteBuffer(int cap) {                   // package-private
    super(-1, 0, cap, cap);
    boolean pa = VM.isDirectMemoryPageAligned();
    int ps = Bits.pageSize();
    long size = Math.max(1L, (long)cap + (pa ? ps : 0));
    Bits.reserveMemory(size, cap);

    long base = 0;
    try {
      	// 分配内存
        base = UNSAFE.allocateMemory(size);
    } catch (OutOfMemoryError x) {
        Bits.unreserveMemory(size, cap);
        throw x;
    }
    UNSAFE.setMemory(base, size, (byte) 0);
    if (pa && (base % ps != 0)) {
        // Round up to page boundary
        address = base + ps - (base & (ps - 1));
    } else {
        address = base;
    }
  
  	// 当this被回收时,就会调用回调函数Deallocator
  	// 就是Java对象被回收，触发直接内存回收
    cleaner = Cleaner.create(this, new Deallocator(base, size, cap));
    att = null;
}

// 继承了虚应用对象
public class Cleaner extends PhantomReference<Object>{
      public void clean() {
        if (!remove(this))
            return;
        try {
            thunk.run();
        } catch (final Throwable x) {
            AccessController.doPrivileged(new PrivilegedAction<>() {
                    public Void run() {
                        if (System.err != null)
                            new Error("Cleaner terminated abnormally", x)
                                .printStackTrace();
                        System.exit(1);
                        return null;
                    }});
        }
    }
}

// 回调函数
private static class Deallocator
    implements Runnable
{

    private long address;
    private long size;
    private int capacity;

    private Deallocator(long address, long size, int capacity) {
        assert (address != 0);
        this.address = address;
        this.size = size;
        this.capacity = capacity;
    }

    public void run() {
        if (address == 0) {
            // Paranoia
            return;
        }
      	// 释放直接内存
        UNSAFE.freeMemory(address);
        address = 0;
        Bits.unreserveMemory(size, capacity);
    }

}
```







## 垃圾回收机制

### 可达性分析法

如何判断对象是否可以回收

1. 引用计数法
2. 可达性分析法,使用[Eclipse Memory Analyzer](https://projects.eclipse.org/projects/tools.mat) ,沿着GC Root查找,找到就不能回收

Eclipse Memory Analyzer的使用方法

```java
/**
 * 使用mat 演示GC root
 */
public class Demo {
    public static void main(String[] args) throws IOException {

        ArrayList<Object> list1 = new ArrayList<>();
        list1.add("a");
        list1.add("b");
        System.out.println(1);
        // jsp 获取到进程id
        // jmap -dump:format=b,live,file=1.bin 进程id

        System.in.read();

        list1 = null;
        System.out.println(2);
        System.in.read();
        System.out.println("end....");
    }
}
```

live 代表的意思是,执行dump文件的时候,先执行一次GC

![image-20230824141103802](https://image.runtimes.cc/202404051508558.png)

![image-20230824141147149](https://image.runtimes.cc/202404051508709.png)

![image-20230824141218556](https://image.runtimes.cc/202404051509973.png)



大概有四类:

System Class

Native Stack,调用native方法要使用到的

Thread,活动线程,使用到的对象

Busy Monitor,各种锁



### 四种引用

![image-20230824141552451](https://image.runtimes.cc/202404051509306.png)



强引用

|            |                                                              |                                |
| ---------- | ------------------------------------------------------------ | ------------------------------ |
| 强引用     |                                                              |                                |
| 软引用     | 有软引用引用该对象,只有在内存不足时才会被垃圾回收            | 可以配合引用队列来释放引用自身 |
| 弱引用     | 在GC时,不管内存是否充足都会被回收                            | 可以配合引用队列来释放引用自身 |
| 需引用     | 主要分配ByteBuffer使用,被引用对象回收时,被引用对象回收时,会将虚引用入队,由Reference Handler线程调用虚引用相关方法释放直接内存 | 必须配合引用队列               |
| 终结器引用 | 无需手动编码,GC时,终结器引用入队,再由Finalizer线程通过终结器引用并调用finalize方法,第二次GC时,才能被回收 |                                |

### 垃圾回收算法

* 标记清除
* 标记整理
* 复制



#### 标记清除

原本的内存使用情况

![image-20230824143552505](https://image.runtimes.cc/202404051509603.png)

标记

![image-20230824143616710](https://image.runtimes.cc/202404051512492.png)

把要清楚的内存的开始地址放在列表你,等下次要分配内存的时候,直接在列表里找

优点:就是速度快

缺点:产生内存碎片

#### 标记整理

标记

![image-20230824143845073](https://image.runtimes.cc/202404051512395.png)

整理

![image-20230824143905620](https://image.runtimes.cc/202404051512099.png)

优点:不会产生碎片

缺点:速度慢

#### 复制算法

原来的内存使用

![image-20230824144146317](https://image.runtimes.cc/202404051514508.png)

标记

![image-20230824144219705](https://image.runtimes.cc/202404051514387.png)

复制

![image-20230824144234895](https://image.runtimes.cc/202404051514593.png)

清理

![image-20230824144247178](https://image.runtimes.cc/202404051515251.png)

交换from 和 to的位置

![image-20230824144305405](https://image.runtimes.cc/202404051515286.png)

### 分代回收

```shell
新生代: Minor GC
	伊甸园
	幸存区 from
	幸存区 to
老年代: Full GC 也会清理新生代
```

- 对象首先分配在伊甸园区域
- ﻿新生代空间不足时，触发 minor ge，伊甸园和trom 存活的对象使用copy 复制到10中，存活的对象年龄加1并且交换 from to

- ﻿﻿minor se 会引1发 stop the wvorid，暂停其它用户的线程，等垃圾回收结束，用户线程才恢复运行
- ﻿当对象寿命超过國值时，会晋升至老年代，最大寿命是15 (4bit)
- ﻿当老年代空间不足，会先尝试触发 mninor ge，如果之后空间仍不足，那么触发 foll ge, sTw的时间更长



| 含义               | 参数                                                         |      |
| ------------------ | ------------------------------------------------------------ | ---- |
| 堆初始大小         | Xms                                                          |      |
| 堆最大大小         | xmx 或 -Xx:MaxFeapSize=size                                  |      |
| 新生代大小         | Xmn 或 （-XX:NewSize=size + -XX:MaxNewSize=size)             |      |
| 幸存区比例（动态） | Xx:InitialSurvivorRatio=ratio 和-XX:+UseAdaptiveSizePolicy   |      |
| 幸存区比例         | XX:SurvivorRatio=ratio                                       |      |
| 晋升阈值           | XX:MaxTenuringThreshold=threshold (threshold和具体的回收器有关) |      |
| 晋升详情           | XX: +PrintTenuringDistribution                               |      |
| GC详情             | XX:+PrintGCDetails - verbose:go                              |      |
| FullGC 前 MinorGC  | XX:+ScavengeBeforeFullGC                                     |      |



# Servlet

## 搭建环境(XML版本)

下载,解压tomcat,运行

```bash
# 进入目录
cd apache-tomcat-10.0.27/bin
# 运行
sh startup.sh
# 报错信息
The file is absent or does not have execute permission
This file is needed to run this program

# 授权
chmod 777 apache-tomcat-10.0.27
# 再次运行
sh startup.sh
# 打印结果
Using CATALINA_BASE:   /Users/anthony/Downloads/apache-tomcat-10.0.27
Using CATALINA_HOME:   /Users/anthony/Downloads/apache-tomcat-10.0.27
Using CATALINA_TMPDIR: /Users/anthony/Downloads/apache-tomcat-10.0.27/temp
Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk-18.0.1.jdk/Contents/Home
Using CLASSPATH:       /Users/anthony/Downloads/apache-tomcat-10.0.27/bin/bootstrap.jar:/Users/anthony/Downloads/apache-tomcat-10.0.27/bin/tomcat-juli.jar
Using CATALINA_OPTS:
Tomcat started.

# 访问
http://localhost:8080
```

文件目录结构
![](https://image.runtimes.cc/202404051515409.png)

导包

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.example</groupId>
    <artifactId>servlet-xml</artifactId>

		<!-- 打war包 -->
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>

    <name>servlet-xml Maven Webapp</name>

    <dependencies>
     <dependency>
            <groupId>org.apache.tomcat</groupId>
            <artifactId>tomcat-servlet-api</artifactId>
            <version>10.0.4</version>
        </dependency>
    </dependencies>
    <build>
        <finalName>servlet-xml</finalName>
    </build>
</project>
```

因为是使用的tomcat10,所有导入的包不一样,如果是tomcat10之前的版本,导入的包好像是`java-serverlt-api`

参考:[https://taurusxin.com/tomcat10-issue/](https://taurusxin.com/tomcat10-issue/)

代码

HelloWorld.java

```java
package com.mmzcg.servlet;

// 导入必需的 java 库

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;

// 扩展 HttpServlet 类
public class HelloWorld extends HttpServlet {

    private String message;

    public void init() throws ServletException {
        // 执行必需的初始化
        message = "执行必需的初始化";
    }

    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException {
        // 设置响应内容类型
        response.setContentType("text/html");

        // 实际的逻辑是在这里
        PrintWriter out = response.getWriter();
        out.println("<h1>" + message + "</h1>");
    }

    public void destroy() {
        // 什么也不做
        System.out.printf("我就没见过销毁过");
    }
}
```

Version.java

```java
package com.mmzcg.servlet;

// 导入必需的 java 库

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;

// 扩展 HttpServlet 类
public class Version extends HttpServlet {

    private String message;

    public void init() throws ServletException {
        // 执行必需的初始化
        message = "执行必需的初始化";
    }

    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException {
        // 设置响应内容类型
        response.setContentType("text/html");

        // 实际的逻辑是在这里
        PrintWriter out = response.getWriter();
        out.println("<h1>" + "返回的是版本号" + "</h1>");
    }

    public void destroy() {
        // 什么也不做
        System.out.printf("我就没见过销毁过");
    }
}
```

index.jsp

```java
// 解决中文乱码
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<html>
<body>
<h2>这是首页</h2>

有两个url

<a href="/hello">hello</a>
<a href="/version">version</a>

</body>
</html>
```

web.xml

```java
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>
  <servlet>
    <servlet-name>HelloWorld</servlet-name>
    <servlet-class>com.mmzcg.servlet.HelloWorld</servlet-class>
  </servlet>

  <servlet>
    <servlet-name>Version</servlet-name>
    <servlet-class>com.mmzcg.servlet.Version</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>HelloWorld</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>

  <servlet-mapping>
    <servlet-name>Version</servlet-name>
    <url-pattern>/version</url-pattern>
  </servlet-mapping>
</web-app>
```

```bash
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.example</groupId>
    <artifactId>servlet-xml</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>

    <name>servlet-xml Maven Webapp</name>

    <dependencies>
       <dependency>
            <groupId>org.apache.tomcat</groupId>
            <artifactId>tomcat-servlet-api</artifactId>
            <version>10.0.4</version>
        </dependency>
    </dependencies>

    <build>
        <finalName>servlet-xml</finalName>
    </build>
</project>
```

运行环境一:在IDEA上运行tomcat

添加tomcat模板

![](https://image.runtimes.cc/202404051515728.png)

配置tomcat_home

![](https://image.runtimes.cc/202404051515905.png)

配置包和路径

![](https://image.runtimes.cc/202404051515430.png)

运行环境一:在命令行运行tomcat

先停掉tomcat,复制war包到webapps目录里

![](https://image.runtimes.cc/202404051515813.png)

再启动tomcat,默认的访问地址就是:`http://localhost:8080/servlet-xml/`

## 搭建环境(注解版本)

前面的都不变,只是改下代码

web.xml

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>
</web-app>
```

index.jsp

```html
<html>
<body>
<h2>Hello World!</h2>
</body>
</html>
```

运行环境一:在命令行运行tomcat

![](https://image.runtimes.cc/202404051515268.png)

HelloWorld.java

```java
package com.mmzcg.annotation;

// 导入必需的 java 库

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;

// 扩展 HttpServlet 类
@WebServlet("/hello")
public class HelloWorld extends HttpServlet {

    private String message;

    public void init() throws ServletException {
        // 执行必需的初始化
        message = "注解执行必需的初始化";
    }

    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException {
        // 设置响应内容类型
        response.setContentType("text/html");

        // 实际的逻辑是在这里
        PrintWriter out = response.getWriter();
        out.println("<h1>" + message + "</h1>");
    }

    public void destroy() {
        // 什么也不做
        System.out.printf("注解我就没见过销毁过");
    }
}
```

Version.java

```java
package com.mmzcg.annotation;

// 导入必需的 java 库

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;

// 扩展 HttpServlet 类
@WebServlet("/version")
public class Version extends HttpServlet {

    private String message;

    public void init() throws ServletException {
        // 执行必需的初始化
        message = "执行必需的初始化";
    }

    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException {
        // 设置响应内容类型
        response.setContentType("text/html");

        // 实际的逻辑是在这里
        PrintWriter out = response.getWriter();
        out.println("<h1>" + "注解返回的是版本号" + "</h1>");
    }

    public void destroy() {
        // 什么也不做
        System.out.printf("我就没见过销毁过");
    }
}
```

## 中文乱码

### Tomcat配置文件

apache-tomcat-8.5.69\conf\server.xml 添加URIEncoding="UTF-8" 属性

```java
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" 
               URIEncoding="UTF-8"/>
```

### Idea指定编码

![](https://image.runtimes.cc/202404051515788.png)

### Jsp指定编码

在jsp的html最先添加

```java
<%@ **page** language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
```

### httpResponse指定编码

```java
// 备注：该方法只对POST方式提交的数据有效，对GET方式提交的数据无效!
request.setCharacterEncoding("UTF-8");

//在响应中主动告诉浏览器使用UTF-8编码格式来接收数据
response.setHeader("Content-Type", "text/html;charset=UTF-8");

//可以使用封装类简写Content-Type，使用该方法则无需使用setCharacterEncoding
response.setContentType("text/html;charset=UTF-8");
```