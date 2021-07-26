---
layout:     post   				        # 使用的布局（不需要改）
title:      试图从 Java 快速入门 Kotlin	# 标题 
subtitle:   然后背起门转身就跑			   	# 副标题
date:       2021-07-26 				    # 时间
author:     YXWang 					    # 作者
header-img: img/post-bg-keybord.jpg 	# 这篇文章的标题背景图片
catalog: true 						    # 是否归档
tags:								    # 标签
    - Kotlin
    - Java
---

# 试图从 Java 快速入门 Kotlin

> 以下笔记内容主要来自 [扔物线_给高级Android工程师的进阶手册 (rengwuxian.com)](https://rengwuxian.com/) 
>
> ~~tmd 他这个开头介绍语就看得我拳头硬了，这个校外老师也让我拳头硬了~~
>
> <img src=".\media\image-20210724094205465.webp" alt="image-20210724094205465" style="zoom: 33%;" />



## 阅读以下内容之前您需要注意

1. 该教程使用 [Android Studio](https://developer.android.google.cn/studio/preview) 作为开发的 IDE 
2. 就算您的 Java 是强子哥教的，也要假定自己是一个自信的 Java 高手，加油！



## 一些暂时不知道归类到哪儿的 Kotlin 特点

Kotlin 文件后缀名是 `.kt` ，就像 Java 文件是以 `.java` 结尾；

Kotlin **不用每行结束写分号** `;` 



## （重点）Kotlin 的空安全设计

IDE 会帮你智能的检测，避免 **在使用时调用 null 对象引发异常**，但侑子小姐曾说：“实现任何愿望都要支付<u>对应的代价</u>。”

Kotlin 这儿防止 NullPointerException 的代价就是：

1. Kotlin 变量没有默认值，初始化也不允许为空（非空限制）；
2. `safe call`：如果你偏要**解除非空限制**，可以在类型后加一个 `?.`

```kotlin
class User {
    var name: String? = null
}
```

​	这个写法同样会对变量做一次非空确认之后再调用方法，而且线程安全；

3. `non-null asserted call`：**非空断言**，可以在类型后加一个 `!!` 

```kotlin
view!!.setBackgroundColor(Color.RED)
```

​	好消息：现在编译器再也不帮你检查非空限制了（编译器不会给你报错了）

​	坏消息：现在编译器再也不帮你检查非空限制了（运行时再出啥异常自己兜着吧）



### 延迟初始化

用于处理这种情景：我很确定等到我真用的时候，它绝对不为空，但我**现在确实没法给它赋值**；

在 Kotlin 中需要使用 `lateinit` 关键字，显式的提示 IDE “我待会儿再初始化”这件事：

```kotlin
lateinit var view: View
override fun onCreate(...) {
    ...
    view = findViewById(R.id.tvContent)
}
```

代价和之前一样，编译器不会再帮你检查这个变量用之前有没有初始化了；



### 类型推断，但不是动态类型

在 Kotlin 中，如果一个变量声明时就赋值，你可以不写变量类型（是自动推断补上的，类型本身不可变）

```kotlin
var name: String = "Mike"
// 等价于
var name = "Mike"
```



## 声明函数

先放一个 Java 声明格式在这里对比：

```java
Food cook(String name) {
    ...
}
```

在 Kotlin 中声明函数：

```kotlin
fun cook(name: String): Food {
    ...
}
```

1. 开头写 `fun` 关键字；

2. 返回值的类型要加个冒号 `:` 写在函数参数后面；

3. 无返回值的函数（指其他语言的 void）可以（在下面例子 Food 的位置）写返回 Unit ，也可以省略不写；

   

### 函数使用也要服从空安全设计

总结就是 **变量的可空性质** 必须和 **函数参数的可空性质** 相匹配；

```kotlin
// 可空变量传给不可空参数，报错
var myName : String? = "rengwuxian"
fun cook(name: String) : Food {}
cook(myName)
  
// 可空变量传给可空参数，正常运行
var myName : String? = "rengwuxian"
fun cook(name: String?) : Food {}
cook(myName)

// 不可空变量传给不可空参数，正常运行
var myName : String = "rengwuxian"
fun cook(name: String) : Food {}
cook(myName)
```



## Kotlin 的 getter/setter 钩子函数

注意：`val` 声明的只读变量不能调用 setter 函数；

```kotlin
class User {
    var name = "Mike"
    fun run() {
        name = "Mary"
        // 实际上是调用 setName("Mary")
        // IDE 的代码补全功能实测发现，你打了 setName 也会被提示换成 name
        
        println(name)
        // 实际上是调用 print(getName())
        // IDE 的代码补全功能会在你打出 getn 的时候直接提示 name 而不是 getName
    }
}
```

对于一个 Kotlin类：

```kotlin
class Kotlin {
  var name = "kaixue.io"
}
```

上文的的 Kotlin 代码在编译后大致等价于这样的 Java 代码：

```java
public final class Kotlin {
   @NotNull
   private String name = "kaixue.io";
   //  String name 就是 Kotlin 帮我们自动创建的一个 *Backing Field* ，这个 field 对编码的人不可见，但会自动应用于 getter 和 setter

   @NotNull
   public final String getName() {
      return this.name;
   }

   public final void setName(@NotNull String name) {
      this.name = name;
   }
}
```



## Kotlin 的基本类型

Kotlin 在语言层面简化了 Java 中的 `int` 基本数据类型 和 `Integer` 封装类型，把二者都合并成了 `Int`（ `Double` , `Float` , `Long`,  `Short`,  `Byte` 也同理）；

所有东西都变成了对象，但是 Kotlin 会对这些类型是否装箱进行判断，装箱与不装箱涉及到程序运行时的性能开销。这里我看不懂，所以快进到结论： **使用基本类型尽量用不可空变量** 



## Kotlin 类和对象

一个 Kotlin 的例子，此处 MainActivity 类继承自 AppCompatActivity 类：

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) 
    {
        ...
    }
}
```

对比 Java 相同功能的代码：

```java
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        ...
    }
}
```

找不同：

1. Kotlin 变量/函数在不加可见性修饰符时，默认是 public 的，可以直接省略不写；
2. Java 的 `extends` 继承和 `implement` 实现接口，在 Kotlin 被统一为 `:` 

```java
// Java 
public class Main2Activity extends AppCompatActivity implements Impl { }
```

```kotlin
// Kotlin
class MainActivity : AppCompatActivity(), Impl {}
```

```kotlin
// 注意这里 AppCompatActivity 后面没有 '()'
class MainActivity : AppCompatActivity {
    constructor() {
    }
}
```

3. Kotlin 把构造函数单独用了一个 `constructor` 关键字：

```kotlin
class MainActivity : AppCompatActivity() {
    ...
}
   
