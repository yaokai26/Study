# Study
-------
## 每天的学习总结(100天)
-------
## 一.HashMap(无序):

#### 定义：HashMap:继承map接口，实现了serializable接口(见第三点)。其实HashMap的数据是存在table数组中的,它是一个entry数组，entry是单向链表（链表是用来解决冲突的。JDK1.8中，当链表节点超过8个，会转为红黑树进行存储）
![附图1](https://github.com/yaokai26/Images/blob/master/1.png)\
它是一个Entry数组，Entry是HashMap的一个静态内部类，\
![附图2](https://github.com/yaokai26/Images/blob/master/2.png)\
Entry就封装了key和value，put方法参数key和value会被封装成Entry，然后放到这个Entry[]数组中。它内部有一个Entry类型的next，指向下一个Entry的引用，所以table存储的是Entry的单向链表。
#### 构造：initialCapacity是HashMap的初始化容量，默认是16，loadFactor为负载因子，默认为0.75（增大该值，能减少内存空间，但是会增加查询的时间开销，调小相反）,threshold为hashMap扩容的阀值，当超过该阀值，HashMap会进行扩容，阀值为HashMap的容量乘以负载因子 16*0.75=12
![附图3](https://github.com/yaokai26/Images/blob/master/3.png)\

#### put方法：这里有一个问题，为什么HashMap的容量是2的幂次方？
![附图4](https://github.com/yaokai26/Images/blob/master/4.png)\
因为在HashMap的put方法中，计算下标的indexFor方法，传入key的hashcode值和map容量，为了保证下标能够充分利用，防止出现只有奇数或者只有偶数的情况，保证map容量length为2的幂次方的情况下，length-1恒为奇数，进行&位运算,能够得到奇偶下标。\
![附图5](https://github.com/yaokai26/Images/blob/master/5.png)\
![附图6](https://github.com/yaokai26/Images/blob/master/6.png)\
这也就是为什么hashMap的容量为2的幂次方的原因，保证下标的充分利用
##### HashMap是如何保证数据的唯一性的：put时，会先取出table数组中下标为i的entry，判断entry的hash值和key值是否和要存储的hash值和key值相同(为什么比较了hash值还要比较key值，因为不同对象的hash值可能一样)，如果相同，表示要存储的key已经存在于hashMap中，只需要替换entry的value就行，如果不相同，就取e.next继续比较，其实就是遍历table中的entry单向链表，如果有相同的key和hash值，就替换最新的value值。所以hashMap只能存储唯一的key。
扩容就是先创建一个长度为原来的两倍的table,再遍历老table,重新计算hash值放入新table对应位置，并重新计算新hashMap阀值的过程。put方法中的createEntry方法，当hash冲突时，采用的拉链法来解决hash冲突的(HashMap其实就是一个Entry数组，Entry对象中包含了键和值，其中next也是一个Entry对象，它就是用来处理hash冲突的，形成一个链表。HashCode相同，表示table的下标相同，key不同表示产生冲突，就会产生一个entry链)，并且是把新元素是插入到单边表的表头。\
所以如果我们已经预知HashMap中元素的个数，那么预设元素的个数能够有效的提高HashMap的性能(个数大于0.75倍的map大小)。

#### get方法：HashMap的遍历，是先遍历table，然后再遍历table上每一条单向链表，时间复杂度为O(n平方),可以按照规律降低时间复杂度，根据key计算hash值，可以知道Key对应的entry所在的table的下标，这样就可以遍历单向链表，时间复杂度降低到O(n)。
Set<Entry<String,String>> set = hashMap.EntrySet();\
Iterator<Entry<String,String>> iterator = set.iterator();\
HashMap重写了entrySet,entrySet是HashMap的内部类，(Entry是静态内部类)，set.iterator会调用newEntryIterator()返回一个自定义的迭代器EntryIterator(继承HashIterator)，EntryIterator没有hasNext()方法，所以调用HashIterator的hasNext()，如果HashMap不为空，第一次调用肯定返回Entry，也就是第一条单向链表的表头。接下来调用EntryIterator.next取下一个Entry,next()方法返回nextEntry()，作用有两点：返回当前Entry,准备好下一个要返回的Entry。get的过程如下图：\
![附图7](https://github.com/yaokai26/Images/blob/master/7.png)\
HashMap的遍历，是先遍历table，再遍历table上每一条单向链表，如果当前返回的entry是链表上的最后一个元素，那它就没有next属性了，所以要寻找下一个table上有单向链表的表头，entry1-entry2-entry3-entry6-entry-7-entry5,所以hashMap是无序的。

### 总结
* HashMap是基于哈希表实现的，用Entry[]来存储数据，而Entry中封装了key、value、hash以及Entry类型的next
* HashMap存储数据是无序的
* hash冲突是通过拉链法解决的
* HashMap的容量永远为2的幂次方，有利于哈希表的散列
* HashMap不支持存储多个相同的key，且只保存一个key为null的值，多个会覆盖
* put过程，是先通过key算出hash，然后用hash算出应该存储在table中的index，然后遍历table[index]，看是否有相同的key存在，存在，则更新value；不存在则插入到table[index]单向链表的表头，时间复杂度为O(n)
* get过程，通过key算出hash，然后用hash算出应该存储在table中的index，然后遍历table[index]，然后比对key，找到相同的key，则取出其value，时间复杂度为O(n)
* HashMap是线程不安全的，如果有线程安全需求，推荐使用ConcurrentHashMap。或者 Map map = Collections.synchronizedMap(new HashMap())\
[相关链接](https://www.jianshu.com/p/dde9b12343c1)

## 二.LinkedHashMap(有序)

#### 定义：LinkedHashMap就是hashMap+双向链表
#### LinkedHashMap存储数据是有序的，而且分为两种：插入顺序和访问顺序

在LinkedHashMap中，只有accessOrder为true，即是访问顺序模式，才会put时对更新的Entry进行重新排序，而如果是插入顺序模式时，不会重新排序，这里的排序跟在HashMap中存储没有关系，只是指在双向链表中的顺序。accessOrder为true，表示LinkedHashMap为访问顺序，当对已存在LinkedHashMap中的Entry进行get和put操作时，会把Entry移动到双向链表的表尾（其实是先删除，再插入)\
[相关链接](https://www.jianshu.com/p/8f4f58b4b8ab)

### 总结
* LinkedHashMap是继承于HashMap，是基于HashMap和双向链表来实现的。
* HashMap无序；LinkedHashMap有序，可分为插入顺序和访问顺序两种。如果是访问顺序，那put和get操作已存在的Entry时，都会把Entry移动到双向链表的表尾(其实是先删除再插入)。
* LinkedHashMap存取数据，还是跟HashMap一样使用的Entry[]的方式，双向链表只是为了保证顺序。
* LinkedHashMap是线程不安全的。

## 三.HashTable
    1.HashTable继承Dictionary类,HashMap继承AbstractMap类;
    2.HashTable是线程安全的，也就是HashTable中方法是synchronized的，HashMap要实现线程安全，
    需要使用concurrentHashMap或者Collections.synchronizedMap(Map m = Collections.synchronizedMap(new HashMap(...)));
    3.HashTable包含contains方法，HashMap不包含,改成了containsValue和containsKey；
    4.HashMap允许null键值,HashTable不允许；
    5.HashTable和HashMap都可以使用Iterator迭代器,HashTable还可以使用枚举enumeration
    6.HashMap和HashTable初始容量都是16,负载因子是0.75,但是扩容HashMap是扩大成旧表的2倍,HashTable是扩大成旧表的2倍+1；
    7.HashTable直接使用key的hashCode作为Hash值,和数组长度进行求余运算,得到键值对在数组中的位置,
    然后再使用equals形成链表，HashMap使用key的hashCode进行高低16位^异或运算作为hash值,和数组长度-1
    进行&运算,得到键值对在数组中的位置，再使用equals形成链表
    
## 四.ConcurrentHashMap
采用segment分段锁技术,每一个segment都相当于一个小的hashTable并且有自己的锁，只要不修改同一个段上就不会引起并发问题。Segment是一种可重入锁(继承ReentrantLock，见下面第六点)。ConcurrentHashMap是segment数组,Segment内部是数组+链表 组成的。

## 五.Serializable接口

含义及作用：一些对象有对应的一些属性，把这个对象保存在硬盘上叫做"持久化"。对象默认序列化的机制写入的内容是：对象的类，类的签名，非静态和非瞬态的字段的值(静态的东西存放在方法区内)。\
序列化能够把存放在堆内存中的对象的生命周期延长，做持久化操作，当下次需要使用这个对象时，就不需要new，直接从硬盘中读取就可以了，存储到硬盘是一个文件(扩展名是.object)。\
一些数据库连接对象,存储特定数据的对象，可以序列化，一些web项目的对象也可以序列化，防止服务崩掉，会话消失。\
注意：SerializableID号是根据类的特征和类的签名算出来的，每一个类都有一个id去区分，如果未给这个类声明SerializableID，则序列化运行时将基于该类的各个方面计算该类的默认serialVersionUID值，但是根据编译器的不同，可能反序列化的过程会产生InvalidClassException。\
案例：[Person.java](https://www.cnblogs.com/DreamDrive/p/4005966.html)

## 六.Synchronized和CAS机制

* Synchronized(悲观锁：认为程序中并发情况严重，所以严防死守):Synchronized关键字会让没有得到锁资源的线程强制进入blocked状态，争夺到锁资源后重新进入runnable状态，这个过程涉及到操作系统用户模式和内核模式的转换，代价比较高。尽管java1.6为Synchronzied做了优化，增加了从偏向锁到轻量级锁再到重量级锁的过度，但是在最终转变为重量级锁之后，性能仍然较低。
* ReentrantLock 可重入锁,通常两类：公平性和非公平性。

      公平性:根据请求锁的顺序依次获取锁,A获取锁，内部计数器+1,A访问期间,B、C发现A线程持有资源,
      因此在后面生成节点排队,假如A再次请求资源时,不需要再次排队，可以直接获取当前资源,内部计数器+1。
      非公平性：A线程释放锁之后,B.C线程会竞争,竞争到的线程获得资源，没有竞争到的继续睡眠。
   
* CAS(Compare And Swap比较并替换，乐观锁：认为程序中的并发情况不那么严重，所以让线程不断去尝试更新)：CAS的实质为3个基本操作数：内存地址V，旧的预期值A，要修改的新值B。更新一个变量的时候，只有当内存中变量的值V和旧的预期值A相同时，才会将内存地址中的值V更新为B。

CAS的缺点：
* 1.CPU开销较大
在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，循环往复，会给CPU带来很大的压力。
* 2.不能保证代码块的原子性
CAS机制所保证的只是一个变量的原子性操作，而不能保证整个代码块的原子性。比如需要保证3个变量共同进行原子性的更新，就不得不使用Synchronized了。
* 3.ABA问题
这是CAS机制最大的问题所在。

1. [漫画：什么是CAS机制?](https://mp.weixin.qq.com/s?__biz=MzIxNjA5MTM2MA==&mid=2652434378&idx=1&sn=f098c3b949acccdf6768302219f92b94&chksm=8c621045bb159953c69cda6af16f1e3fb5ec44421a50260cb079a93e783cf4d4fb3449fa69ba&scene=21#wechat_redirect)
2. [漫画：什么是CAS机制?(进阶篇)](https://mp.weixin.qq.com/s?__biz=MzIxNjA5MTM2MA==&mid=2652434390&idx=1&sn=897ab32eee826a014a95ead809d9c7e7&chksm=8c621059bb15994f469030d1b788c5e5d1e40ba94116e35f224b8864d30d1d771affa6655ec3&mpshare=1&scene=23&srcid=0116W6BjfVBeJqR0D2WtVf1z#rd)

### CAS的自旋与比较
![附图8](https://github.com/yaokai26/Images/blob/master/8.png)\
这段代码是一个无限循环，也就是CAS自旋，做了三件事：\
1、获取当前值\
2、当前值+1，计算目标值\
3、进行CAS操作，如果成功则跳出循环，如果失败继续重复上述操作\
![附图9](https://github.com/yaokai26/Images/blob/master/9.png)\
这里涉及到两个重要的对象，一个是unsafe，一个是valueOffset。unsafe为我们提供了硬件级别的原子操作。至于valueOffset对象，是unsafe.objectFieldOffset方法得到，所代表的是AtomicInteger对象value成员变量在内存中的偏移量。我们可以简单地把valueOffset理解为value变量的内存地址。而unsafe的compareAndSwapInt方法参数包括了这三个基本元素：valueOffset参数代表了V，expect参数代表了A，update参数代表了B。

* 问：java语言CAS底层如何实现的？\
答：利用unsafe提供原子性操作。
* 问：什么是ABA问题，怎么解决？\
答：见下一点。

#### ABA问题
1.含义\
两个线程执行了同样的a-》b操作，只应该有一个成功，另一个失败，但是一个线程阻塞了，又有线程3做了b-》a的操作，结果又变成了a， 线程2又成功了，本来应该执行一个a-》b结果在这里2次。\
2.解决方案\
利用版本号比较可以有效解决ABA问题。在Java当中，AtomicStampedReference类就实现了用版本号做比较的CAS机制。

#### CAS的缺点
1.ABA问题\
2.循环开销大\
3.只能保证一个共享变量的原子性操作

### 补充知识点 
并发编程中的三个概念：[具体参见](https://www.cnblogs.com/dolphin0520/p/3920373.html)
* 原子性：即一个操作或者多个操作 要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行。(只有简单的读取、赋值才是原子操作,而且必须是将数字赋值给某个变量，变量之间的相互赋值不是原子操作。但是好像在最新的JDK中，JVM已经保证对64位数据的读取和赋值也是原子性操作了。)
* 可见性：可见性是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。当一个共享变量被volatile修饰时，它会保证修改的值会立即被更新到主存，当有其他线程需要读取时，它会去内存中读取新值。
* 有序性：即程序执行的顺序按照代码的先后顺序执行。在Java内存模型中，允许编译器和处理器对指令进行重排序，但是重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。volatile关键字禁止进行指令重排序。

### volatile关键字的两层语义
一旦一个共享变量（类的成员变量、类的静态成员变量）被volatile修饰之后，那么就具备了两层语义：
* 保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。
* 禁止进行指令重排序。

## 七.Collection学习
集合和数组的区别：1.长度：数组的长度固定，集合可变； 2.内容：数组只能存储同一种元素，集合可以存储多种不同元素；3.存储元素的数据类型：数组可以存基本类型和引用类型，集合只能存储引用类型，不能存储int,double等基本类型，但是能存储相应的包装类Integer,Double。因为基本数据类型是在栈内存上分配空间的，栈上的数据随时会被收回。\
数组是内存连续的线性结构，元素类型相同，大小相同，优点是存取比较快，缺点是事先要知道长度，空间是有限的，插入删除数据效率低，需要大量连续内存。\
这里插入一个排序算法：插入排序，快速排序，冒泡排序等，多打多敲！\
1、插入排序的基本操作就是将一个数据插入到已经排好序的有序数据中，从而得到一个新的、个数加一的有序数据，算法适用于少量数据的排序，时间复杂度为O(n^2)。是稳定的排序方法。[参考链接](https://www.jianshu.com/p/27e14bb8539e)\
![附图10](https://github.com/yaokai26/Images/blob/master/10.png)\
2、冒泡排序的时间复杂度为O(n^2)\
![附图11](https://github.com/yaokai26/Images/blob/master/11.png)\
3、快速排序是从冒泡排序演变过来的，要比冒泡排序高效得多，因为它使用了分治法。分治法就是选择一个基准元素，把比这个元素小的元素放在这个元素的左边，比这个元素大的放在右边，从而把数组分成了两个部分。快速排序的平均时间复杂度为O(log2n)，最坏情况时间复杂度为O(n^2)。\
[漫画：什么是快速排序](http://www.sohu.com/a/246785807_684445)

### Collection有两个子接口List和Set
### 1.List（有序：存储顺序和取出顺序一致）
List集合有ArrayList,LinkedList,Vector
#### (1)ArrayList
API详见源码。其中remove、fastmove等移除元素的方法涉及到gc的回收。[链接](https://www.cnblogs.com/skywang12345/p/3308556.html)\
System.arraycopy(Object src, int srcPos, Object dest, int destPos, int length);\
Object src : 原数组 ; int srcPos : 从元数据的起始位置开始;Object dest : 目标数组;int destPos : 目标数组的开始起始位置;int length  : 要copy的数组的长度;\
ArrayList的几种遍历方法：1、随机访问，根据索引遍历;2、iterator迭代器;3、for(:)。其中随机访问效率最高，而使用迭代器的效率最低！
查询快，增删慢：基于数组实现，查询遍历根据索引，所以查询快。增删数据时，需要将原来的数据复制到一个新的数组，然后将元素删除插入(删除)，所以比较慢。涉及到操作元素位置之后的元素位置变动。\
当ArrayList存储自定义对象的时候,进行自然排序，需要在自定义对象中实现Comparable接口(重写compareTo方法)，构造器排序实现Comparator。如果同时有自然排序和构造器排序，以构造器排序为主。
![附图12](https://github.com/yaokai26/Images/blob/master/12.png)

#### (2)LinkedList
查询慢，增删快：双向链表结构，遍历集合需要从头或者从尾遍历，但是增删只需要改变前后元素的引用。\
ArrayList是基于数组实现的，而LinkedList是基于链表实现的。ArrayList在存储的时候不仅仅是存储了数据，还给每一个数据存储了一个引用即数组下标，而在LinkedList中是没有下标的。因此，ArrayList可以通过数组下标迅速地找到要查询的数据，LinkedList查询需要重新遍历一次链表，在遍历中逐个查询，直到找到index为止。简单来说，ArrayList可以直接通过数组下标得到元素，而LinkedList则需要根据所给的下标从头部或尾部开始往下标的位置依次获得下一个元素或上一个元素。至于增删，由于ArrayList是以数组下标(引用)＋数据存储的。例如，在一个长度为10的数组中，在数组A[3]位置插入一个数据时，数组会先扩容至长度11，然后A[4]-A[9]的值会推一位，重新赋予数组下标，这时候添增的效率就好变得非常慢。而LinkedList中的添加，就不用考虑引用，直接在链尾添加一个数据就行了。

### 2.Set（无序：存储顺序和取出顺序不一致）
Set集合有HashSet,TreeSet,LinkedHashSet

    HashSet:HashSet底层调用Map的put方法，依赖的是HashCode()和equals()方法。
    LinkedHashSet:底层数据结构由哈希表和链表组成,哈希表保证元素的唯一性，链表保证元素有序（存储和取出顺序是一致的）。
    TreeSet:底层是二叉树。并且是红黑树。红黑树是一种自平衡二叉树。TreeSet：能够对元素按照某种规则进行排序。 
      1、一种叫做自然排序。根据元素的自然顺序对元素进行排序 （自然排序是Comparable接口）
      2、根据创建set时提供的Comparator进行排序。具体取决于使用的构造方法 （比较器排序是Comparator接口）
![附图13](https://github.com/yaokai26/Images/blob/master/13.png)     
![附图14](https://github.com/yaokai26/Images/blob/master/14.png)
