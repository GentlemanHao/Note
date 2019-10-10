### 线程池的优点

1.重用线程池中的线程，避免线程的创建和销毁所带来的性能开销。
2.控制线程池的最大并发数，避免大量的线程之间因相互抢占系统资源导致的阻塞现象。
3.能够对线程进行简单的管理，并提供定时执行以及指定间隔循环执行等功能。
![线程池](https://upload-images.jianshu.io/upload_images/8408735-2253c0b454a0bc4f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

##### ThreadPoolExecutor

线程池的真正实现，它的构造方法提供了一系列参数来配置线程池。

**corePoolSize**：核心线程数（CPU核心数+1）
**maximumPoolSize**：最大线程数（CUP核心数×2+1）
**keepAliveTime**：非核心线程闲置的超时时长（1秒）
**unit**：指定keepAliveTime的时间单位
**workQueue**：线程池中的任务队列（容量128）
**threadFactory**：线程工厂，为线程池提供创建新线程的功能。

**ThreadPoolExecutor执行规则**
1.如果线程池中的线程数量未达到核心线程数量，会直接启动一个核心线程执行任务。
2.如果线程池中的线程数量已经达到或者超过核心线程数量，那么任务会被插入到任务队列中排队等待执行。
3.如果无法插入到任务队列中，往往是由于任务队列已满，这时候如果线程数量未达到线程池规定的最大值，则立刻启动一个非核心线程来执行任务。
4.如果线程数量已经达到线程池规定的最大值，那么就拒绝执行此任务，ThreadPoolExecutor会调用RejectedExecutionHandler的rejectedExecution方法通知调用者。

##### #####线程池的分类

**FixedThreadPool**
只有核心线程、线程数量固定、没有超时机制、空闲状态也不会被回收、任务队列也没有大小限制
**CachedThreadPool**
只有核心线程、线程数量不定、有超时机制、60秒闲置线程回收、最大线程数是Integer.MAX_VALUE
**ScheduledThreadPool**
核心线程固定、非核心线程Integer.MAX_VALUE、非核心线程闲置立即被回收
**SingleThreadExecutor**
只有一个核心线程、所有任务都在同一线程中按顺序执行