// 实际等价于：
class MainActivity constructor() : AppCompatActivity() {
	...
}
   
// 也可以写成：注意这里 AppCompatActivity 后面没有 '()'
class MainActivity : AppCompatActivity {
    constructor() {
       ...
    }
}
```

4.  `override` 变成了关键字，且有遗传性，但 Kotlin 抛弃了 `protected` 关键字；
5.  Kotlin 的类默认是 final 的，需要加一个 `open` 关键字才能使它可被继承； 
6.  依然有 `abstract` 关键字
7.  Kotlin 实例化一个类不需要 `new` 关键字，直接这么写即可：

```kotlin
fun main() {
    var activity: Activity = NewActivity()
}
```

8.  Kotlin 可以使用 `is` 关键字代替 Java 的 `instanceof` 关键字进行类型判断，还可以使用 `as` 关键字直接强行类型转换；但更推荐使用 `as?` 安全强转：


```kotlin
fun main() {
    var activity: Activity = NewActivity()
    // (activity as? NewActivity) 之后是一个可空类型的对象，所以，需要使用 '?.' 来调用
    (activity as? NewActivity)?.action()
}
```

强转若成功就执行之后的调用，若不成功就不执行；



## Kotlin 主构造函数

这是一个普通的构造函数：

```kotlin
class User {
    var name: String
    constructor(name: String) {
        this.name = name
    }
}
```

而使用主构造函数的版本长这样：

```kotlin
class User constructor(name: String) {
    // 与构造函数中的 name 是同一个
    var name: String = name
}
```

主构造函数提供了一种更简单的方法来写构造函数，写法是：constructor 紧跟在类名后，让类的属性（或者 init 代码块）引用构造函数中的参数；

```kotlin
class User constructor(name: String) {
    var name: String
    init {
        this.name = name
    }
}
```

注意事项：

1. 一个类最多只有 1 个主构造函数；

2. 主构造函数应该是最基本、最通用的构造函数；

3. 一旦类中存在主构造函数，那么其他的次构造函数都需要通过 `this` 关键字直接或间接地调用主构造函数，称为 **必须性** 和 **第一性** ；

4. 当一个类存在多个次构造函数时怎么调用：


```kotlin
class User constructor(var name: String) {
    constructor(name: String, id: Int) : this(name) {
    }
    constructor(name: String, id: Int, age: Int) : this(name, id) {
    }
}
```

可以发现 `:` 符号在 Kotlin 中其实是表示了一种**依赖关系** ，在此处表示依赖于其他构造器；

### 可在主构造器里声明属性

```kotlin
// 参数声明时加上 var 或者 val 意为在类中同时创建该名称的属性
class User(var name: String) {
}

