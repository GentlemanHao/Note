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

### 三、LinkedList

链表是由一系列非连续的节点组成的存储结构

单向链表就是通过每个节点的指针指向下一个节点而链接起来的结构，最后一个节点的next指向null，单向循环链表和单向链表不同的是，最后一个节点的next指向head节点，形成一个环

双向链表包含两个指针，pre指向前一个节点，next指向后一个节点，第一个节点head的pre指向null，最后一个节点tail的next指向null，双向循环链表和双向链表的不同在于，第一个节点的pre指向最后一个节点，最后一个节点的next指向第一个节点，也形成一个环，**LinkedList就是基于双向链表设计的**

~~~java
public class LinkedList<E>  extends  AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
~~~

`LinkedList`是一个继承于`AbstractSequentialList`的双向循环链表，可以被当作堆栈、队列或双端队列进行操作

LinkedList实现List接口，能对它进行队列操作

LinkedList实现Deque接口，能将它当作双端队列使用

LinkedList实现Cloneable接口，即覆盖了clone函数，能克隆

LinkedList实现了Serializable接口。支持序列化

**Node**

~~~java
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
~~~

**构造函数**

~~~java
	transient int size = 0;
    transient Node<E> first; //表示链表头部
    transient Node<E> last; //表示链表尾部

    public LinkedList() {}

    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }
~~~

**add**

~~~java
    public boolean add(E e) {
        linkLast(e);
        return true;
    }

    void linkLast(E e) {
        final Node<E> l = last;  //指向链表尾部节点
        final Node<E> newNode = new Node<>(l, e, null); //以尾部节点为前驱节点创建一个新节点
        last = newNode; //将链表尾部指向新节点
        if (l == null) //如果链表为空，该节点既是头节点也是尾节点
            first = newNode;
        else //链表不为空，将该节点作为原链表尾部的后继节点
            l.next = newNode;
        size++;
        modCount++;
    }

    public void add(int index, E element) {
        checkPositionIndex(index); //检查index

        if (index == size) 
            linkLast(element); //添加在链表尾部
        else
            linkBefore(element, node(index)); //添加在链表中间
    }

    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) { //判断从前还是从后遍历
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }

    void linkBefore(E e, Node<E> succ) {
        // assert succ != null;
        final Node<E> pred = succ.prev;
        final Node<E> newNode = new Node<>(pred, e, succ);
        succ.prev = newNode; //更新插入位置后节点的前驱节点
        if (pred == null)
            first = newNode;
        else
            pred.next = newNode;
        size++;
        modCount++;
    }
~~~

**addAll**

~~~java
    public boolean addAll(Collection<? extends E> c) {
        return addAll(size, c);
    }

    public boolean addAll(int index, Collection<? extends E> c) {
        checkPositionIndex(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        if (numNew == 0)
            return false;

        Node<E> pred, succ;
        if (index == size) { //表示在尾部插入，前驱节点是last，后继节点是null
            succ = null;
            pred = last;
        } else { //表示在中间插入，调用node()得到后继节点，以及前驱节点
            succ = node(index);
            pred = succ.prev;
        }

        for (Object o : a) { //遍历插入数据
            @SuppressWarnings("unchecked") E e = (E) o;
            Node<E> newNode = new Node<>(pred, e, null);
            if (pred == null) //表示插入位置在头部
                first = newNode;
            else
                pred.next = newNode;
            pred = newNode;
        }

        if (succ == null) { //表示在尾部插入，更新last
            last = pred;
        } else { //表示在中间插入，更新前后节点把链表连接起来
            pred.next = succ;
            succ.prev = pred;
        }

        size += numNew;
        modCount++;
        return true;
    }
~~~

### 四、HashMap

数组具有遍历快，增删慢的特点。数组在堆中是一块连续的存储空间，遍历是数组的首地址是知道的，所以遍历快（时间复杂度为O(1)），增删慢是因为，当在中间插入或删除元素时，会造成该元素后面所有元素地址的改变，所以增删慢（时间复杂度O(n)）

链表有增删快，遍历慢的特点。链表中各元素的内存空间是不连续的，一个节点包含节点数据和后继节点的引用，所以在插入和删除时，只需修改该位置的前驱节点和后继节点，链表在插入删除时的时间复杂度为O(1)，但是在遍历是，get(n)元素时，需要从第一个开始，依次拿到后边元素的地址，进行遍历，直到遍历到第n个元素（时间复杂度为O(n)）,效率极低

Hash表是一个数组+链表的结构，这种结构能够保证在遍历与增删的过程中，如果不产生hash碰撞，仅需一次定位就可完成，时间复杂度O(1)，在JDK1.7中，只是单纯的数组+链表的结构，但是如果散列表中的hash碰撞过多时，会造成效率的降低，所以在JDK1.8中对这种情况进行了控制，当一个hash值上的链表长度大于8时，该节点的数据就不再以链表进行存储，而是转成了一个红黑树

**构造函数**

~~~java
 public HashMap() {
     //无参构造，初始容量16，负载因子0.75
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}

public HashMap(int initialCapacity) {
    //初始容量initialCapacity，负载因子0.75
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
}

public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +  initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY) //最大容量
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " + loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }

