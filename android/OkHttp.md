### OkHttp请求流程

**okHttpClient.newCall(request).enqueue()**

初始化Request，OkHttpClient.newCall(request)返回一个包装类RealCall

调用RealCall的enqueue(CallBack)方法，通过executed检查是否执行过，然后调用client.dispatcher.enqueue(AsyncCall(responseCallback))，传入callBack的包装类AsyncCall，AsyncCall实现了Runnable接口

~~~kotlin
  override fun enqueue(responseCallback: Callback) {
    synchronized(this) {
      check(!executed) { "Already Executed" }
      executed = true
    }
    callStart()
    client.dispatcher.enqueue(AsyncCall(responseCallback))
  }
~~~

Dispatcher核心属性

~~~kotlin
 var maxRequests = 64
var maxRequestsPerHost = 5
val executorService: ExecutorService
  /** Ready async calls in the order they'll be run. */
  private val readyAsyncCalls = ArrayDeque<AsyncCall>()
  /** Running asynchronous calls. Includes canceled calls that haven't finished yet. */
  private val runningAsyncCalls = ArrayDeque<AsyncCall>()
  /** Running synchronous calls. Includes canceled calls that haven't finished yet. */
  private val runningSyncCalls = ArrayDeque<RealCall>()
~~~

dispatcher.enqueue()方法将call添加到readyAsyncCalls中，然后执行promoteAndExecute()

~~~kotlin
  internal fun enqueue(call: AsyncCall) {
    synchronized(this) {
      readyAsyncCalls.add(call)

      // Mutate the AsyncCall so that it shares the AtomicInteger of an existing running call to
      // the same host.
      if (!call.call.forWebSocket) {
        val existingCall = findExistingCallWithHost(call.host)
        if (existingCall != null) call.reuseCallsPerHostFrom(existingCall)
      }
    }
    promoteAndExecute()
  }
~~~

创建executableCalls用于存储将要执行的任务，遍历readyAsyncCalls并通过判断执行数量限制将任务添加到executableCalls，最后遍历它并调用executeOn(executorService)执行任务

~~~kotlin
  private fun promoteAndExecute(): Boolean {
    this.assertThreadDoesntHoldLock()

    val executableCalls = mutableListOf<AsyncCall>()
    val isRunning: Boolean
    synchronized(this) {
      val i = readyAsyncCalls.iterator()
      while (i.hasNext()) {
        val asyncCall = i.next()

        if (runningAsyncCalls.size >= this.maxRequests) break // Max capacity.
        if (asyncCall.callsPerHost.get() >= this.maxRequestsPerHost) continue // Host max capacity.

        i.remove()
        asyncCall.callsPerHost.incrementAndGet()
        executableCalls.add(asyncCall)
        runningAsyncCalls.add(asyncCall)
      }
      isRunning = runningCallsCount() > 0
    }

    for (i in 0 until executableCalls.size) {
      val asyncCall = executableCalls[i]
      asyncCall.executeOn(executorService)
    }

    return isRunning
  }
~~~





















