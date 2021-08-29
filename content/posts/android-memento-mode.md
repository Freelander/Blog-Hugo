---
title: "通俗易懂了解 Android 设计模式 - 备忘录模式"
date: 2021-08-29T12:12:27+08:00
tags: [Android,设计模式]
categories: 设计模式
draft: false
---

### 介绍

备忘录模式是三大设计模式中的行为型模式一种，用于保存对象当前状态，并且在之后可以重新恢复此状态。

值得注意的是备忘录模式实现方式要保证保存的对象状态不能被对象从外部访问，目的保护好被保存的这些对象状态的完整性以及内部实现不向外部暴露。

### 定义

在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可将该对象恢复到原先保存的状态。

通俗理解，备忘录模式就是对一个对象的备份模式，提供一种程序数据的备份方法。

### 使用场景

* 需要保存或恢复一个对象在某一个时刻的数据状态时。


### UML 类图

![img](/images/2021/08/android-memento-mode-01.png)

#### 角色解释：

Memento：备忘录角色，负责存储 Originator 的内部状态，并且可以防止 Originator 以外的对象访问 Memento。

Caretaker：管理者角色，负责存储备忘录，不能对备忘录的内容进行操作和访问，只能将备忘录传递给其他对象。

Originator：发起者角色，负责创建和恢复记录的数据。同时 Originator 还可以根据需要决定 Memento 存储自身的哪些内部状态。

### 举个例子

场景：在手机电商 App 应用上，用户正在对某个商品进行编辑评论时，突然接到来电，此时需要存储下当前已编辑的评论内容，等待用户接完电话后恢复继续进行评论。

#### 备忘录角色

```java
/**
 * Description: 备忘录角色，存储已输入的评论内容及时间
 *
 * @author Jun 2021/8/29
 */
public class Memento {

    // 输入的评论内容
    private String writeCommentContent;
    // 评论时间
    private String time;

    public String getWriteCommentContent() {
        return writeCommentContent;
    }

    public void setWriteCommentContent(String writeCommentContent) {
        this.writeCommentContent = writeCommentContent;
    }

    public String getTime() {
        return time;
    }

    public void setTime(String time) {
        this.time = time;
    }
}
```

#### 管理者角色

```java
/**
 * Description: 管理者角色，负责存储备忘录角色
 *
 * @author Jun 2021/8/29
 */
public class Caretaker {

   private Memento mMemento;

    public Memento getMemento() {
        return mMemento;
    }

    public void setMemento(Memento memento) {
        mMemento = memento;
    }
}
```

#### 发起者角色

```java
import java.util.Date;

/**
 * Description: 发起者角色，负责创建、保存及恢复记录的评论内容
 *
 * @author Jun 2021/8/29
 */
public class Originator {

    // 输入的评论内容
    private String writeCommentContent;
    // 评论时间
    private String time;

    public String getWriteCommentContent() {
        return writeCommentContent;
    }

    public void setWriteCommentContent(String writeCommentContent) {
        this.writeCommentContent = writeCommentContent;
    }

    public String getTime() {
        return time;
    }

    public void setTime(String time) {
        this.time = time;
    }

    @Override
    public String toString() {
        return "在 " + time + " 对商品进行评论：" + writeCommentContent;
    }

    // 开始编写评论内容
    public void startWriteComment(String content) {
        this.writeCommentContent = content;
        this.time = new Date(System.currentTimeMillis()).toString();
        System.out.println("开始编写评论: " + toString());
    }

    // 停止编写评论内容
    public void stopWriteComment() {
        System.out.println("停止编写评论");
    }

    // 保存当前编写的评论内容
    public Memento saveWrite() {
        Memento memento = new Memento();
        memento.setTime(time);
        memento.setWriteCommentContent(writeCommentContent);
        return memento;
    }

    // 恢复上次编写评论
    public void restoreWrite(Memento memento) {
        this.writeCommentContent = memento.getWriteCommentContent();
        this.time = memento.getTime();
        System.out.println("恢复上次编写评论: " + this.toString());
    }
}
```

#### 测试

```java
/**
 * Description: 测试
 *
 * @author Jun 2021/8/29
 */
public class MementoDemo {

    public static void main(String[] args) {
        // 用户开始编写商品评论信息
        Originator originator = new Originator();
        originator.startWriteComment("裙子尺寸合适，没有色差，穿上很好看！");

        // 突然用户接到来电停止评论
        originator.stopWriteComment();

        // 此时程序需保存用户编写的评论内容
        Caretaker caretaker = new Caretaker();
        caretaker.setMemento(originator.saveWrite());

        // 用户通话结束回到界面继续编写的评论内容
        originator.restoreWrite(caretaker.getMemento());
    }
}
```

#### 结果

![img](/images/2021/08/android-memento-mode-02.png)

### 总结

通过上面场景了解到备忘录模式不仅在思想上简单，并且在代码的使用上也很简单，通过用单独的类来保存和读取状态，这样就避免直接访问数据以及其中的内部实现。

发起角色 Originator 状态的备份与恢复，自身不需要管理与操作，而是由 Caretaker(客户端) 自行按需要处理，当发起角色的状态出现问题时，Caretaker 可以很轻松将它恢复。

在 Android 源码中的使用到备忘录模式的，当属 Activity 中的 onSaveInstanceState()，onRestoreInstanceState() 两个方法，
在非主动退出或跳转到其他 Activity 时，触发 onSaveInstanceState() 方法进行数据备份，下次恢复启动 Activity 时读取备份的数据。

#### 优点

* 提供了一种状态备份恢复机制，使用户能方便的回到某个时刻的状态；
* 实现了信息的封装，使用户不需要关心状态的保存细节。

#### 缺点

* 每一次保存都会消耗一定的内存，当类的成员变量过多，势必会消耗更多的资源。

### 参考链接

* [Design patterns: Memento](https://medium.com/@sawomirkowalski/design-patterns-memento-f9fc1e127316)
* [Android 编程设计模式之备忘录模式详解](https://cloud.tencent.com/developer/article/1735840)
