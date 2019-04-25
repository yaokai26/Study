# Study
-------
## 每天的学习总结(100天)
-------
### 1.HashMap、LinkedHashMap、HashTable等:
HashMap:继承map接口，实现了serializable接口(见第二点)。其实HashMap的数据是存在table数组中的，\
![附图1](https://github.com/yaokai26/Images.git/1.png)\
它是一个Entry数组，Entry是HashMap的一个静态内部类，\
![附图2](https://github.com/yaokai26/Images.git/2.png)\
[相关链接](https://www.jianshu.com/p/dde9b12343c1)
### 2.Serializable接口
含义及作用：一些对象有对应的一些属性，把这个对象保存在硬盘上叫做"持久化"。对象默认序列化的机制写入的内容是：对象的类，类的签名，非静态和非瞬态的字段的值(静态的东西存放在方法区内)。\
序列化能够把存放在堆内存中的对象的生命周期延长，做持久化操作，当下次需要使用这个对象时，就不需要new，直接从硬盘中读取就可以了，存储到硬盘是一个文件(扩展名是.object)。\
一些数据库连接对象,存储特定数据的对象，可以序列化，一些web项目的对象也可以序列化，防止服务崩掉，会话消失。\
注意：SerializableID号是根据类的特征和类的签名算出来的，每一个类都有一个id去区分，如果未给这个类声明SerializableID，则序列化运行时将基于该类的各个方面计算该类的默认serialVersionUID值，但是根据编译器的不同，可能反序列化的过程会产生InvalidClassException。\
案例：[Person.java](https://www.cnblogs.com/DreamDrive/p/4005966.html)