static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}

public HashMap(Map<? extends K, ? extends V> m) {
        this.loadFactor = DEFAULT_LOAD_FACTOR;
        putMapEntries(m, false);
}

final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
        int s = m.size();
        if (s > 0) {
            if (table == null) { // pre-size
                //根据map的size计算容量
                float ft = ((float)s / loadFactor) + 1.0F;
                int t = ((ft < (float)MAXIMUM_CAPACITY) ?
                         (int)ft : MAXIMUM_CAPACITY);
                if (t > threshold)
                    threshold = tableSizeFor(t);
            }
            else if (s > threshold) //判断是否需要扩容
                resize();
            for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) { //遍历，添加
                K key = e.getKey();
                V value = e.getValue();
                putVal(hash(key), key, value, false, evict);
            }
        }
 }
~~~

**get方法**

~~~java
public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
}

final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    //table不为空且length大于0，table索引位置的节点不为空（使用length-1和hash值进行位与运算）
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            //检查first节点的hash和key是否和入参一样
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            //如果first不是目标节点，并且first的next节点不为空则继续便利
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    //如果是红黑树节点，则调用红黑树查找目标的方法getTreeNode
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    //向下遍历链表，直到找到目标节点
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
}

final TreeNode<K,V> getTreeNode(int h, Object k) {
    //首先找到红黑树的根节点，使用跟节点调用find方法
        return ((parent != null) ? root() : this).find(h, k, null);
}

        final TreeNode<K,V> find(int h, Object k, Class<?> kc) {
            TreeNode<K,V> p = this; //将p节点赋值为调用此方法的节点，即红黑树跟节点
            do { //从p节点开始向下遍历
                int ph, dir; K pk;
                TreeNode<K,V> pl = p.left, pr = p.right, q;
                if ((ph = p.hash) > h) //如果传入的hash值小于p节点的hash值，则向p节点的左边遍历
                    p = pl;
                else if (ph < h) //如果传入的hash值大于p节点的hash值，则向p节点的右边遍历
                    p = pr;
                else if ((pk = p.key) == k || (k != null && k.equals(pk))) //如果hash值和key等于p节点的值，则p为目标节点
                    return p;
                else if (pl == null)
                    p = pr;
                else if (pr == null)
                    p = pl;
                else if ((kc != null || //kc不为空表示k实现了Comparable
                          (kc = comparableClassFor(k)) != null) &&
                         (dir = compareComparables(kc, k, pk)) != 0)
                    p = (dir < 0) ? pl : pr;
                else if ((q = pr.find(h, k, kc)) != null)
                    return q;
                else
                    p = pl;
            } while (p != null);
            return null;
        }
~~~

**put方法**

