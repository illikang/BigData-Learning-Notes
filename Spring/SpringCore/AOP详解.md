# AOP详解

## 横切关注点（Aspect）
系统由许多不同的组件组成，每一个组件个负责一块特定功能。除了实现自身核心的功能之外，这些组件还经常承担着额外的职责。诸如日志、事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的组件中去，这些系统服务通常被称为横切关注点，因为它们会跨越系统的多个组件。

如果将这些关注点分散到多个组件中去，你的代码将会带来双重的复杂性：
  * 实现系统关注点功能的代码将会重复出现在多个组件中。这意味着如果你要改变这些关注点的逻辑，必须修改各个模块中的相关实现。即使你把这些关注点抽象为一个独立的模块，其他模块只是调用它的方法，但方法的调用还是会重复出现在各个模块中。
  * 组件会因为那些与自身核心业务无关的代码而变得混乱。一个向地址簿增加地址条目的方法应该只关注如何添加地址，而不应该关注它是不是安的或者是否需要支持事物。

下图展示了这种复杂性。左边的业务对象与系统级服务结合得过于紧密。每个对象不但要知道它需要记日志、进行安全控制和参与事务，还要亲自执行这些服务。

![](img/AOP.jpg)

图中展示了这种复杂性。左边的业务对象与系统级服务结合得过于紧密。每个对象不但要知道它需要的日志、进行安全控制和参与事务，还要亲自执行这些服务。

## AOP
AOP（aspect-oriented programming）允许你把遍布应用各处的功能分离出来形成可重用的组件。AOP能够使这些服务模块化，并以声明的方式将它们应用到它们需要影响的组件中去。所造成的结果就是这些组件会具有更高的内聚性并且会更加关注自身的业务，完全不需要了解设计系统服务所带来的复杂性。总之，AOP能够确保POJO的简单性。

![](img/aop2.jpg)

如图所示，我们可以把切面想象为覆盖在很多组件之上的一个外壳。应用是由那些实现各自业务功能的模块组成的。借助AOP，可以使用各种功能层去包裹核心业务层。这些层以声明的方式灵活地应用到系统中，你的核心应用甚至根部不知道他们的存在。这是一个非常强大的理念，可以将安全、事务和日志关注点与核心业务逻辑相分离。

## 举例
上文DI详解中，我们用了骑士的例子。这里继续使用这个例子：

每一个人都熟知骑士所做的任何事情，这是因为吟游诗人用诗歌记载了骑士的实际并将其进行传唱。假设我们需要使用引诱诗人这个服务类来记载骑士的所有事迹。
```
public class Minstrel{

    private PrintStream stream;
    //
    public Minstrel(PrintStream stream){
      this.stream=stream;  
    }
    //
    public void singBeforeQuest() {
        stream.println("Fa la la,the knight is so bravel");
    }
    public void singAfterQuest() {
        stream.println("Tee hee hee the brave knight did embark on a quest ");
    }

}
```
在骑士执行每一个探险任务之前，singBeforeQuest()方法会被调用；在骑士完成探险任务之后，singAfterQuest()方法会被调用。现在改造之前的代码，把Minstrel加入进来。
```
public class BraveKnight implements Knight{
    //任务
    private Quest quest;
    private Minstrel minstrel;
    //骑士的构造方法,不再自己创建，而是通过参数传入的方法构建
    public DamselRescuingKnight(Quest quest,Minstrel minstrel){
        this.quest=quest;
        this.minstrel=minstrel;
    }
    //执行任务
    public void embarkOnQuest() {
        minstrel.singBeforeQuest();
        quest.embark();
        minstrel.singAfterQuest();
    }
}
```
借助DI，在spring配置中声明Minstrel bean并将其注入到BraveKnight的构造器中，似乎就OK了。

但这里有似乎有些问题。管理他的吟游诗人真的是骑士职责范围内的工作嘛？吟游诗人应该做他份内的事，根本不需要骑士命令他这么做。此外，因为骑士需要知道吟游诗人，所有就必须把吟游诗人注入到BraveKnight类中。这不仅使BraveKnight的代码复杂化了，而且还让我疑惑是否还需要一个不需要吟游诗人的骑士呢？如果Minstrel为Null会发生什么呢？我是否应该引入一个空值校验逻辑来覆盖该场景？简单的BraveKnight类开始变得复杂，如果你还需要应对没有吟游诗人时的场景，那代码变得更复杂。

但利用AOP，你可以声明吟游诗人必须歌颂骑士的探险事迹，而骑士本身不用直接访问Minstrel的方法。要将Minstrel抽象为一个切面，你所需要做的事情就是在一个Spring配置文件中声明它。
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

    <bean id="minstrel" class="com.leon.entity.Minstrel">
      <constructor-arg value="#{T(System).out}"/>
    </bean>

    <aop:config>
       <aop:aspect ref="minstrel">
           <!--定义切点-->
           <aop:pointcut id="embark" expression="execution(* *.embarkOnQuest(..))"/>
           <!--声明前置通知-->
           <aop:before pointcut-ref="embark" method="singBeforeQuest"/>
           <!--声明后置通知-->
           <aop:after pointcut-ref="embark" method="singAfterQuest"/>
       </aop:aspect>
    </aop:config>
    
</beans>
```
