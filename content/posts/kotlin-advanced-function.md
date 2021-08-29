---
title: "Kotlin 高阶函数与特性，让代码飞起！"
date: 2021-08-24T23:02:40+08:00
tags: [Kotlin,转载]
categories: Kotlin
draft: false
---

> 转自：掘金 王远道
>
> https://juejin.cn/post/6949128725900296200

Google 在 2017 年的时候就开始推广Kotlin语言作为Android开发的首选语言，现在都 2021 了，发现身边不少小伙伴还是全部用 Java 作为开发语言，对 Kotlin 的理解还停留在空指针判断上。我觉得有必要向他们安利一下 **我为什么选择Kotlin作为首选的开发语言**。

Kotlin 对 Java 的优势：

### 一、编译时判空机制

Kotlin在编译时会对可能会导致空指针异常的地方进行了强制判断，帮助我们规避掉绝大多数的空指针异常。主要体现在我们在声明变量或者参数的时候就必须显示给定这个变量或参数是否可以为空。例如：

```kotlin
 class User {
     fun sayHello(who: String) {
         println("Hello $who")
     }
 }

var user : User? = null //声明一个可以为空的user变量
```

在声明user变量的时候，通过在User类型后面加**”?“**来表示这个user对象是可能为空的，如果我们在使用user的时候不进行判空操作，编译器将直接抛出异常。

```kotlin
user.sayHello("Mark") //直接使用user对象无法编译通过
```

错误如下：

```kotlin
Only safe (?.) or non-null asserted (!!.) calls are allowed on a nullable receiver of type User?
```

要想使用可能为空的对象，有一下两种方案：

```kotlin
user?.sayHello("Mark") //对象后面加”?“，表示如果user对象不为空，才会执行后面的sayHello方法
//或者
user!!.sayHello("Mark") //强行认定user不为空，当user为空时会抛出NullPointException
```

如果我们在声明变量或参数的时候，在类型后面不加**"?"**，则表示这个对象不允许为空，变量的声明如下：

```kotlin
var user : User = User() // 由于user不能为空，我们无法给user赋值为null
//或者
lateinit var user : User // 先声明出user，延迟对user进行初始化操作
```

**注意**：如果使用**lateinit**延迟对象的初始化的话，在使用这个对象的时候要确保先对其进行初始化操作，否则会抛出异常：

```kotlin
kotlin.UninitializedPropertyAccessException:lateinit property user has not been initialized
```

以上是变量中的空判断，作为参数的时候也是一样的道理：

```kotlin
fun somebody1(user : User?){} // 允许传入的参数user为空
fun somebody2(user : User){}  // 不允许传入的参数user为空
```

切记，**在Kotlin中User和User?是两个不相同的类型**，因此在somebody2中传入User?类型的参数的话，是无法编译通过的。也正是因为这样，开发过程中存在的空指针隐患能在编译阶段被暴露出来，能帮助我们规避绝大多数的NullPointException。

以上就是Kotlin在规避空指针方面的应用。

小伙伴：就这？我多加几个if(user != null)的判断不也一样，为啥还要学一门新的语言？

打工人：别急，Kotlin的特性远远不止如此，且听我慢慢道来。

### 二、可选参数与具名参数

重载是我们开发过程经常见到，以前用Java的时候是如何进行方法的重载呢？

```kotlin
public void sayHello(String name){
  sayHello(name,null,null,null)
}

public void sayHello(String name,Integer age){
  sayHello(name,age,null,null)
}

public void sayHello(String name,Integer age,Integer tall){
  sayHello(name,age,tall,null)
}

public void sayHello(String name,Integer age,Integer tall,String interest){
  //balabala
}
```

上面是比较常见的重载，但是**如果再想要重载一个只要name和tall参数的方法，怎么重载？**

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtsaa3yrx6j60a00a0jrm02.jpg)

Kotlin的方法重载比Java要简洁许多：

```kotlin
//给age、tall、interest分别
fun sayHello(name:String,age:Int? = null,tall:Int? = null,interest:String? = null){
 //balabala
}
```

这一个方法变实现了上面四个方法的功能，并且还一不小心做了增强。再出现上面那个只要name和tall的尴尬问题，只需要使用具名调用的方式就可以了。