// 这样的写法等价于：
class User(name: String) {
  var name: String = name
}
```



## Kotlin 类的初始化写法总结

1. 创建一个 `User` 类：

```kotlin
class User {
}
```

2. 添加一个参数为 `name` 与 `id` 的主构造函数：

```kotlin
class User(name: String, id: String) {
}
```

3. 将主构造函数中的 `name` 与 `id` 加上 `val` 关键字，声明为类的属性：

```kotlin
class User(val name: String, val id: String) {
}
```

4. 在 `init` 代码块中添加一些初始化逻辑：

```kotlin
class User(val name: String, val id: String) {
    init {
        ...
    }
}
```

5. 最后再添加其他次构造函数：

```kotlin
class User(val name: String, val id: String) {
    init {
        ...
    }
    constructor(person: Person) : this(person.name, person.id) {
    }
}
```



## Kotlin 只读变量

`var` 是 variable 的缩写，`val` 是 value 的缩写；

类似于 Java 的 `final` ， `val` 声明的只读变量只能赋值一次，不能再做任何修改了；

```kotlin
val size = 18
```



## Kotlin 静态：不用 `static` ，而是 `companion object` 

先看 Java 里怎么写常量字符串：

```java
public static final String CONST_STRING = "A String";
```

在 Kotlin 里需要写成这个样子：

```kotlin
class Sample {
    companion object {
        val anotherString = "Another String"
    }
}
```



###  静态类：用`object` 关键字声明单例

Java 中的 Object （大写首字母！）根类在 Kotlin 中变成了 `Any`，`Any` 和 Object 作用一样，作为**所有类的基类**；

> 就是个概念，生活中的类似形容还包括万王之王、天可汗啥的，历史学得好的朋友可以联想一下（这个比喻一点也不像吧）

Kotlin 的 `object` 不是类，而是一种叫对象的关键字，作用是创建一个**单例类**，并且创建一个这个类的对象：

```kotlin
object Sample {
    val name = "A name"
}
```

使用时直接通过它的类名访问：

```kotlin
Sample.name
```

Kotlin 实现单例类只需要这么写：

```kotlin
object A {
    val number: Int = 1
    fun method() {
        println("A.method()")
    }
}    
```

和 Java 相比的不同点有：

1. 和类的定义类似，但是把 `class` 换成了 `object` ；
2. 不需要额外维护一个实例变量 `sInstance` ；
3. 不需要 `getInstance()` 方法，也能保证实例只创建一次；
4. 能实现线程安全

#### 参考资料：关于 Java 的单例模式是什么

> 单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
>
> 这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。
>
> **意图：**保证一个类仅有一个实例，并提供一个访问它的全局访问点。
>
> **主要解决：**一个全局使用的类频繁地创建与销毁。
>
> **何时使用：**当您想控制实例数目，节省系统资源的时候。
>
> **如何解决：**判断系统是否已经有这个单例，如果有则返回，如果没有则创建。
>
> **关键代码：**构造函数是私有的。



### 静态变量：用 `companion` 关键字

类中嵌套的对象可以用 `companion` 修饰：

```kotlin
class A {
    companion object B {
        var c: Int = 0
    }
}
```

1. `companion` 关键字的含义是“伴随、伴生”，表示修饰的对象和外部类绑定；
2. 一个类只允许有一个伴生对象；
3. 一个类可以有多个嵌套对象；

调用时写成：

```kotlin
A.B.c 
// 或者不写 B
A.c 
```



## Kotlin 顶层声明（全局变量）

`top-level declaration` ：把属性和函数的声明写在所有 class 外，就可以获得直接属于 `package` 的**全局变量**；

主要好处是提高效率，减少项目中的重复代码；

出现两个同名的顶级函数/变量时，IDE 会自动加上包前缀来区分；



### 小结： object 、companion object 、和 top-level 选用建议 

1. 如果想写**工具类**的功能，直接创建文件，写 top-level 顶层函数；
2. 如果需要**继承**别的类或者**实现接口**，就用 `object` 或 `companion object`；



## Kotlin 的常量

和其他语言没什么区别，常量指的就是 compile-time constant ，即编译器在编译时就知道这个变量在每个调用处的实际值；

性质：

1. 需要用修饰常量的 `const` 关键字修饰；
2. 常量必须声明在对象或者 top-level 顶层中；
3. **只有基本类型和 String 类型**可以声明成常量；

```kotlin
class Sample {
    companion object {
        const val CONST_NUMBER = 1
    }
}

