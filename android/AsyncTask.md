#####AsyncTask封装了Thread和Handler，是一种轻量级的异步任务类。
#####参数
~~~java
class MyAsyncTask extends AsyncTask<Params, Progress, Result>
~~~
**Params**：执行execute时需要传入的参数，可以是Void。
**Progress**：更新进度时用到的类型。
**Result**：任务执行完成后返回的结果类型。
#####核心方法
**doInBackground**：异步执行后台线程需要完成的任务。
**onPreExecute**：执行后台耗时操作前被调用，通常进行初始化操作。
**onPostExecute**：doInBackground执行完成后调用的方法。
**onProgressUpdate**：doInBackground中调用publishProgress会执行该方法，用于更新进度。
**onCancelled**：异步任务被取消时执行该方法。
######doInBackground在线程池中执行，其余方法均在主线程中执行，可以直接更新UI。
#####基本用法
![Demo运行图.png](http://upload-images.jianshu.io/upload_images/8408735-4b47fee493604a36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

~~~java
class MyAsyncTask extends AsyncTask<String, Integer, String> {

        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            progressBar.setMax(100);
            bt_start.setText("开始下载");
        }

        @Override
        protected String doInBackground(String... strings) {
            for (int i = 0; i < 101; i++) {
                if (isCancelled()) {
                    break;
                }
                publishProgress(i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            return null;
        }

        @Override
        protected void onProgressUpdate(Integer... values) {
            super.onProgressUpdate(values);
            if (isCancelled()) {
                return;
            }
            progressBar.setProgress(values[0]);
        }

        @Override
        protected void onPostExecute(String s) {
            super.onPostExecute(s);
            bt_start.setText("下载完成");
        }

        @Override
        protected void onCancelled() {
            super.onCancelled();
        }
    }
~~~
**启动方式**
~~~java
        myAsyncTask = new MyAsyncTask();
        myAsyncTask.execute("downLoadUrl");
~~~
从Android3.0开始，默认情况下AsyncTask是串行执行的，要想并行执行，可以尝试下面方法
~~~java
        myAsyncTask.executeOnExecutor(AsyncTask.THREAD_POOL_EXECUTOR,"downLoadUrl");
~~~
#####源码初探
~~~Java
    @MainThread
    public final AsyncTask<Params, Progress, Result> execute(Params... params) {
        return executeOnExecutor(sDefaultExecutor, params);
    }

    @MainThread
    public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,
            Params... params) {
        if (mStatus != Status.PENDING) {
            switch (mStatus) {
                case RUNNING:
                    throw new IllegalStateException("Cannot execute task:"
                            + " the task is already running.");
                case FINISHED:
                    throw new IllegalStateException("Cannot execute task:"
                            + " the task has already been executed "
                            + "(a task can be executed only once)");
            }
        }

        mStatus = Status.RUNNING;

        onPreExecute();

        mWorker.mParams = params;
        exec.execute(mFuture);

        return this;
    }
~~~
当执行execute方法时，可以看到调用了executeOnExecutor，并加入了默认的sDefaultExecutor。也可以看到，当重复执行时就会抛出异常。
~~~Java
    private static volatile Executor sDefaultExecutor = SERIAL_EXECUTOR;
~~~
Executor是一个接口，百度翻译是执行者，就是用来执行提交的任务的。
所以不管调用AsyncTask.execute(Params),
还是调用AsyncTask.executeOnExecutor(AsyncTask.SERIAL_EXECUTOR,Params),
效果是一样的，用的都是默认的Executor，即sDefaultExecutor。
~~~Java
 public static final Executor SERIAL_EXECUTOR = new SerialExecutor();

 private static class SerialExecutor implements Executor {
        final ArrayDeque<Runnable> mTasks = new ArrayDeque<Runnable>();
        Runnable mActive;

        public synchronized void execute(final Runnable r) {
            mTasks.offer(new Runnable() {
                public void run() {
                    try {
                        r.run();
                    } finally {
                        scheduleNext();
                    }
                }
            });
            if (mActive == null) {
                scheduleNext();
            }
        }

        protected synchronized void scheduleNext() {
            if ((mActive = mTasks.poll()) != null) {
                THREAD_POOL_EXECUTOR.execute(mActive);
            }
        }
    }
~~~
ArrayDeque采用循环数组实现，mTasks为要执行的任务队列，从SerialExecutor可以看出，当调用AsyncTask.execute(Params)或者调用AsyncTask.executeOnExecutor(AsyncTask.SERIAL_EXECUTOR,Params),并不是立即执行任务，而是通过mTasks.offer方法向队尾插入了执行任务，只有当scheduleNext方法取到它时，才会通过THREAD_POOL_EXECUTOR执行，mTasks.poll为获取并删除队首任务，所以通过这两种方法执行的都是串行任务。

等等

THREAD_POOL_EXECUTOR在前边遇到过，是的，没错。就是在要执行并行任务的时候用到的它。
~~~Java
public static final Executor THREAD_POOL_EXECUTOR;

    static {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
                CORE_POOL_SIZE, MAXIMUM_POOL_SIZE, KEEP_ALIVE_SECONDS, TimeUnit.SECONDS,
                sPoolWorkQueue, sThreadFactory);
        threadPoolExecutor.allowCoreThreadTimeOut(true);
        THREAD_POOL_EXECUTOR = threadPoolExecutor;
    }
~~~
ThreadPoolExecutor是一个线程池，用来执行提交的任务，所以当参数设置为THREAD_POOL_EXECUTOR时，没有一系列的添加队列操作，而是在executeOnExecutor方法中直接执行，所以这种方法执行任务是并行的。
#####注意事项
**AsyncTask对象必须在主线程创建，execute方法也必须在主线程调用，且execute只能调用一次。**
#####取消任务
~~~java
        myAsyncTask.cancel(true);
~~~
调用cancel方法取消任务是否可以有效的取消当前任务？cancel掉当前任务后，此时新建AsyncTask对象并执行，发现会有短暂的停顿后才会执行新建的任务，因为cancel只是改变了任务的状态，并没有真正的停止任务，cancel掉的任务在队列中执行完成后才会执行新创建的任务。所以，要在doInBackground中进行isCancelled判断，如果为true，则直接跳出，避免资源、时间的浪费。
#####最后
有什么解释不对还望各位大神指正