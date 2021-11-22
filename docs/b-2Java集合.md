

## Java集合



#### 1. Set

- TreeSet:基于红黑树实现，支持有序性操作
- HashSet:基于哈希表实现，直接快速查找但不支持有序性操作。
- LinkHashSet:具有HashSet的查找效率，并且内部使用双向链表维护插入顺序



#### 2.LIst

- ArrayList:基于动态数组实现，支持随机访问
- Vector:和ArrayList类似，但它是线程安全的。
- LinkedListL:基于双向链表实现，只能随机访问，但是可以快速在链表中间插入删除链表。还可以用作栈，队列和双向队列

#### 3.Queue

- LinkedList:可以用来实现双向队列
- PriorityQueue:基于堆结构实现，可以实现优先队列



### Map

- TreeMap：红黑树实现
- HasMap:基于哈希表实现，底层数据结构是数据+链表/红黑树实现
- HashTable:和HashMap类似，但是他是线程安全。
- LinkedHashMap：继承自LinkedHashMap，使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序。



### 说说List、SET、Map的区别

List:储存的顺序是有序的，可重复的，每次扩容为1.5倍

Set:储存的数据的唯一的。无序的，每次扩容为2倍

Map:通过就键值对存储数据，key是无序不可以重复的。



#### ArrayList和LinkedList的区别？

1：都不能保证线程安全

2：ArrayList底层用数组实现，支持随机访问，每次增加修改都需要把后面的元素进行移动，时间复杂度o(n-i)。而LinkedList使用双向链表来实现，不支持随机访问，直接用add()方法插入时时间复杂度o(1),指定位置插入时，O（N）

3：ArrayList因为list列表的结尾会预留一定的容量空间吗，会造成空间浪费。而LinkedList存储每个元素都需要比ArrayList消耗更多的空间，因为要存放直接前驱，直接后继和数据



#### HashMap和HashTable的区别？

1. **线程是否安全:** HashMap 是非线程安全的， HashTable 是线程安全的,因为 HashTable 内 部的方法基本都经过 synchronized 修饰。

2. **效率:** 因为线程安全的问题， HashMap 要比 HashTable 效率高一点。

3. **对** **Null key** **和** **Null value** **的支持:** HashMap 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个;HashTable 不允许有 null 键和 null 值，否则会抛出 NullPointerException 。

4. **初始容量大小和每次扩充容量大小的不同 :** 1 创建时，如果不指定容量，HashTable默认初始化为11的大小，每次扩容为2n+1。HashMap初始为16，每次扩容为原来的两倍。2 给定了初始容量，HashTable会直接使用给定的大小，而HashMap会扩容为2的幂次方倍。
5. **底层数据结构:** 当链表⻓度大于阈值(默认为 8)(将链表转换成红黑树前会判断，如果当前数组的⻓度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树)时，将链表转化为红黑树，以减少搜索时间。Hashtable 没有这样的机制。

#### **ConcurrentHashMap**

##### 1.7之前使用分段锁来实现

首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问。

**ConcurrentHashMap** **是由** **Segment** **数组结构和** **HashEntry** **数组结构组成**。

Segment 实现了 ReentrantLock ,所以 Segment 是一种可重入锁，扮演锁的⻆色。 HashEntry 用 于存储键值对数据。

​     

#####  1.8取消了 Segment 分段锁，采用 CAS 和 synchronized 来保证并发安全

synchronized 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并 发，效率又提升 N 倍。

首先使用无锁操作CAS插入头结点，失败则循环重试

若头结点已存在，则尝试哦去胡头结点的同步锁，再进行操作 