const val CONST_SECOND_NUMBER = 2
```



## 可见性

Kotlin 中有四种可见性修饰符：

1. `public`：（是 Kotlin 默认的可见性，没必要写）公开，可见性最大，哪里都可以引用；
2. `private`：私有，可见性最小，根据声明位置不同可分为类中可见和文件中可见；
3. `protected`：保护，相当于 `private` + 子类可见；
4. `internal`：内部，仅对 module 内可见；

这里不多介绍了，和其他语言区别没那么大。



## 数组和集合

### 数组（Array）

Kotlin 为数组准备了一个 Array 类，对基本类型元素还有 intArray, byteArray ... 等特别的数组类型；

Array 是一个泛型的类，创建函数也是泛型函数，和集合数据类型一样；

```kotlin
// 声明 String 数组和 Int 数组
val strs: Array<String> = arrayOf("a", "b", "c")
val intArray = intArrayOf(1, 2, 3)

// 声明一个集合
val strList = listOf("a", "b", "c")
```



### 集合（List, Set, Map）

Kotlin 有三种集合类型：List、Set 和 Map：（性质跟其他语言基本一样）

`List` 以固定顺序存储一组元素，元素可以重复；

`Set` 存储一组互不相等的元素，通常没有固定顺序；

`Map` 存储 key-value 对的数据集合，key 互不相等，不同的key可以对应相同的 value；

```kotlin
// 创建一个 List：
val strList = listOf("a", "b", "c")

// 集合都允许把子类的 List 赋值给父类的 List（协变性质）：
val strs: List<String> = listOf("a", "b", "c")
val anys: List<Any> = strs // success

// 创建一个 Set：          
val strSet = setOf("a", "b", "c")

// 创建一个 Map，Kotlin 用中缀表达式：
val map = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 3)

// 从 map 中以 key 取 value：
val value1 = map.get("key1")	
// 也可以用方括号运算符：
val value2 = map["key2"]

// 在 map 中给 key 赋值新的 value
val map = mutableMapOf("key1" to 1, "key2" to 2)
map.put("key1", 2)
// 也可以用方括号运算符：
map["key1"] = 2
```



### 可变和只读

Kotlin 的集合按能否修改又可分为**不可变集合（只读集合）**和**可变集合**两种，不可变集合（只读集合）的含义是：

1. 集合 `size()` 不可变；
2. 集合的元素值不可变；
3. `listOf()` 是创建不可变的 List，`mutableListOf()` 才是创建可变的 List（Map 和 Set 同理）
4. 不可变的 List 可以通过 `toMutableList()` 系函数，返回新建的一个可变的集合（Map 和 Set 同理）
5. 可变集合可以直接用 `.add()` 增加元素；
6. 不可变集合可以用 `.plus()` 方法增加元素（实际上，这个方法的实现不是增加了元素，是背后复制了一个长度+1，带上新元素的**新集合**）；



### 数组与集合的操作

这里写在一起，是因为很多操作**同时对数组、集合可用**；

集合功能更多，但数组不用装箱性能更好；

数组依然能用下标操作：

```kotlin
println(strs[0])
strs[1] = "B"
```

Java 也有的那些工具函数：

`get()`

`set()`

`contains()`

`first()`

`find()`

Kotlin 特有的一些方法：

```kotlin
// forEach：遍历每一个元素
intArray.forEach { i ->
    print(i + " ")
}

