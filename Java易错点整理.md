## JAVA易错点整理

### 1.Integer和int的区别
自动拆箱：Integer a = new Integer(10); int i = a; ====> Integer a = new Integer(10); int i = a.intValue();\
自动装箱: Integer a = 10; ====> Integer a = Integer.valueOf(10);\
    
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
