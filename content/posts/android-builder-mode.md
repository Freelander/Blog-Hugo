---
title: "通俗易懂了解 Android 设计模式 - 建造者模式"
date: 2021-08-21T10:49:27+08:00
tags: [Android,设计模式]
categories: 设计模式
draft: false
---

### 介绍

建造者模式属于设计模式三大类中的创建型模式，主要用来创建复杂的对象，用户可以不用关心其建造过程和细节。

简单例子：当要组装一台电脑时，我们选择好CPU、内存、硬盘等等，然后交给装机师傅，装机师傅就把电脑给组装起来，我们不需要关心是怎么拼装起来的。

### 定义

将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

### 使用场景

* 如果一个对象有非常复杂的内部结构「这些产品通常有很多属性」，那么使用建造者模式；
* 如果想把复杂对象的创建和使用分离开来，那么使用建造者模式「使用相同的创建步骤可以创建不同的产品」。

### UML 类图

![img](/images/2021/08/android-builder-mode-01.png)

#### 角色解释：

* Product（产品类）：要创建的复杂对象。在本类图中，产品类是一个具体的类，而非抽象类。实际编程中，产品类可以是由一个抽象类与它的不同实现组成，也可以是由多个抽象类与他们的实现组成。
* Builder（抽象建造者）：创建产品的抽象接口，一般至少有一个创建产品的抽象方法和一个返回产品的抽象方法。引入抽象类，是为了更容易扩展。
* ConcreteBuilder（实际的建造者）：继承 Builder 类，实现抽象类的所有抽象方法。实现具体的建造过程和细节。
* Director（指挥者类）：分配不同的建造者来创建产品，统一组装流程。

### 举个例子

按照惯例，拿公司涉及到的场景来举例，比如公司产品开发人员，当挖掘到一个新产品时，对新产品信息进行录入并创建。

#### 具体产品信息

```java
package com.gojun.designpatterns.builder;

/**
 * Description: 具体产品信息
 *
 * @author Jun 2021/8/22
 */
public class Product {

    // 产品 id（必填）
    private String mId;
    // 产品名称（选填）
    private String mName;
    // 产品价格（选填）
    private String mPrice;
    // 产品图片（选填）
    private String mImage;
    // 产品规格（选填）
    private String mOption;

    public void setId(String id) {
        mId = id;
    }

    public void setName(String name) {
        mName = name;
    }

    public void setPrice(String price) {
        mPrice = price;
    }

    public void setImage(String image) {
        mImage = image;
    }

    public void setOption(String option) {
        mOption = option;
    }

    @Override
    public String toString() {
        return "产品：'" + mId + '\'' +
                "\n名称：'" + mName + '\'' +
                "\n价格：'" + mPrice + '\'' +
                "\n图片：'" + mImage + '\'' +
                "\n规格：'" + mOption + '\'';
    }
}
```

#### 抽象的建造者

```java
package com.gojun.designpatterns.builder;

/**
 * Description: 抽象的建造者
 *
 * @author Jun 2021/8/22
 */
public abstract class Builder {
    // 录入产品名称
    public abstract Builder inputName(String name);
    // 录入产品价格
    public abstract Builder inputPrice(String price);
    // 录入产品图片
    public abstract Builder inputImage(String image);
    // 录入产品规格
    public abstract Builder inputOption(String option);

    // 创建产品
    public abstract Product create(String id);
}
```

### 具体的建造者

```java
package com.gojun.designpatterns.builder;

/**
 * Description: 具体的建造者
 *
 * @author Jun 2021/8/22
 */
public class ProductBuilder extends Builder {

    private final Product mProduct = new Product();

    @Override
    public Builder inputName(String name) {
        mProduct.setName(name);
        return this;
    }

    @Override
    public Builder inputPrice(String price) {
        mProduct.setPrice(price);
        return this;
    }

    @Override
    public Builder inputImage(String image) {
        mProduct.setImage(image);
        return this;
    }

    @Override
    public Builder inputOption(String option) {
        mProduct.setOption(option);
        return this;
    }

    @Override
    public Product create(String id) {
        mProduct.setId(id);
        return mProduct;
    }
}
```

#### 测试代码

```java
package com.gojun.designpatterns.builder;

/**
 * Description:
 *
 * @author Jun 2021/8/22
 */
public class BuilderDemo {

    public static void main(String[] args) {
        // 产品开发人员只管录入产品信息并触发创建指令，不管内部产品是如何创建入库
        Product product = new ProductBuilder()
                .inputName("小米 Mix4")
                .inputPrice("￥4999")
                .inputImage("https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1628588587.81086421.jpg")
                .inputOption("陶瓷白 12GB+512GB")
                .create("987650");

        // 输出产品信息
        System.out.print(product.toString());
    }
}
```

#### 结果

![img](/images/2021/08/android-builder-mode-02.png)

### 总结

从上面例子带大家简单了解下建造者模式，对于外部人员来说不需要知道具体产品构建过程，只需要负责提供相关信息即可。

在 Android 开发里建造者模式被用到的地方：

* Android 系统组件 AlertDialog、Notification、AnimatorSet.Builder 等都使用到建造者模式；
* 著名第三方库：Retrofit、Picasso、OkHttp、Luban。

#### 优点

* 封装性良好，隐藏内部构建细节。
* 易于解耦，将产品本身与产品创建过程进行解耦，可以使用相同的创建过程来得到不同的产品。也就说细节依赖抽象。
* 易于扩展，具体的建造者类之间相互独立，增加新的具体建造者无需修改原有类库的代码。
* 易于精确控制对象的创建，由于具体的建造者是独立的，因此可以对建造过程逐步细化，而不对其他的模块产生任何影响。

#### 缺点

* 产生多余的 Build 对象以及 Dirextor 类。
* 建造者模式所创建的产品一般具有较多的共同点，其组成部分相似；如果产品之间的差异性很大，则不适合使用建造者模式，因此其使用范围受到一定的限制。
* 如果产品的内部变化复杂，可能会导致需要定义很多具体建造者类来实现这种变化，导致系统变得很庞大。

### 参考链接

* [Android的设计模式-建造者模式](https://www.jianshu.com/p/154948d5adc6)
* [人人都会设计模式---建造者模式--Builder](https://juejin.cn/post/6844903518449434638#heading-6)