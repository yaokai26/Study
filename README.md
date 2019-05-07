# Study
-------
## 每天的学习总结(100天)
-------
## 1.HashMap(无序):
#### 定义：HashMap:继承map接口，实现了serializable接口(见第二点)。其实HashMap的数据是存在table数组中的,它是一个entry数组，entry是单向链表（链表是用来解决冲突的）
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
## 2.LinkedHashMap(有序)
#### 定义：LinkedHashMap就是hashMap+双向链表
#### LinkedHashMap存储数据是有序的，而且分为两种：插入顺序和访问顺序
在LinkedHashMap中，只有accessOrder为true，即是访问顺序模式，才会put时对更新的Entry进行重新排序，而如果是插入顺序模式时，不会重新排序，这里的排序跟在HashMap中存储没有关系，只是指在双向链表中的顺序。accessOrder为true，表示LinkedHashMap为访问顺序，当对已存在LinkedHashMap中的Entry进行get和put操作时，会把Entry移动到双向链表的表尾（其实是先删除，再插入)\
[相关链接](https://www.jianshu.com/p/8f4f58b4b8ab)
### 总结
* LinkedHashMap是继承于HashMap，是基于HashMap和双向链表来实现的。
* HashMap无序；LinkedHashMap有序，可分为插入顺序和访问顺序两种。如果是访问顺序，那put和get操作已存在的Entry时，都会把Entry移动到双向链表的表尾(其实是先删除再插入)。
* LinkedHashMap存取数据，还是跟HashMap一样使用的Entry[]的方式，双向链表只是为了保证顺序。
* LinkedHashMap是线程不安全的。
## 3.Serializable接口
含义及作用：一些对象有对应的一些属性，把这个对象保存在硬盘上叫做"持久化"。对象默认序列化的机制写入的内容是：对象的类，类的签名，非静态和非瞬态的字段的值(静态的东西存放在方法区内)。\
序列化能够把存放在堆内存中的对象的生命周期延长，做持久化操作，当下次需要使用这个对象时，就不需要new，直接从硬盘中读取就可以了，存储到硬盘是一个文件(扩展名是.object)。\
一些数据库连接对象,存储特定数据的对象，可以序列化，一些web项目的对象也可以序列化，防止服务崩掉，会话消失。\
注意：SerializableID号是根据类的特征和类的签名算出来的，每一个类都有一个id去区分，如果未给这个类声明SerializableID，则序列化运行时将基于该类的各个方面计算该类的默认serialVersionUID值，但是根据编译器的不同，可能反序列化的过程会产生InvalidClassException。\
案例：[Person.java](https://www.cnblogs.com/DreamDrive/p/4005966.html)
## Synchronized和CAS机制
* Synchronized(悲观锁：认为程序中并发情况严重，所以严防死守):Synchronized关键字会让没有得到锁资源的线程强制进入blocked状态，争夺到锁资源后重新进入runnable状态，这个过程涉及到操作系统用户模式和内核模式的转换，代价比较高。尽管java1.6为Synchronzied做了优化，增加了从片偏向锁到轻量级锁再到重量级锁的过度，但是在最终转变为重量级锁之后，性能仍然较低。
* CAS(Compare And Swap比较并替换，乐观锁：认为程序中的并发情况不那么严重，所以让线程不断去尝试更新)：CAS的实质为3个基本操作数：内存地址V，旧的预期值A，要修改的新值B。更新一个变量的时候，只有当内存中变量的值V和旧的预期值A相同时，才会将内存地址中的值V更新为B。\
[漫画：什么是CAS机制?](https://mp.weixin.qq.com/s?__biz=MzIxNjA5MTM2MA==&mid=2652434378&idx=1&sn=f098c3b949acccdf6768302219f92b94&chksm=8c621045bb159953c69cda6af16f1e3fb5ec44421a50260cb079a93e783cf4d4fb3449fa69ba&scene=21#wechat_redirect)

