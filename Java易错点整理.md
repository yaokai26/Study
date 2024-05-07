## JAVA易错点整理

### 1.Integer和int的区别
自动拆箱:Integer a = new Integer(10); int i = a; ====> Integer a = new Integer(10); int i = a.intValue();\
自动装箱:Integer a = 10; ====> Integer a = Integer.valueOf(10);
    
    Integer a =10;
    Integer b =10;
    Integer c =128;
    Integer d =128;
    a==b;//true;因为-128~127 Integer会存放在缓存里，不会新建Integer对象
    c==d;//false; 超出范围，新建对象
    Integer e = new Integer(10);
    Integer f = new Integer(10);
    e==f;//false;因为是新建的对象，==比较的是地址

### 2.Math.round(11.5)和Math.round(-11.5)
    ceil是向上取整
    Math.ceil(11.5)---> 12.0
    Math.ceil(-11.5)---> -11.0
    floor是向下取整
    Math.floor(11.5)---> 11.0
    Math.floor(-11.5)---> -12.0
    round是floor(x+0.5)
    Math.round(11.5)--->Math.floor(11.5+0.5)--->Math.floor(12)--->12
    Math.round(-11.4)--->Math.floor(-11.4+0.5)--->Math.floor(-10.9)--->-11

### 3.abstract的method是否可同时是static,是否可同时是native，是否可同时是synchronized?
    1.static表示可以直接用类名调用方法，abstract表示把方法交给子类重写实现，如果同时调用，用类名调用抽象方法肯定不行
    2.native也是方法的声明，但是native是把方法移交给操作系统，abstract是把方法交给子类，冲突
    3.synchronized是表示方法同步，但是要有具体的同步操作，同步什么东西就成了问题，可以在子类中添加同步

### 4.try {}里有一个return语句，那么紧跟在这个try后的finally{}里的code会不会被执行，什么时候被执行，在return前还是后?
注意，finally里的语句在return前被执行,finally中的return会插销之前的return语句。
    
### 5.final,finally,finalize的区别
1.final：最终的
    
    1)final修饰类：表示类无法被继承，所有成员包含隐式final方法
    2)final修饰方法：表示方法不能被重写,若父类中final修饰的方法的访问权限是private，此时可以在子类中定义相同名字的方法，
    不与final矛盾，而是在子类中重新定义了新方法
    3)final修饰变量：表示该变量为常量，只能赋值一次，赋值后其值不再发生变化。修饰基本类型时，一经初始化该值就不变化，
    修饰引用类型时，该值在初始化后就不能再指向其他对象，但是该引用指向的对象内容可以发生变化
    举例：
    final StringBuffer str = new StringBuffer("abc");
    //str = new StringBuffer("def");
    str.append("def");final修饰变量的地址不能改变，但是该地址所指向的对象是可以变的

2.finally:异常处理的一部分
    finally中的语句块一定执行吗？答案是不一定。
    
    1)如果在没有执行try语句块之前就抛出异常或者return是不会执行finally语句中的语句的
    2)如果在try语句块中执行了System.exit(0)终止jvm的运行或者在执行try或者catch语句时被打断(interrupted)
    或者终止(killed)，finally语句也不会执行。
    
3.finalize：gc启动，对象被回收时调用，一个对象的finalize()方法只会被调用一次。   

### 6.计算String str1 = 'a' + 3 + "Hello"和 String str2 = "Hello" + 'a' + 3;
String str1 = 'a' + 3 + "Hello"; // char类型和int类型相加，转换为ASCII值 97+3=100，再和String类型相加 100Hello\
String str2 = "Hello" + 'a' + 3; // Helloa3

### 7.字节流和字符流的区别

    字节流：输入流InputStream,输出流 OutputStream
    字符流：输入流Reader,输出流Writer
    字节流是直接操作文件，而字符流在操作时先操作的缓冲区，通过缓冲区再操作文件，flush可以清空缓冲区。

### 8.OOM
    OOM是Out of Memory的简称,表示程序需要的内存空间大于JVM分配的内存空间,一般导致OOM的原因是:给JVM分配的内存太小,实际业务消耗的内存比较多,还有就是java应用里存在内存泄漏.
    内存泄漏:申请使用完的内存没有释放,导致虚拟机不能再次使用改内存,此时这段内存就泄漏了,因为申请者不用了,而又不能被虚拟机分配给别人用
    内存溢出:申请的内存超出了JVM所能提供的内存大小


### 9.servlet生命周期
    web容器加载servlet，生命周期开始。首先调用servlet的init方法，进行初始化，然后调用service方法，
    根据请求的不同调用do*方法(doGet/doPost),结束服务，调用servlet的destroy方法。

### 10.session什么时候被创建
    server端(servlet)调用HttpServletRequest.getSession(true)才会被创建.
