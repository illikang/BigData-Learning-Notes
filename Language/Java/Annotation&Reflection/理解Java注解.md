# 理解Java注解

## 一、注解如同标签
想象代码具有生命，注解就是对于代码中某个鲜活个体贴上去的一张标签。简化来讲，注解如同一张标签。如果初学过程中大脑防空的时候，不要慌张，对自己说：

注解，标签。注解，标签。

## 二、注解语法
同class和interface一样，注解也属于一种类型。它是在Java SE 5.0 版本中开始引入的概念。

### 1.注解的定义
```
public @interface TestAnnotation{

}
```
它的形式跟接口很类似，不过前面多了一个@符号。上面的代码就创建了一个名字为TestAnnotation的注解。可以简单理解为创建了一张名字为TestAnnotation的标签。
### 2.标签的应用
上面创建了一个注解，那么注解如何使用呢？
```
@TestAnnotation
public class Test{

}
```
创建一个类Test,然后在类定义的地方加上@TestAnnotation就可以用TestAnnotation注解这个类了。可以简单理解为将 TestAnnotation这张标签贴到Test这个类上面。

## 三、元注解
### 1. 元注解的涵义
元注解是可以注解到注解上的注解，更准确的说元注解是一种基本注解，但它能够应用到其他的注解上面。如果难以理解的话，可以这样理解：元注解也是一张标签，但是它是一张特殊的标签，他的作用和目的就是作为其他标签的基本构成部件。元标签有@Retention，@Documented，@Target，@Inherited和@Repeatable五种。
### 2. Retention注解
源代码如下：
```
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
    RetentionPolicy value();
}
```
```
public enum RetentionPolicy {
    SOURCE,
    CLASS,
    RUNTIME
}
```
Retention为保留期的意思。当@Retention应用到一个注解上的时候，它解释说明了这个注解的存活时间。它的取值如下:
* RetentionPolicy.SOURCE，注解只在源码阶段保留，在编译器进行编译时它将被丢弃忽视。
* RetentionPolicy.CLASS，注解只被保留到编译运行的时候，它并不会被加载到JVM中。
* RetentionPolicy.RUNTIME，注解可以保留到程序运行的时候，它会被加载进入到JVM中，所以在程序运行时可以获取它们。

```
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
}
```
上面的代码中，我们指定TestAnnotation可以在程序运行期间被获取到。

### 3.Documented注解
注解@Documented的作用是能够将注解中的元素包含到Javadoc中去。
### 4.Target注解
源码如下：
```
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    ElementType[] value();
}
```
```
public enum ElementType {
    TYPE,
    FIELD,
    METHOD,
    PARAMETER,
    CONSTRUCTOR,
    LOCAL_VARIABLE,
    ANNOTATION_TYPE,
    PACKAGE,
    TYPE_PARAMETER,
    TYPE_USE
}
```
Target是目标的意思，@Target指定了注解运用的地方。
* ElementType.ANNOTATION_TYPE 可以给一个注解进行注解
* ElementType.CONSTRUCTOR 可以给构造方法进行注解
* ElementType.FIELD 可以给属性进行注解
* ElementType.LOCAL_VARIABLE 可以给局部变量进行注解
* ElementType.METHOD 可以给方法进行注解
* ElementType.PACKAGE 可以给一个包进行注解
* ElementType.PARAMETER 可以给一个方法内的参数进行注解
* ElementType.TYPE 可以给一个类型进行注解，比如类、接口、枚举

### 5.Inherited注解
Inherited是继承的意思，但是它并不是说注解本身可以继承，而是说如果一个父类被@Inherited注解过的注解进行注解的话，那么如果它的子类没有被任何注解应用的话，那么这个子类就继承了父类的注解。
### 6.Repeatable注解
Repeatable是可重复的意思。@Repeatable 是 Java 1.8 才加进来的，所以算是一个新的特性。什么样的注解会多次应用呢？通常是注解的值可以同时取多个。举个例子，一个人他既是程序员又是产品经理同时他还是个画家：
```
@interface Persons {
	Person[]  value();
}

@Repeatable(Persons.class)
@interface Person{
	String role default "";
}

@Person(role="artist")
@Person(role="coder")
@Person(role="PM")
public class SuperMan{

}
```
## 四、注解的属性
注解的属性也叫成员变量。注解只有成员变量，没有方法。注解的成员变量在注解的定义中以“无形参的方法”形式来声明，其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型。比如：

```
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface MyAnnotation {
    int id();
    String name();
}
```
上面代码定义了MyAnnotation这个注解中拥有“id”和“name”两个属性。在使用的时候，我们应该给他们进行赋值。赋值的方式是在注解的括号内以value=""形式，多个属性之间用“,”隔开。
## 五、Java预制的注解
学习了上面相关的知识，我们已经可以自己定义一个注解了。其实Java语言本身已经提供几个现成的注解。
1. 注解@Deprecated，用来标记过时的元素。
2. 注解@Override，用来提示子类要复写父类中被@Override修饰的方法。
3. 注解@SuppressWarnings，用来阻止警告的意思。
4. 注解SafeVarargs，参数安全类型注解。它的目的是提醒开发者不要用参数做一些不安全的操作，它的存在会阻止编译器产生unchecked 这样的警告。它是在Java1.7的版本中加入的。
5. 注解@FunctionalInterface，函数式接口注解，这个是 Java 1.8 版本引入的新特性。函数式编程很火，所以 Java 8 也及时添加了这个特性。