~~~java
    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

    final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0) //检查table是否为空或者length是0，如果是则调用resize进行初始化
            n = (tab = resize()).length;
        //通过hash值计算索引位置，将索引位置的头节点赋值给p，如果p为空则直接在该索引位置新增一个节点
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            //判断p节点的hash和key是否跟传入的相等，如果相等，将p节点赋值给e节点
            if (p.hash == hash &&  ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode) //判断p节点是否为TreeNote，如果是则调用红黑树的putTreeVal方法查找节点
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else { //表示普通链表节点
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) { //表示找不到目标节点，新增一个节点并插入链表尾部
                        p.next = newNode(hash, key, value, null);
                        // 判断节点数是否超过8个，如果超过则调用treeifyBin方法将链表节点转为红黑树节点
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&  ((k = e.key) == key || (key != null && key.equals(k))))
                        break; //找到目标节点，跳出循环
                    p = e; //更新下一个节点
                }
            }
            if (e != null) { //目标节点存在，使用传入的value覆盖该节点的value，返回oldValue
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold) //如果插入节点后节点数超过阀值，调用resize进行扩容
            resize();
        afterNodeInsertion(evict);
        return null;
    }

        final TreeNode<K,V> putTreeVal(HashMap<K,V> map, Node<K,V>[] tab, int h, K k, V v) {
            Class<?> kc = null;
            boolean searched = false;
            // 查找根节点，索引位置的头节点不一定为红黑树的根节点
            TreeNode<K,V> root = (parent != null) ? root() : this;
            for (TreeNode<K,V> p = root;;) { //将根节点赋值给p节点，开始进行查找
                int dir, ph; K pk;
                if ((ph = p.hash) > h) //传入hash值小于p节点，dir赋值为-1，表示向p的左边查找树
                    dir = -1;
                else if (ph < h) //传入hash值大于p节点，dir赋值为1，表示向p的右边查找树 
                    dir = 1;
                else if ((pk = p.key) == k || (k != null && k.equals(pk))) //表示p节点为目标节点，返回该及
                    return p;
                else if ((kc == null &&
                          (kc = comparableClassFor(k)) == null) ||
                         (dir = compareComparables(kc, k, pk)) == 0) {
                    if (!searched) { //第一次符合条件，从p节点的左节点和右节点分别调用find方法进行查找
                        TreeNode<K,V> q, ch;
                        searched = true;
                        if (((ch = p.left) != null &&
                             (q = ch.find(h, k, kc)) != null) ||
                            ((ch = p.right) != null &&
                             (q = ch.find(h, k, kc)) != null))
                            return q;
                    }
                    dir = tieBreakOrder(k, pk); //比较k和p节点的key的大小，用来决定向左还是向右查找
                }

                TreeNode<K,V> xp = p;
                if ((p = (dir <= 0) ? p.left : p.right) == null) {
                    Node<K,V> xpn = xp.next;
                    TreeNode<K,V> x = map.newTreeNode(h, k, v, xpn);
                    if (dir <= 0)
                        xp.left = x;
                    else
                        xp.right = x;
                    xp.next = x;
                    x.parent = x.prev = xp;
                    if (xpn != null)
                        ((TreeNode<K,V>)xpn).prev = x;
                    moveRootToFront(tab, balanceInsertion(root, x));
                    return null;
                }
            }
        }
~~~

**扩容机制**

~~~java
    final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) { //表示已经初始化过了
            if (oldCap >= MAXIMUM_CAPACITY) { //table容量超过最大值，则不再扩容
                threshold = Integer.MAX_VALUE;
                return oldTab;
            } //按旧容量和阈值的的2倍计算新容量和阈值的大小
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY; //默认容量
            //阈值为默认容量与负载因子的乘积
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) { //按计算公式计算阈值
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) { //如果旧的桶数组不为空，则遍历数组，并将键值对映射到新的桶数组中
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        // 重新映射时，需要对红黑树进行拆分
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        // 遍历链表，并将链表节点按原顺序进行分组
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        // 将分组后的链表映射到新桶中
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
~~~

**remove**

~~~java
public V remove(Object key) {
        Node<K,V> e;
        return (e = removeNode(hash(key), key, null, false, true)) == null ? null : e.value;
}

 final Node<K,V> removeNode(int hash, Object key, Object value, boolean matchValue, boolean movable) {
        Node<K,V>[] tab; Node<K,V> p; int n, index;
        if ((tab = table) != null && (n = tab.length) > 0 &&  (p = tab[index = (n - 1) & hash]) != null) {
            Node<K,V> node = null, e; K k; V v;
            if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
                node = p;
            else if ((e = p.next) != null) {
                if (p instanceof TreeNode) //定位红黑树的查找逻辑定位待删除节点
                    node = ((TreeNode<K,V>)p).getTreeNode(hash, key);
                else {
                    do { // 遍历链表，找到待删除节点
                        if (e.hash == hash &&  ((k = e.key) == key ||  (key != null && key.equals(k)))) {
                            node = e;
                            break;
                        }
                        p = e;
                    } while ((e = e.next) != null);
                }
            }
            // 删除节点，并修复链表或红黑树
            if (node != null && (!matchValue || (v = node.value) == value ||  (value != null && value.equals(v)))) {
                if (node instanceof TreeNode)
                    ((TreeNode<K,V>)node).removeTreeNode(this, tab, movable);
                else if (node == p)
                    tab[index] = node.next;
                else
                    p.next = node.next;
                ++modCount;
                --size;
                afterNodeRemoval(node);
                return node;
            }
        }
        return null;
}
~~~