// filter：对每个元素进行过滤操作，如果 lambda 表达式中的条件成立则留下该元素，否则剔除，最终生成新的集合
val newList: List = intArray.filter { i ->
    i != 1		// 过滤掉数组中等于 1 的元素
}

// map：遍历每个元素并执行给定表达式，最终形成新的集合
val newList: List = intArray.map { i ->
    i + 1 		// 每个元素加 1
}

// flatMap：遍历每个元素，并为每个元素创建新的集合，最后合并到一个集合中
intArray.flatMap { i ->
    listOf("${i + 1}", "a") // 👈 生成新集合
}
```



## Kotlin 的 `Sequence` 容器

功能：`Sequence ` 和 `Iterable` 一样用来遍历一组数据，并可以对每个元素进行特定的处理；

`Sequence ` 又称为“惰性集合操作”，实际运行有**懒加载机制**：

1. 一旦满足遍历退出的条件，就不再进行后续不必要的遍历过程；
2.  `Sequence` 在整个流程中只有一个 `Iterable` ，不像 `List` 这种实现 `Iterable` 接口的集合类，每次函数调用产生的临时 `Iterable` 会导致额外的内存消耗；

Sequence 的三种创建方式：

1. 类似 `listOf()` ，使用一组元素创建：

```kotlin
sequenceOf("a", "b", "c")
```

2. 使用 `Iterable` 创建：

```kotlin
// 这里的 List 实现了 Iterable 接口
val list = listOf("a", "b", "c")
list.asSequence()
```

3. 使用 lambda 表达式创建：

```kotlin
// lambda 表达式，第二个及以后的元素都是前一个元素 it 再 +1 的值
val sequence = generateSequence(0) { it + 1 }
```



## Kotlin 的 `Range` 区间

Kotlin 区间的常见写法如下：

```kotlin
// 这里的 0..1000 表示闭区间 [0, 1000]
val range: IntRange = 0..1000 
```

除了 `IntRange` ，还有 `CharRange` 以及 `LongRange`；

Kotlin 中没有纯开区间的定义，不过有半开区间的定义：

```kotlin
// 这里的 0 until 1000 表示半开区间 [0, 1000) 
val range: IntRange = 0 until 1000 
```

区间是设计用于遍历的工具， `in` 关键字可以与 `for` 循环结合使用，表示挨个遍历 `range` 中的值（类似 Python ）：

```kotlin
val range = 0..1000
// 默认步长 step 为 1，输出：0, 1, 2, 3, 4, 5, 6, 7....1000,
for (i in range) {
    print("$i, ")
}
```

除了使用默认的步长 1，还可以通过 `step` 设置步长：

```kotlin
val range = 0..1000
// 设置步长为 2，输出：0, 2, 4, 6, 8, 10,....1000,
for (i in range step 2) {
    print("$i, ")
}
```

Kotlin 还提供了递减区间 `downTo` ，不过递减没有半开区间的用法：

```kotlin
//  4 downTo 1 就表示递减的闭区间 [4, 1]，输出4, 3, 2, 1, 
for (i in 4 downTo 1) {
    print("$i, ")
}
```

Kotlin 的区间开闭，概括是不能碰到开区间不包括的那个点；



## Kotlin 更方便的函数简化

### 把 C 语言的三元运算符 `?:` 换成 if-else 表达式

```kotlin
val max = if (a > b) a else b
// 等价于其他语言的（以 C 举例） int v = (a>b) ? a : b;
```

上面的 `if/else` 的分支中是一个变量，其实分支中还可以是一个代码块，代码块的最后一行会作为结果返回：

```kotlin
val max = if (a > b) {
    println("max:a")
    a // 返回 a
} else {
    println("max:b")
    b // 返回 b
}
```



### Kotlin的 `?:` 是 Elvis 操作符

之前学过空安全调用 `?.` 操作符，当对象非空时它执行后面的调用，对象为空时就会返回 `null`；

```kotlin
val str: String? = "Hello"
// IDE 报错，Type mismatch. Required:Int. Found:Int?
var length: Int = str?.length
```

报错的原因当是 `str` 为 null 时，我们没有值可以返回给 `length`，这时使用 Kotlin 中的 Elvis 操作符 `?:` 来兜底：

```kotlin
val str: String? = "Hello"
// 如果左侧表达式 str?.length 结果为空，则返回右侧的值 -1:
val length: Int = str?.length ?: -1
```

还有一种验证逻辑中的常见用法：

```kotlin
fun validate(user: User) {
    // 验证 user.id 是否为空，为空时 return 
    val id = user.id ?: return 
}

