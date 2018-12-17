# DI详解

## 依赖的产生
在面向对象的编程开发中，任何一个有实际意义的应用都会由两个或者更多的类组成，这些类相互之间进行协作来完成特定的业务逻辑。按照传统的做法，每个对象负责管理与自己相互协作的对象（即它所依赖的对象）的引用，这将会导致高度耦合和难以测试的代码。

## 耦合

耦合具有两面性。一方面，紧密耦合的代码难以测试、难以复用、难以理解，并且典型地表现出“打地鼠”式的bug特性（修复一个bug，将会出现一个或者更多新的bug）。另一方面，一定程度的耦合又是必须的——完全没有耦合的的代码什么也做不了。为了完成有实际意义的功能，不同的类必须以适当的方式进行交互。

总而言之，耦合时必须的，但应当被小心谨慎地管理。比如下面的例子:

```
//救少女骑士
public class DamselRescuingKnight implements Knight{
    //救少女任务
    private DamselRescuingQuest quest;
    //骑士的构造方法，与救少女任务紧密绑在了一起
    public DamselRescuingKnight(){
        this.quest=new DamselRescuingQuest();
    }
    //执行任务
    public void embarkOnQuest() {
        quest.embark();
    }
}
```
可以看到，例子中骑士的构造方法使得这个其实与救少女的任务紧密耦合到了一起，因此极大地限制了这个骑士执行探险的能力。如果要执行一个杀龙的任务，那就只能在创造另外一个骑士了。更糟糕的是，为这个DamselRescuingKnight编写测试单元将出奇地困难。在这样的一个测试中，你必须保证当骑士的embarkOnQuest()方法被调用的时候，探险的embark()方法也要被调用。

## DI（依赖注入）
在传统的做法中，由于每个对象负责管理与自己相互协作的对象的引用，而导致了高度的耦合。如果什么办法可以让对象不用管理与自己相互协作的对象的引用，同时还能让这种协作关系维持，那不就可以解除这种高度耦合关系了吗。

Spring的DI（依赖注入）正式为此而来。通过DI，对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候进行设定。对象无需自行创建或管理他们的以来关系。如图：

![](img/DI.jpg)

依赖注入会将所依赖的关系自动交给目标对象，而不是让对象自己去获取依赖（通过new来创建）。有了DI，我们尝试来改造上面的代码：
```
public class BraveKnight implements Knight{
    //任务
    private Quest quest;
    //骑士的构造方法,不再自己创建，而是通过参数传入的方法构建
    public DamselRescuingKnight(Quest quest){
        this.quest=quest;
    }
    //执行任务
    public void embarkOnQuest() {
        quest.embark();
    }
}
```
可以看到，不同于之前的DamselRescuingKnight，BraveKnight没有自行创建探险任务，而是在构造的时候把探险任务作为构造器参数注入。这是依赖注入的方式之一，及构造器注入（constructor injection）。更重要的是，传入的探险类型是Quest，也就是所有的探险任务都必须实现的一个接口。所以BraveKnight能够相应DamselRescuingQuest，SlayDragonQuest,MakeRoundTableRounderQuest等任意的Quest的实现。

这里的要点是BraveKnight没有与任何特定的Quest任务发生耦合。对它来说，被要求挑战的探险任务只要实现了Quest接口，那么具体是那种类型的探险就无关紧要了。这就是DI所带来的最大收益——松耦合。如果一个对象只通过接口（而不是具体实现或初始化过程）来表明依赖关系，那么这种依赖在对象本身毫不知情的情况下，用不同的具体实现进行替换。

## 装配（wiring）
现在BraveKnight类可以接受你传递给它的任意一种Quest的实现，但该怎样把特定的Quest实现传给它呢？假设现在有一个杀龙的任务SlayDragonQuest挺适合：
```
public class SlayDragonQuest implements Quest{
    //这里没有用print，而用了PrintStream是为了演示装配的作用
    private PrintStream stream;
    //
    public SlayDragonQuest(PrintStream steam){
      this.stream=steam;  
    }
    //执行任务
    public void embark() {
        stream.println("Embarking on quest to slay the dragon!");
    }
}
```
现在最大的问题是，我们该如何将SlayDragonQuest交给BraveKnight呢？又如何将PrintStream交给SlayDragonQuest呢？如果我们只是创建了类，而没有new过类的对象，那如何完成传递呢？显然如果我们要new一个brvaeKnight的话，需要传入的是一个Quest的实现对象，而不是一个类，最终创建的语句会类似于下面的语句。这样显然又产生了耦合。
```
BraveKnight knight= new BraveKnight(new SlayDragonQuest(new PrintStream("a.txt")));
```

创建应用组件之间协作的行为通常称为装配。要完成DI，还需要提供自动装配，而Spring就为我们提供了多种装配bean的方式，采用XML是很常见的一种装配方式。
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
               http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="knight" class="com.leon.entity.BraveKnight">
        <constructor-arg ref="quest"/>

    </bean>

    <bean id="quest" class="com.leon.entity.SlayDragonQuest">
        <constructor-arg value="#{T(System).out}"/>
    </bean>

</beans>    
```
在这里，BraveKnight 和SlayDragonQuest 被声明为Spring中的bean。就在这里，BraveKnight来讲，它在构造时传入了对SlayDragonQuest bean的引用，将其作为构造器参数。同时，SlayDragonQuest bean的声明使用了Spring表达式语言将System.out传入了SlayDragonQuest的构造器中。

至此，我们借助Spring帮助完全实现了依赖注入。要想获得Knight的对象，只需要从ApplcationContext中getBean()就可以了。至于他们之间的依赖关系等等，Spring容器都负责管理了。
```
ApplicationContext applicationContext=new ClassPathXmlApplicationContext("knight.xml");
BraveKnight knight= (BraveKnight) applicationContext.getBean(Knight.class);
knight.embarkOnQuest();
```
尽管BraveKnight依赖于Quest，但是它并不知道传递给它的是什么类型的Quest，也不知道这个Quest来自哪里。与之类似，SlayDragonQuest依赖于PrintStream,但是在编码时它并不需要知道这个PrintStream是什么样子的。只有Spring通过它的配置，能够了解这些组成部分是如何装配起来的。这样的话，就可以在不改变所依赖的类的情况下，修改依赖关系。
