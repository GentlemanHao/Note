### ThreadLocal

ThreadLocal是线程的局部变量，是每一个线程所单独持有的。当使用ThreadLocal维护变量的时候，为每一个使用该变量的线程提供了一个独立的变量副本。

~~~java
    public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t); //获取当前线程的ThreadLocalMap
        if (map != null) {
            //根据ThreadLocal对象获取Entry
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }
~~~

~~~java
    private T setInitialValue() {
        T value = initialValue();
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
        return value;
    }

    protected T initialValue() {
        return null;
    }
~~~

~~~java
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value); //以ThreadLocal为key，保存到ThreadLocalMap
        else
            createMap(t, value); //创建ThreadLocalMap并赋值给当前线程
    }
~~~

每个线程的ThreadLocalMap是属于线程自己的，ThreadLocalMap中维护的值也是属于线程自己的，这就保证了ThreadLocal类型变量在每个线程中是独立的，在多线程环境下互不影响。

~~~java
    ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
        table = new Entry[INITIAL_CAPACITY]; //创建新的数组
        int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
        table[i] = new Entry(firstKey, firstValue);
        size = 1;
        setThreshold(INITIAL_CAPACITY); //设置阈值2/3
    }
~~~

~~~java
        private void set(ThreadLocal<?> key, Object value) {
            Entry[] tab = table;
            int len = tab.length;
            int i = key.threadLocalHashCode & (len-1); //计算要存储的索引

            for (Entry e = tab[i]; //循环判断要存放的索引位置是否已经存在Entry
                 e != null;
                 e = tab[i = nextIndex(i, len)]) {
                ThreadLocal<?> k = e.get();

                if (k == key) { //key相等，更新值
                    e.value = value;
                    return;
                }

                if (k == null) { //key为空（被回收了），表示Entry已经无效，替换该位置键值
                    replaceStaleEntry(key, value, i);
                    return;
                }
            }

            tab[i] = new Entry(key, value);
            int sz = ++size;
            if (!cleanSomeSlots(i, sz) && sz >= threshold) //清除无效的条目并判断是否超出阈值
                rehash(); //调整table的容量，重新摆放table中的Entry
        }

private void rehash() {
            expungeStaleEntries(); //清楚无效Entry

            // Use lower threshold for doubling to avoid hysteresis
            if (size >= threshold - threshold / 4) //table中条目数是否超出阈值的3/4
                resize();
        }
~~~

~~~java
private Entry getEntry(ThreadLocal<?> key) {
            int i = key.threadLocalHashCode & (table.length - 1);
            Entry e = table[i];
            if (e != null && e.get() == key)
                return e;
            else
                return getEntryAfterMiss(key, i, e);
        }


        private Entry getEntryAfterMiss(ThreadLocal<?> key, int i, Entry e) {
            Entry[] tab = table;
            int len = tab.length;

            while (e != null) {
                ThreadLocal<?> k = e.get();
                if (k == key)
                    return e;
                if (k == null)
                    expungeStaleEntry(i); //清楚无效Entry
                else
                    i = nextIndex(i, len);
                e = tab[i]; //获取下一个，继续判断
            }
            return null;
        }
~~~

ThreadLocalMap的set() get() remove()方法都有清除无效Entrty的操作，降低内存泄漏的发生，Entry的key使用了弱引用，降低内存泄漏发生的概率，由于ThreadLocalMap的生命周期和当前线程一样长，当ThreadLocal对象被回收后，由于ThreadLocalMap还持有ThreadLocal和对应value的强引用，ThreadLocal和对应的value是不会被回收的，这就导致了内存泄漏

