```kotlin
sayHello("Mark",18,180,"大宝剑") //传入了所有参数
sayHello("Mark",18) // 传入了name和age
sayHello("Mark",age = 18) //同上，显示的指定了age参数
sayHello("Mark",tall = 180) //传入了name和tall
sayHello("Mark",interest = "大宝剑") //直接大宝剑也是可以的
```

是不是既满足了Java重载的多个方法的功能，又增强了不少？

但有一点需要注意：**如果传入的可选参数是连续的，Kotlin可以推演出参数的类型，对号入座，但如果不连续，就需要用具名参数的方式指明要传入哪个参数。**

同样和作用于构造方法的重载。

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtsaa45ppnj60a109wwep02.jpg)

### 三、函数作为参数或返回值

虽然说JDK 1.8的时候，也支持了把函数作为参数传递的特性，但是却很少在项目中有见到过这么用的，具体是什么原因我也不太清楚，猜测是Java语言给开发者们留下的旧有印象吧。Java的实现这里就不做讨论了，重点介绍一下再Kotlin中是如何使用这一特性的。

我们现在实现一个最简单的函数参数方法，再讨论使用场景。

```kotlin
fun launch(block : () -> Unit){
 //do something
}
```

在上面的代码中，我们定义了一个名为**launch**的方法，而这个方法的参数是一个无参无返回值的函数，在调用方法的时候传入具体的函数。

如果这个函数参数是定义方法的最后一个参数，函数体可以写到括号表达式外部，如果括号表达式里面没有参数，则可以省略括号表达式，所以可以用如下形式调用launch方法：

```kotlin
launch{
 // 这是函数参数里面的具体实现
}
```

可能看起来有点抽象，我们可以描述一个需求，然后再实现这个需求，理解起来就更清楚一些。

需求：

A、B两人分别负责各自的运算逻辑对m和n两个数据进行处理，但是处理的最终结果需要在show方法里面展示出来，我们当然可以用if else的逻辑实现这个show方法，伪代码如下：

```kotlin
fun show(m,n){
  int v = if(A){//如果是A处理，返回m+n
     m+n
  } else if(B){//如果是B处理，返回m-n
     m-n
  } 
 showIt(v) //对结果进行最终处理
}
```

这样的写法虽然可以满足功能，但是如果有十几个人都要实现自己的逻辑呢？程序将进入if else的地狱。还有，如果show方法是在基础模块中定义，A和B又都是业务模块，把业务模块中的逻辑沉淀到基础模块是不合理的，在Java中遇到这种需求通常就要考虑**面向对象**了，基础模块中抽象出接口，A、B中对接口做实现，但这样的实现未免太复杂，我们是不是可以这么写呢：

```kotlin
//基础模块 block函数需要两个Int类型参数并返回值为Int类型
fun show(m:Int,n:Int,block : (m :Int, n:Int) -> Int){
  showIt(block(m,n))
}

//A 的模块
var blockA = fun (m :Int, n:Int){
  return m+n
}
show(m,n,blockA) //A 的模块调用基础模块的show方法

//B 的模块
var blockB = fun (m :Int, n:Int){
  return m-n
}
show(m,n,blockB) //B 的模块调用基础模块的show方法
```

相比抽象成接口的方式，是不是简洁许多？

**函数参数的另一个常用场景就是代替Callback**，作为Java程序员，每天都在与回调地狱做斗争，从RxJava的火爆程度上便可见一斑。

通常我们异步方法结果的获取都是通过Callback实现的，由于太过耳熟能详，这里就不再举例子了，只简单描述下如何通过接口参数简化回调地狱。

```kotlin
// 定义一个异步方法
fun asyncExecute(param:Any,result:(data:String) -> Unit){
  var result = //balabala 异步处理操作
  //balalba切回主线程
  result(data)
}

//调用异步方法,并把结果show出来
asyncExecute(param){data ->
  showData(data)
}
```

从代码观感层面来说，简直不要太友好啊！

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtsaa4aoipj60a0097jrn02.jpg)

函数除了作为参数，同样也可以作为返回值：

```kotlin
fun testMethod() : (str : String) -> Int{
 return { str ->
    str.length
  }
}
```

