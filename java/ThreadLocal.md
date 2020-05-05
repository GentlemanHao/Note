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
            map.set(this, value);
        else
            createMap(t, value);
    }
~~~

