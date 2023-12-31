## 开始

Kotlin 有个特别好用的功能叫扩展，你可以给已有的类去额外添加函数和属性，而且既不需要改源码也不需要写子类。这就是今天这个视频的主题。另外很多人虽然会用扩展，但只会最基本的使用，比如就只用来写个叫 `dp` 的扩展属性来把 dp 值转成像素值：

```kotlin
val Float.dp
  get() = TypedValue.applyDimension(
    TypedValue.COMPLEX_UNIT_DIP,
    this,
    Resources.getSystem().displayMetrics
  )

...

val RADIUS = 200f.dp
```

Kotlin

稍微高级一点就不太行了，尤其是扩展函数和函数引用混在一起的时候就更是瞬间蒙圈。如果你有这样的问题，这个视频应该可以帮到你。

## Java 的 Math.pow()

在 Java 里我们如果想做幂运算——也就是几的几次方——要用静态方法 `pow(a, n)`

```java
Math.pow(2, 10); // 2 的 10 次方
```

Java

pow 这个词你可能不认识，其实它不是个完整的词，而是 power 的缩写，power 就是乘方的意思，哎中国人学程序经常还需要学英文好烦。这个 `pow(a, n)` 方法是 `Math` 类的一个静态方法，这类方法我们用得比较多的是 `max()` 和 `min()`

```java
Math.max(1, 2); // 2
Math.min(1, 2); // 1
```

Java

比较两个数的大小，用静态方法很符合直觉；但是幂运算的话，静态方法就不如成员方法来得更直观了：

```java
2.pow(10); // 要是 Java 里能这样写就好了
```

Java

但我们只能选择静态方法。为什么？很简单，因为 Integer、Float、Double 这几个类没提供这个方法，所以我们只能用 Math 类的静态方法。

## Kotlin 的扩展函数 Float.pow()

在 Kotlin 里，我们用的不是 Java 的 Integer、Float、Double，而是另外几个名字相同或相像的 Kotlin 自己新创造的类。这几个类同样没有提供 `pow()` 这个函数，但好的是，我们依然可以用看起来像是成员函数的方式来做幂运算。

```kotlin
2f.pow(10) // Kotlin 可以这么写
```

Kotlin

为什么？因为 `Float.pow(n: Int)` 是 Kotlin 给 `Float` 这个类增加的一个扩展函数：

```kotlin
// kotlin.util.MathJVM.kt
public actual inline fun Float.pow(n: Int): Float
    = nativeMath.pow(this.toDouble(), n.toDouble()).toFloat()
```

Kotlin

在声明一个函数的时候在函数名的左边写个类名再加个点，你就能对这个类的对象调用这个函数了。这种函数就叫扩展函数，Extension Functions。就好像你钻到这个类的源码里，改了它的代码，给它增加了一个新的函数一样。虽然事实上不是，但用起来基本一样。具体区别我等会儿说。

这种用法给我们的开发带来了极大的便利，我们可以用它来做很多事。

举个例子？

- 比如 pow() 吧？

