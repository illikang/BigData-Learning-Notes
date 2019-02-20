# Java中的String，StringBuilder与StringBuffer的区别
这三个类的区别主要在两个方面，即运行速度和线程安全两方面：
## 运行速度
StringBuilder > StringBuffer > String。String为字符串常量，而StringBulider和StringBuffer均为字符串变量，即String对象一旦创建之后该对象是不可更改的，但后两者的对象是变量，是可以更改的。

```
String str test="abc";
System.out.println(str);
>>abc
str=str+"de";
System.out.println(str);
>>abcde
```
如果运行这段代码会发现先输出“abc”，然后又输出“abcde”，好像是str这个对象被更改了，其实，这只是一种假象罢了，JVM对于这几行代码是这样处理的，首先创建一个String对象str，并把“abc”赋值给str，然后在第三行中，其实JVM又创建了一个新的对象也名为str，然后再把原来的str的值和“de”加起来再赋值给新的str，而原来的str就会被JVM的垃圾回收机制（GC）给回收掉了，所以，str实际上并没有被更改，也就是前面说的String对象一旦创建之后就不可更改了。所以，Java中对String对象进行的操作实际上是一个不断创建新的对象并且将旧的对象回收的一个过程，所以执行速度很慢。

而StringBuilder和StringBuffer的对象是变量，对变量进行操作就是直接对该对象进行更改，而不进行创建和回收的操作，所以速度要比String快很多。

## 线程安全
StringBuilder是线程不安全的，而StringBuffer是线程安全的。如果一个StringBuffer对象在字符串缓冲区被多个线程使用时，StringBuffer中很多方法可以带有synchronized关键字，所以可以保证线程安全，但StringBuilder的方法则没有该关键字，所以不能保证线程安全。


## 总结
  1. String适用于少量的字符串操作的情况。
  2. StringBuilder适用于单线程下字符缓冲区进行大量操作的情况。
  3. StringBuffer适用于多线程下在字符缓冲区进行大量操作的情况。