// 等效于：
fun validate(user: User) {
    if (user.id == null) {
        return
    }
    val id = user.id
}
```



### 使用 `=` 连接返回值

对于只有一行的函数，可以去掉`{}` 和 `return` ，直接使用 `=` 符号连接返回值：

```kotlin
fun area(width: Int, height: Int): Int = width * height

// 等价于：
fun area(width: Int, height: Int): Int {
    return width * height
}
```

因为 Kotlin 还有类型推断的特性，所以函数的返回类型都可以省略：

```kotlin
fun area(width: Int, height: Int) = width * height
```

```kotlin
fun sayHi(name: String) = println("Hi " + name)
```



### 设定参数默认值

这里 `"world"` 就是参数 name 的默认值，若调用 sayHi 函数时没有提供参数，就会使用默认值；

需要注意的是，假如提供参数比函数需求的少，是从左到右依次分配的，C 语言也有类似特性，所以此处不多介绍；



### Kotlin 命名参数和位置参数

```kotlin
fun sayHi(name: String = "world", age: Int) {
    ...
}

// 错误写法：希望给 age 赋值 10 
sayHi(10) 
// 注意这时 IDE 会报以下两个错误：
// 1. The integer literal does not conform to the expected type String
// 2. No value passed for parameter 'age'

// 正确写法：使用命名参数，显示指定要把 10 赋值给 age
sayHi(age = 21)
```

对于参数比较多的函数，可以多使用命名参数增加代码可读性：

（

```kotlin
fun sayHi(name: String = "world", age: Int, isStudent: Boolean = true, isFat: Boolean = true, isTall: Boolean = true) {
    ...
}

// 能跑，但可读性不够好
sayHi("world", 21, false, true, false)

// 扔物线觉得这么写更明白，但我不明白，IDE 它不要面子的吗 ……
sayHi(name = "wo", age = 21, isStudent = false, isFat = true, isTall = false)
```

与命名参数相对的一个概念是 **位置参数**，也就是 **按位置顺序** 进行参数填写；

如果要混用位置参数与命名参数（虽然无论如何都不建议这么做，但是扔物线写了），必须把 **所有的位置参数都放在第一个命名参数之前** ：

```kotlin
fun sayHi(name: String = "world", age: Int) {
    ...
}

// 错误写法，IDE 将报错 Mixing named and positioned arguments is not allowed（不允许混用位置和命名参数）：
sayHi(name = "wo", 21) 

