---
title: "通俗易懂了解 Android 设计模式 - 观察者模式"
date: 2021-08-07T10:59:04+08:00
tags: [Android,设计模式]
categories: 设计模式
draft: false
---

### 介绍

设计模式有三大类分别是创建型模式、结构型模式和行为型模式， 而观察者模式是属于行为型模式中的一种。
观察者模式又称为 “发布/订阅模式”，是一个使用频率非常高的模式之一，主要的作用是解耦，将被观察者和观察者解耦，使得它们之间减少依赖，甚至没有任何依赖。

### 定义

定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

### 使用场景

* 关联行为场景，需要注意的是，关联行为是可拆分的，而不是 “组合” 关系；
* 事件多级触发场景；
* 跨系统的消息交换场景，如消息队列，事件总线的处理机制。

### UML 类图

![img](/images/2021/08/android-observer-mode-02.png)

#### 角色解释：

* Subject（抽象被观察者）：所有对观察者对象的引用都保存在一个集合中，每个主体可以有任意数量的观察者，提供了添加和删除观察者对象的方法。
* ConcreteSubject（具体被观察者）：将相关状态存储在特定的 Observer Object 中，当特定 Subject 的内部状态发生变化时通知所有注册的观察者。
* Observer（抽象观察者）：为所有特定的观察者定义一个接口，以便在收到主题通知时进行自我更新。
* ConcrereObserver（具体观察者）：实现由抽象观察者定义的更新接口，当主题发生变化时更新自身状态。

### 举个例子

从工作事例中挑选一个场景来简单理解下观察者模式，比如公司通知员工进行打疫苗事项。

#### 观察者

```java
/**
 * Description: 员工是观察者
 */
public class Worker implements Observer {

    public String name;

    public Worker(String name) {
        this.name = name;
    }

    @Override
    public void update(Observable o, Object arg) {
        System.out.println(name + ", " + arg);
    }

    @Override
    public String toString() {
        return "员工：" + name;
    }
}

```

#### 被观察者

```java
/**
 * Description: 公司作为被观察者
 */
public class Company extends Observable {

    public void postNoticeMsg(String msg) {
        // 标识状态或者内容发生改变
        setChanged();
        // 通知所有员工
        notifyObservers(msg);
    }
}
```

### 测试代码

```java
/**
 * Description:测试
 */
public class ObserverDemo {
    public static void main(String[] args) {
        // 被观察的角色-公司
        Company company = new Company();

        // 观察者-员工
        Worker worker = new Worker("小二");
        Worker worker1 = new Worker("小三");
        Worker worker2 = new Worker("小四");
        Worker worker3 = new Worker("小五");
        Worker worker4 = new Worker("小六");

        // 把员工添加到公司中
        company.addObserver(worker);
        company.addObserver(worker1);
        company.addObserver(worker2);
        company.addObserver(worker3);
        company.addObserver(worker4);

        // 发布信息
        company.postNoticeMsg("明天公司安排你打疫苗");
    }
}
```

#### 结果

![img](/images/2021/08/android-observer-mode-01.png)

从结果来看，所有被注册到公司的员工，公司只要发送通知，底下所有员工都能收到消息，这就是观察者模式的一对多依赖关系，它们之间没有任何耦合。

### 总结

从上面例子带大家简单了解下观察者模式，Observer 和 Observable 均是 Java JDK 中的内置类型，可见观察者模式是非常重要的。

其实观察模式在 Android 里被广泛应用，简单列举下应用的地方：

* BroadcastReceiver 作为 Android 的四大组件之一，实际上也是一个典型的观察者模式；
* Android 官方架构组件 LiveData；
* ListView 和 RecyclerView 列表控件中的 notifyDataSetChanged 方法；
* 还有一些著名的第三方库 如 RxJava、RxAndroid、EventBus 等等。

因此大家有必要了解学习观察者模式，下面再简单总结下观察者模式的优缺点：

#### 优点

* 观察者和被观察者是分开的，它们之间是抽象的耦合而不是具体，因此它们各自更改不会影响到对方；
* 易于扩展，提升系统灵活性。

#### 缺点

* 在使用观察者模式时，需要考虑开发和运行效率问题，程序中包括一个被观察者、多个观察者、开发和调试等内容会比较复杂，而且在 Java 中消息的通知默认是顺序执行的，
一个观察者卡顿，会影响到整体的执行效率，在这种情况下，一般考虑采用异步的方式；
* 滥用会造成冗余的数据通知。

### 参考链接

* [Android Design Patterns - Observer Patterns](https://programmer.help/blogs/android-design-patterns-observer-patterns.html)
* [Android 设计模式学习（二）观察者模式应用实例](https://zhuanlan.zhihu.com/p/143479553)