- 再比如，AndroidX 里有个东西叫 ViewModel 对吧？——这个我以后有空的话也讲一下，很多人对 ViewModel 有很大误解，竟然以为这是用来写 MVVM 架构的——AndroidX 的 KTX 库里有一个对于 ComponentActivity 类的扩展函数叫 viewModels()：
  ![rxkdvv](https://image.rengwuxian.com/2021/01/07/rxkdvv.jpg)

  只要引用了对应的 KTX 库，在 Activity 里你可以直接就调用这个函数来很方便地初始化 ViewModel：

```kotlin
class MainActivity : AppCompatActivity() {

  val model: MyViewModel by viewModels()

  ...

}
```

而不需要重写 Activity 类。

- 类似的用法可以有很多很多，限制你的是你的想象力。所以其实对于扩展函数，你更需要注意的是谨慎和克制：需要用了再用，而不要因为它很酷很方便就能用则用。因为这些方便的东西如果太多，就会变成对你和同事的打扰。

## 扩展函数的写法

扩展函数写在哪都可以，但写的位置不同，作用域就也不同。所谓作用域就是说你能在哪些地方调用到它。
最简单的写法就是把它写成 Top Level 也就是顶层的，让它不属于任何类，这样你就能在任何类里使用它。这也和成员函数的作用域很像——哪里能用到这个类，哪里就能用到类里的这个函数：

```kotlin
package com.rengwuxian

fun String.method1(i: Int) {
  ...
}

...

"rengwuxian".method1(1)
```

Kotlin

有一点要注意了：这个函数属于谁？属于函数名左边的类吗？并不是的，它是个 Top-level Function，它谁也不属于，或者说它只属于它所在的 package。
那它为什么可以被这个类的对象调用呢？——因为它在函数名的左边呀！在 Kotlin 里，当你给声明的函数名左边加上一个类名的时候，表示你要给这个函数限定一个 Receiver——直译的话叫接收者，其实也就是哪个类的对象可以调用这个函数。虽然说你是个 Top-level Function，不属于任何类——确切地说是，不是任何一个类的成员函数——但我要限制只有通过某个类的对象才能调用你。这就是扩展函数的本质。
那这……和成员函数有什么区别吗？这种奇怪又绕脑子的知识有什么用吗？听我继续讲。

## 成员扩展函数

除了写成 Top Level 的，扩展函数也可以写在某个类里：

```kotlin
class Example {

  fun String.method2(i: Int) {
    ...
  }

}
```

Kotlin

然后你就可以在这个类里调用这个函数，但必须使用那个前缀类的对象来调用它：

```kotlin
class Example {

  fun String.method2(i: Int) {
    ...
  }

  ...

  "rengwuxian".method2(1) // 可以调用

}
```

Kotlin

看起来……有点奇怪了。这个函数这么写，它到底是属于谁的呀？属于外部的类还是左边前缀的类？
属于谁？这个「属于谁」其实有点模糊的，我需要问再明确点：它是谁的成员函数？当然是外部的类的成员函数了，因为它写在它里面嘛，对吧？那函数名左边的是什么？刚才我刚说过，它是这个函数的 Receiver，对吧？也就是谁可以去调用它。
所以它既是外部类的成员函数，又是前缀类的扩展函数。
这种既是成员函数、又是扩展函数的函数，它们的用法跟 Top Level 的扩展函数一样，只是由于它同时还是成员函数，所以只能在它所属的类里面被调用，到了外面就不能用了：

```kotlin
class Example {

  fun String.method2(i: Int) {
    ...
  }

  ...

  "rengwuxian".method2(1) // 可以调用

}

"rengwuxian".method2(1) // 类的外部不能调用
```

Kotlin

这个……也好理解吧？你为什么要把扩展函数写在类的里面？不就是为了让它不要被外界看见造成污染吗，是吧？

## 指向扩展函数的引用

在之前 Lambda 那一期视频里，我说过函数是可以使用双冒号被指向的对吧：

```kotlin
Int::toFloat
```

Kotlin

我当时也讲了，其实指向的并不是函数本身，而是和函数等价的一个对象，这也是为什么你可以对这个引用调用 invoke()，却不能对函数本身调用：

```kotlin
(Int::toFloat)(1) // 等价于 1.toFloat()
Int::toFloat.invoke(1) // 等价于 1.toFloat()
1.toFloat.invoke() // 报错
```

Kotlin

但是为了简单起见，我们通常可以把这个「指向和函数等价的对象的引用」称作是「指向这个函数的引用」，这个问题不大。那么我们基于这个叫法继续说。
普通函数可以被指向，扩展函数同样也是可以被指向的：

```kotlin
fun String.method1(i: Int) {

}

...

String::method1
```

Kotlin

不过如果这个扩展函数不是 Top-Level 的，也就是说如果它是某个类的成员函数，它就不能被引用了：

```kotlin
class Extensions {

  fun String.method1(i: Int) {
    ...
  }

  ...

  String::method1 // 报错
}
```

Kotlin

为什么？你想啊，一个成员函数怎么引用：类名加双冒号加函数名对吧？扩展函数呢？也是类名加双冒号加函数名对吧？只不过这次是 Receiver 的类名。那成员扩展函数呢？还用类名加双冒号加函数名呗？但是……用谁的类名？是这个函数所属的类名，还是它的 Receiver 的类名？这是有歧义的，所以 Kotlin 就干脆不许我们引用既是成员函数又是扩展函数的函数了，一了百了。
同样，跟普通函数的引用一样，扩展函数的引用也可以被调用，直接调用或者用 invoke() 都可以，不过要记得把 Receiver 也就是接收者或者说调用者填成第一个参数：

```kotlin
(String::method1)("rengwuxian", 1)
String::method1.invoke("rengwuxian", 1)

// 以上两句都等价于：
"rengwuxian".method1(1)
```

Kotlin

### 把扩展函数的引用赋值给变量

同样的，扩展函数的引用也可以赋值给变量：

```kotlin
val a: String.(Int) -> Unit = String::method1
```

Kotlin

然后你再拿着这个变量去调用，或者再次传递给别的变量，都是可以的：

```kotlin
"rengwuxian".a(1)
a("rengwuxian", 1)
a.invoke("rengwuxian", 1)
```

Kotlin

### 有无 Receiver 的变量的互换

另外大家可能会发现，当你拿着一个函数的引用去调用的时候，不管是一个普通的成员函数还是扩展函数，你都需要把 Receiver 也就是接收者或者调用者作为第一个参数填进去。

```kotlin
(String::method1)("rengwuxian", 1)  // 等价于 "rengwuxian".method1(1)
(Int::toFloat)(1) // 等价于 1.toFloat()
```

Kotlin

为什么？因为你拿到的是函数引用而不是调用者的对象，所以没办法在左边写上调用者啊，是吧？
所以 Kotlin 要想支持让我们拿着函数的引用去调用，就必须给个途径让我们提供调用者。那提供怎样的途径呢？最终 Kotlin 给我们的方案就是：在这种调用方式下，增加一个函数参数，让我们把第一个参数的位置填上调用者。这样，我们就可以用函数的引用来调用成员函数和扩展函数了。但同时，又有一个问题我不知道你们发现没有：
既然有 Receiver 的函数可以以无 Receiver 的方式来调用，那……它可以赋值给无 Receiver 的函数类型的变量吗？

```kotlin
val b: (String, Int) -> Unit = String::method1 // 这样可以吗？
```

Kotlin

答案是，可以的。在 Kotlin 里，每一个有 Receiver 的函数——其实就是成员函数和扩展函数——它的引用都可以赋值给两种不同的函数类型变量：一种是有 Receiver 的，一种是没有 Receiver 的：

```kotlin
val a: String.(Int) -> Unit = String::method1
val b: (String, Int) -> Unit = String::method1
```

Kotlin

这两种写法都是合法的。为什么？因为有用啊，是吧？有什么用我刚讲过，忘了的倒个带。

> 蔡依林：「终于看开……」

而且同样的，这两种类型的变量也可以互相赋值来进行转换：

```kotlin
val a: String.(Int) -> Unit = String::method1
val b: (String, Int) -> Unit = String::method1
val c: String.(Int) -> Unit = b
val d: (String, Int) -> Unit = a
```

Kotlin

懵了？懵就对了，不要急，继续看，知识掌握住了，下去慢慢试慢慢琢磨。

## 继续

继续讲。
既然这两种类型的变量可以互相赋值来转换，那不就是说无 Receiver 的函数引用也可以赋值给有 Receiver 的变量？
这样的话，是不是一个普通的无 Receiver 的函数也可以直接赋值给有 Receiver 的变量？

```kotlin
fun method3(s: String, i: Int) {

}

...

val e: (String, Int) -> Unit = ::method3
val f: String.(Int) -> Unit = ::method3 // 这种写法也行哦
```

Kotlin

哇塞，没有报错！
是的，这样赋值也是可以的。
通过这些类型的互相转换，你可以把一个本来没有 Receiver 的函数变得可以通过 Receiver 来调用：

```kotlin
fun method3(s: String, i: Int) {

}

...

val f: String.(Int) -> Unit = ::method3
"rengwuxian".method3(1) // 不允许调用，报错
"rengwuxian".f(1) // 可以调用
```

Kotlin

这就很爽了哈？
当然了你也可以反向操作，去把一个有 Receiver 的函数变得不能用 Receiver 调用：

```kotlin
fun String.method1(i: Int) {

}

...

val b: (String, Int) -> Unit = String::method1
"rengwuxian".method1(1) // 可以调用
"rengwuxian".b(1) // 不允许调用，报错
```

## 扩展属性

除了扩展函数，Kotlin 的扩展还包括扩展属性。它跟扩展函数是一个逻辑，就是在声明的属性左边写上类名加点，这就是一个扩展属性了，英文原名叫 Extension Property。

```kotlin
val Float.dp
  get() = TypedValue.applyDimension(
    TypedValue.COMPLEX_UNIT_DIP,
    this,
    Resources.getSystem().displayMetrics
  )

...

val RADIUS = 200f.dp
```

Kotlin

它的用法和扩展函数一样，但少了扩展函数在引用上以及 Receiver 上的一些比较绕的问题，所以很简单，你自己去研究吧。有些东西写成扩展属性是比扩展函数要更加直观和方便的，所以虽然它很简单，但研究一下绝对有好处。

## 总结

这次讲的内容挺多的，但其实也很简单，主要就这么几点：扩展函数、扩展函数的引用、有无 Receiver 的函数类型的转换以及扩展属性。记不住的把视频多刷几遍，不要怕，我在我的课程里也经常跟我的学员说：你把每节课多刷几遍，别嫌费时间，又不是电视剧，知识密度这么大的课程你多看几遍只赚不亏。

好了今天的视频就到这里，如果你喜欢我的内容，欢迎点赞留言收藏分享。扫码关注我，不错过我的任何新内容。我是扔物线，我不和你比高低，我只助你成长。我们下期见。