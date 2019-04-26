# Study
-------
## 每天的学习总结(100天)
-------
### 1.HashMap、LinkedHashMap、HashTable等:
#### 定义：HashMap:继承map接口，实现了serializable接口(见第二点)。其实HashMap的数据是存在table数组中的
![附图1](https://github.com/yaokai26/Images/blob/master/1.png)\
它是一个Entry数组，Entry是HashMap的一个静态内部类，\
![附图2](https://github.com/yaokai26/Images/blob/master/2.png)\
Entry就封装了key和value，put方法参数key和value会被封装成Entry，然后放到这个Entry[]数组中。它内部有一个Entry类型的next，指向下一个Entry的引用，所以table存储的是Entry的单向链表。
#### 构造：initialCapacity是HashMap的初始化容量，默认是16，loadFactor为负载因子，默认为0.75,threshold为hashMap扩容的阀值，当超过该阀值，HashMap会进行扩容，阀值为HashMap的容量乘以负载因子 16*0.75=12
![附图3](https://github.com/yaokai26/Images/blob/master/3.png)\
#### put方法：这里有一个问题，为什么HashMap的容量是2的幂次方？
![附图4](https://github.com/yaokai26/Images/blob/master/4.png)\
因为在HashMap的put方法中，计算下标的indexFor方法，传入key的hashcode值和map容量，为了保证下标能够充分利用，防止出现只有奇数或者只有偶数的情况，保证map容量length为2的幂次方的情况下，length-1恒为奇数，进行&位运算,能够得到奇偶下标。\
![附图5](https://github.com/yaokai26/Images/blob/master/5.png)\
![附图6](https://github.com/yaokai26/Images/blob/master/6.png)\
这也就是为什么hashMap的容量为2的幂次方的原因，保证下标的充分利用
##### HashMap是如何保证数据的唯一性的：put时，会先取出table数组中的entry，判断entry的hash值和key值是否和要存储的hash值和key值相同(为什么比较了hash值还要比较key值，因为不同对象的hash值可能一样)，如果相同，表示要存储的key已经存在于hashMap中，只需要替换entry的value就行，如果不相同，就取e.next继续比较，其实就是遍历table中的entry单向链表，如果有相同的key和hash值，就替换最新的value值。所以hashMap只能存储唯一的key。
[相关链接](https://www.jianshu.com/p/dde9b12343c1)
### 2.Serializable接口
含义及作用：一些对象有对应的一些属性，把这个对象保存在硬盘上叫做"持久化"。对象默认序列化的机制写入的内容是：对象的类，类的签名，非静态和非瞬态的字段的值(静态的东西存放在方法区内)。\
序列化能够把存放在堆内存中的对象的生命周期延长，做持久化操作，当下次需要使用这个对象时，就不需要new，直接从硬盘中读取就可以了，存储到硬盘是一个文件(扩展名是.object)。\
一些数据库连接对象,存储特定数据的对象，可以序列化，一些web项目的对象也可以序列化，防止服务崩掉，会话消失。\
注意：SerializableID号是根据类的特征和类的签名算出来的，每一个类都有一个id去区分，如果未给这个类声明SerializableID，则序列化运行时将基于该类的各个方面计算该类的默认serialVersionUID值，但是根据编译器的不同，可能反序列化的过程会产生InvalidClassException。\
案例：[Person.java](https://www.cnblogs.com/DreamDrive/p/4005966.html)