// 正确写法：
sayHi("wo", age = 21) 
```



### 本地函数（嵌套函数）

这是一个登录验证函数，它本来长这样：

```kotlin
fun login(user: String, password: String, illegalStr: String) {
    // 验证 user 是否为空
    if (user.isEmpty()) {
        throw IllegalArgumentException(illegalStr)
    }
    // 验证 password 是否为空
    if (password.isEmpty()) {
        throw IllegalArgumentException(illegalStr)
    }
}
```

用 Kotlin 的 `嵌套函数` 修改后：

```kotlin
fun login(user: String, password: String, illegalStr: String) {
    fun validate(value: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException(illegalStr)
        }
    }
    ...
}
```

主要的改动是：

1. 检查参数是否为空的步骤显得很多余，又不想把这段逻辑外露，此时我们就可以适用嵌套函数，在 login 函数内部再声明一个 `fun validate(value: String) ` 
2. 在该嵌套函数内直接使用外层函数 `login` 的参数 `illegalStr` 

3. 其实还有另一种更简单的写法：

```kotlin
fun login(user: String, password: String, illegalStr: String) {
    require(user.isNotEmpty()) { illegalStr }
    require(password.isNotEmpty()) { illegalStr }
}
```

用到了 lambda 表达式以及 Kotlin 内置的 `require` 函数；



### `when` 代替 `switch` 

Kotlin 的 `when`：

```kotlin
when (x) {
    1 -> { println("1") }
    2 -> { println("2") }
    else -> { println("else") }
}
```

与 Java 相比的不同点有：

1. 省略了 `case` 和 `break`，Kotlin 自动为每个分支加上了 `break` 的功能，防止我们像 Java 那样写错，~~也防止脑瘫学校期末考什么 fall-down 特性~~；
2. Java / C 中的默认分支使用的是 `default` 关键字，Kotlin 中使用的是 `else`；

`when` 也可以作为表达式进行使用，分支中最后一行的结果作为返回值；但需要注意**这时必须要有 `else` 分支**，确保无论怎样都会有结果返回；

```kotlin
val value: Int = when (x) {
    1 -> { x + 1 }
    2, 3, 4 -> { x * 2 }
    else -> { x + 5 }
}
```

`when` 应用场合比较灵活，以下是扔物线的几个例子：

```kotlin
// 使用 in 检测是否在一个区间或者集合中：
when (x) {
    in 1..10 -> print("x 在区间 1..10 中")
    in listOf(1,2) -> print("x 在集合中")
    !in 10..20 -> print("x 不在区间 10..20 中")
    else -> print("不在任何区间上")
}

// 使用 is 进行特定类型的检测：
val isString = when(x) {
     is String -> true
     else -> false
 }

// 省略 when 后面的参数，分支条件用一个布尔表达式:
when {
    str1.contains("a") -> print("字符串 str1 包含 a")
    str2.length == 3 -> print("字符串 str2 的长度为 3")
}
```



### 改进的 `for` 循环

~~让我们看看高级程序设计语言里最拉的是谁？~~

```kotlin
val array = intArrayOf(1, 2, 3, 4)
for (item in array) {
    ...
}

// 等效于 for (int i = 0; i <= 10; i++)
for (i in 0..10) {
    println(i)
}
```





## Kotlin 字符串

1. 可以像 Java 一样用 `+` 拼接字符串；

2. 可以用类似 C 系语言的转义字符，比如 `\n` , `\0`

3. 还可以有**类似 PHP** 的、更方便的写法，即用 `$` 加变量名：

```kotlin
val name = "world"
println("Hi $name")
```

​	`$` 后还可以跟表达式，但表达式是一个整体，要用 `{}` 包裹：

```kotlin
val name = "world"
println("Hi ${name.length}") 
```



### Kotlin 原生字符串：当不想用转义字符的时候

`raw string` ：使用方法是用一对 `"""` 把字符串部分括起来：

```kotlin
val name = "world"
val myName = "kotlin"
           👇
val text = """
      Hi $name!
    My name is $myName.\n
"""
println(text)
```

原生字符串具有以下性质：

1. `$` 符号引用的变量名仍然生效；
2. 不处理转义字符，例如 `\n` , `\0` ；
3. 最后输出的内容格式和 `"""` 括号内文本完全一致；

输出结果如下：

```text
      Hi world!
    My name is kotlin.\n
```

还可以通过 `trimMargin()` 函数去除每行前面的空格：

```kotlin
val text = """
      |Hi world!
    |My name is kotlin.
""".trimMargin()
println(text)
```

输出结果如下：

```text
Hi world!
My name is kotlin.
```

使用 `trimMargin()` 函数有以下几个注意点：

1. `|` 符号为默认的边界前缀，前面**只能有空格**，否则不生效！
2. 输出时 `|` 符号本身以及它前面的空格都会被删除；
3. 边界前缀还可以使用其他字符，比如 `trimMargin("/")`；



## Kotlin的 `==` 和 `===` 运算符

`==` 可以对基本数据类型以及 `String` 等类型进行内容比较，相当于 Java 中的 `equals` 方法；

而`===` 是对引用的**内存地址进行比较**，相当于 Java 中的 `==`；



## 未完待续中 ... 后续又发现什么有用的会更新在前面的

没人给我校对，如果发现有啥问题欢迎评论区或者私聊告诉我！

