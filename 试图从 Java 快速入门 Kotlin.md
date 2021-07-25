# 试图从 Java 快速入门 Kotlin

> 以下笔记内容主要来自 [扔物线_给高级Android工程师的进阶手册 (rengwuxian.com)](https://rengwuxian.com/) 
>
> ~~tmd 他这个开头介绍语就看得我拳头硬了~~
>
> <img src=".\media\image-20210724094205465.webp" alt="image-20210724094205465" style="zoom: 33%;" />



## 阅读以下内容之前您需要注意

1. 该教程使用 [Android Studio](https://developer.android.google.cn/studio/preview) 作为开发的 IDE 
2. 就算您的 Java 是强子哥教的，也要假定自己是一个自信的 Java 高手



## 一些暂时不知道归类到哪儿的 Kotlin 特点

Kotlin 文件后缀名是 `.kt` ，就像 Java 文件是以 `.java` 结尾；

Kotlin 不用每行结束写分号 `;` 



## （重点）Kotlin 的空安全设计

IDE 会帮你智能的检测，避免 **在使用时调用 null 对象引发异常**，但侑子小姐曾说：“实现任何愿望都要支付对应的代价。”

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

用于处理这种情景：我很确定等到我真用的时候，它绝对不为空，但我现在确实没法给它赋值；

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
注意这里 AppCompatActivity 后面没有 '()'
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
7.   Kotlin 实例化一个类不需要 `new`关键字，直接这么写即可：

```kotlin
fun main() {
    var activity: Activity = NewActivity()
}
```

8.  Kotlin 可以使用 `is` 关键字代替 Java 的 `instanceof` 关键字进行类型判断，还可以使用 `as` 关键字直接强行类型转换；

   但更推荐使用 `as?` 安全强转：

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

一个类最多只有 1 个主构造函数；

主构造函数应该是最基本、最通用的构造函数；

一旦类中存在主构造函数，那么其他的次构造函数都需要通过 `this` 关键字直接或间接地调用主构造函数，称为 `必须性` 和 `第一性` ；

当一个类存在多个次构造函数时怎么调用：

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
    companion object {、
        const val CONST_NUMBER = 1
    }
}

const val CONST_SECOND_NUMBER = 2
```



## 数组和集合

```kotlin
val strs: Array<String> = arrayOf("a", "b", "c")
```

Kotlin 的数组是一个泛型的类，创建函数也是泛型函数，和集合数据类型一样



## Kotlin 更方便的函数简化

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