上面的代码定义了一个返回值为一个函数，该函数需要一个String类型的参数并返回一个Int型数据。不过函数作为返回值的场景，我基本是没有用到过，有使用过的盆友可以介绍一下心得~

### 四、拓展函数

资本家：小打，给我们的String新增一个方法helloWorker的方法，调用这个方法可以打印”打工人，打工魂，打工的都是人上人!“这句话。

打工人：（...什么脑残需求..）老板英明，只需要重写String新增这个方法就可以了，这样我们就有自己的String了。

资本家：重写String方法？你去财务领下工资，明天不用来了！

由于String是final类型，我们没法通过重写该类为它新增方法，一般操作String都是使用静态方法来处理，但有时候我们偏偏想不起来那个该死的静态方法在哪个类里面了...

没关系，有了拓展方法，再也不用担心这个问题了，请看：

```kotlin
//为String新增拓展方法
fun String.helloWorker(){
 println("打工人，打工魂，打工的都是人上人!")
}

"Jack".helloWorker()
"Mark".helloWorker()
//Jack在工地搬砖，Mark在做Android开发，他们都有光明的未来~
```

拓展方法的可以近似面向切面的方式为某个类新增方法，而不需要通过继承来实现。即使是被final修饰的类也可以进行拓展，因为它本身就是编译工具的一个障眼法（感兴趣的可以研究一下拓展函数的原理），但这并不妨碍我们在开发中的使用。

拓展函数的用途非常广泛，而且拓展方法对被拓展的类以及其子类都能生效。它的使用场景非常广泛，除了为某些类提供额外的方法外（为View提供统一的防连点，为Int提供dp2px的转换等等），还可以对业务功能庞大的类进行拓展，把业务复杂的业务逻辑梳理到拓展类里面，提高代码的可读性。

不得不说，自从用了拓展方法，吃饭都有劲儿了。

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtsaa4fze3j60a009lgm502.jpg)

### 五、内联函数

Kotlin提供了一系列的内联函数：**let,with,run,apply,also**，能帮我们提高代码的整洁度，比如：

```kotlin
user?.username = "Mark"
user?.age = 18
user?.tall = 180
//使用let函数
user?.let{
  it.username = "Mark"
  it.age = 18
  it.tall = 180
}

user?.sayHello()
user?.sayBye()
user?.sayGood()
//使用with函数
user?.with{
  sayHello()
  sayBye()
  sayGood()
}

//多数场景都是使用run，因为run是let和with的结合
```

关于**let,with,run,apply,also**的区别如下：

| 函数名 | 函数体内使用的对象       | 返回值 | 适用的场景                             |
| :--------- | :--------------------------- | :--------- | :----------------------------------------- |
| **let**    | **it**指代当前对象           | 闭包       | 合并多处判断null的操作                     |
| **with**   | **this**指代当前对象，可省略 | 闭包       | 合并多个方法调用                           |
| **run**    | **this**指代当前对象，可省略 | 闭包       | let和with的结合体                          |
| **apply**  | **this**指代当前对象，可省略 | this       | 能用run的地方就能用apply，多用于初始化对象 |
| **also**   | **it**指代当前对象           | this       | 能用let就能用also，可用于函数链式调用      |

除了上诉 Kotlin 为我们提供的内敛函数外，我们也可以自定义内联函数。内联函数比普通函数的优势在于，内联函数会把函数体复制到到调用函数的地方，避免了栈帧的入栈出栈操作。

### 六、字符串处理

字符串内可以引用变量或表达式，相当实用的一个功能，许多语言也有这个特性，但不知道Java一直不跟进。

```kotlin
var str = "My name is ${user.name},I am ${user.age} years old."
//如果直接使用变量可以省略大括号
var str2 = "My name is $username"
```

Kotlin也支持多行字符串：

```kotlin
var str = """
 Hello everybody，
 my name is ${user.name},
 I am ${user.age}
""".trimIndent()
```

多行字符串在某些特殊的场景里面还是比较好用的。

### 七、结语

以上就是 Kotlin 部分常用的特性，当 Kotlin 好用的特性远不止这些，流式操作、懒加载、无需 findViewById 就能拿到 xml 里面的对象等等。当然还有最重要的：**协程**。
