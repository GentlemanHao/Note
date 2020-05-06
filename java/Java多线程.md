### Synchronized

保证在**同一时刻**最多只有**一个线程**执行该段代码，以达到并发安全的效果。

JVM基于进入和退出`Monitor`对象来实现方法同步和代码块同步

方法级同步是隐式，即无需通过字节码指令来控制，它实现在方法调用和返回操作之中，JVM可以从方法常量池中的方法表结构中的`ACC_SYNCHRONIZED`访问标志区分一个方法是否是同步方法，当方法调用时，调用指令会检查方法的`ACC_SYNCHRONIZED`访问标志是否被设置，如果设置了，执行线程将先持有`monitor`，然后再执行方法，最后在方法完成时释放`monitor`

代码块同步是利用`monitorenter`和`monitorexit`两个字节码指令，它们分别位于同步代码块的开始和结束位置，当JVM执行到`monitorenter`指令时，当前线程试图获取`monitor`对象的所有权，如果未加锁或已经被当前线程持有，就把锁的计数器 +1 ，当执行`monitorexit`指令时，锁计数器 -1 ，当锁计数器为0时，该锁就被释放，如果获取`monitor`对象失败，该线程会进入阻塞状态，直到其他线程释放锁

**Synchronized的两个用法**

**对象锁：**包括**方法锁**（默认锁对象为this当前实例对象）和**同步代码块锁**（自己指定锁对象）

**类锁：**指`synchronized`修饰静态的方法或指定锁为Class对象

​	概念：Java类可能有很多对象，但只有一个Class对象

​	形式1：`synchronized`加在`static`方法上

​	形式2：`synchronized(*.class)`代码块

​	**一把锁只能同时被一个线程获取，没有拿到锁的线程必须等待；每个实例都对应有自己的一把锁，不同实例之间互不影响；无论方法是正常执行完毕或者抛出异常，都会释放锁**

性质：可重入（同一线程的外层函数获得锁之后，内层函数可以直接获取该锁），不可中断

缺陷：效率低，锁的释放情况少、试图获得锁时不能设置超时、不能中断一个正在试图获得锁的线程；不够灵活，加锁和释放的时机单一，每个锁仅有单一的条件（某个对象），可能是不够的；无法知道是否成功获取到锁。

**Synchonized的四种状态**

**无锁**

**偏向锁**：多数情况下，锁不存在竞争，总是由同一线程获得

**轻量级锁**：由偏向锁升级而来，第二个线程加入锁争用的时候

**重量级锁**：同一时间访问同一把锁，就会有线程获取锁失败，导致轻量级锁升级为重量级锁



### 线程池`ThreadPoolExecutor`

**构造参数：**

~~~java
ThreadPoolExecutor(
  int corePoolSize, //核心线程数
  int maximumPoolSize, //最大线程数
  long keepAliveTime, //空闲超时时间
  TimeUnit unit, //时间单位
  BlockingQueue<Runnable> workQueue, //工作队列
  ThreadFactory threadFactory, //线程工厂
  RejectedExecutionHandler handler //拒绝策略
)
~~~

四种拒绝策略：`DiscardOldestPolicy、DiscardPolicy、AbortPolicy、CallerRunsPolicy`

自定义拒绝策略接口：`RejectedExecutionHandler`

线程池的状态：RUNNING、SHUTDOWN、STOP、TIDYING、TERMINATED



