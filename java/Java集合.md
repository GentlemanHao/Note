### 一、集合框架主要分为两大类：`Collection`和`Map`

**Collection**

- List 有序，可重复
  - ArrayList：底层数据结构是数组，查询快，增删慢；线程不安全，效率高。
  - Vector：底层数据结构是数组，查询快，增删快；线程安全，效率低。
  - LinkedList：底层数据结构是链表，查询慢，增删快；线程不安全，效率高。

- Set无序，唯一
  - HashSet：底层数据结构是哈希表，无序，唯一。依赖`hashCode()`和`equals()`保证唯一性。
  - LinkedHashSet：底层数据结构是链表和哈希表，FIFO插入有序，唯一。
  - TreeSet：底层数据结构是红黑树，有序，唯一。

**Map**

Map接口有三个比较重要的实现类，`HashMap`、`TreeMap`、`HashTable`。

`TreeMap`是有序的，`HashMap`和`HashTable`是无序的。`HashTable`是线程安全的，效率低，`HashMap`是线程不安全的，效率高；`HashTable`的父类是`Dictionary`，`HashMap`的父类是`AbstractMap`；`HashTable`不允许`null`值，`HashMap`允许`null`值。

### 二、ArrayList

**构造函数**

```java
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
          	//初始化elementData容量
            this.elementData = new Object[initialCapacity];
        } else {
            if (initialCapacity != 0) {
                throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
            }

            this.elementData = EMPTY_ELEMENTDATA;
        }

    }

    public ArrayList() {
      	//无参构造，返回DEFAULTCAPACITY_EMPTY_ELEMENTDATA
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    public ArrayList(Collection<? extends E> c) {
      	//将入参c转化为array赋值给elementData
        this.elementData = c.toArray();
        if ((this.size = this.elementData.length) != 0) {
          	//重新将elementData拷贝为Object[]
            if (this.elementData.getClass() != Object[].class) {
                this.elementData = Arrays.copyOf(this.elementData, this.size, Object[].class);
            }
        } else {
          	//入参length为0，设置为EMPTY_ELEMENTDATA
            this.elementData = EMPTY_ELEMENTDATA;
        }

    }
```

**添加**

~~~java
    private void add(E e, Object[] elementData, int s) {
      	//当s表示实际size
        if (s == elementData.length) {
          	//触发扩容
            elementData = this.grow();
        }

        elementData[s] = e;
        this.size = s + 1;
    }

    public boolean add(E e) {
        ++this.modCount;
        this.add(e, this.elementData, this.size);
        return true;
    }
~~~

**扩容机制**

~~~java
private Object[] grow(int minCapacity) {
  	//拷贝数组并计算新的容量
		return this.elementData = Arrays.copyOf(this.elementData, this.newCapacity(minCapacity));
}

private Object[] grow() {
  	//当前需要的最小容量size+1
    return this.grow(this.size + 1);
}
~~~

**计算扩容容量**

~~~java
    private int newCapacity(int minCapacity) {
        int oldCapacity = this.elementData.length;
      	//oldCapacity >> 1，除以2的1次方，newCapacity = oldCapacity * 1.5
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity <= 0) {
          	//当使用无参构造时，出现这种情况
            if (this.elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
                return Math.max(10, minCapacity);
            } else if (minCapacity < 0) {
                throw new OutOfMemoryError();
            } else {
                return minCapacity;
            }
        } else {
          	//判断容量是否超过Int.MAX_VALUE
            return newCapacity - 2147483639 <= 0 ? newCapacity : hugeCapacity(minCapacity);
        }
    }
~~~

**addAll**

~~~java
    public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        ++this.modCount;
        int numNew = a.length;
        if (numNew == 0) {
            return false;
        } else {
            Object[] elementData;
            int s;
          	//判断剩下的容量是否容得下，是否扩容
            if (numNew > (elementData = this.elementData).length - (s = this.size)) {
                elementData = this.grow(s + numNew);
            }

            System.arraycopy(a, 0, elementData, s, numNew);
            this.size = s + numNew;
            return true;
        }
    }

    public boolean addAll(int index, Collection<? extends E> c) {
        this.rangeCheckForAdd(index);
        Object[] a = c.toArray();
        ++this.modCount;
        int numNew = a.length;
        if (numNew == 0) {
            return false;
        } else {
            Object[] elementData;
            int s;
            if (numNew > (elementData = this.elementData).length - (s = this.size)) {
                elementData = this.grow(s + numNew);
            }

            int numMoved = s - index;
            if (numMoved > 0) {
              	//向后移动index后的元素，为新插入元素留出空间
                System.arraycopy(elementData, index, elementData, index + numNew, numMoved);
            }

            System.arraycopy(a, 0, elementData, index, numNew);
            this.size = s + numNew;
            return true;
        }
    }
~~~

**删除**

~~~java
public E remove(int index) {
		Objects.checkIndex(index, this.size);
		Object[] es = this.elementData;
		E oldValue = es[index];
		this.fastRemove(es, index);
		return oldValue;
}

private void fastRemove(Object[] es, int i) {
		++this.modCount;
		int newSize;
  	//判断要删除的元素是不是最后一个
		if ((newSize = this.size - 1) > i) {
      	//将后边的元素整体前移，覆盖前边的元素
				System.arraycopy(es, i + 1, es, i, newSize - i);
		}

  	//更新size，把元素的最后一个置为null
		es[this.size = newSize] = null;
}
~~~

**修改**

~~~java
public E set(int index, E element) {
		Objects.checkIndex(index, this.size);
		E oldValue = this.elementData(index);
		this.elementData[index] = element;
		return oldValue;
}
~~~

**查询**

~~~java
public E get(int index) {
		Objects.checkIndex(index, this.size);
		return this.elementData(index);
}
~~~

