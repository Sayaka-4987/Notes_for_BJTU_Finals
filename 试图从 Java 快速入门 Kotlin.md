# 试图从 Java 快速入门 Kotlin

> 以下笔记内容主要来自 [扔物线_给高级Android工程师的进阶手册 (rengwuxian.com)](https://rengwuxian.com/) 
>
> ~~tmd 他这个开头介绍语就看得我拳头硬了~~
>
> <img src="D:\CyberAngel\github\Notes_for_BJTU_Finals\media\image-20210724094205465.webp" alt="image-20210724094205465" style="zoom: 33%;" />



## 阅读以下内容之前您需要注意

1. 该教程使用 [Android Studio](https://developer.android.google.cn/studio/preview) 作为开发的 IDE 
2. 就算您的 Java 是强子哥教的，也要假定自己是一个自信的 Java 高手



## 一些暂时不知道归类到哪儿的 Kotlin 特点

Kotlin 文件后缀名是 `.kt` ，就像 Java 文件是以 `.java` 结尾；



## Kotlin 的空安全设计

IDE 会帮你智能的检测，避免 **在使用时调用 null 对象引发异常**，但侑子小姐曾说：实现任何愿望都要支付对应的代价，这里的代价就是：

1. Kotlin 变量没有默认值，初始化也不允许为空（非空限制）；
2. `safe call`：如果你偏要解除非空限制，可以在类型后加一个 `?.`

```kotlin
class User {
    var name: String? = null
}
```

​	这个写法同样会对变量做一次非空确认之后再调用方法，而且线程安全；

3. `non-null asserted call`：非空断言，可以在类型后加一个 `!!` 

```kotlin
view!!.setBackgroundColor(Color.RED)
```

​	好消息：现在编译器再也不帮你检查非空限制了（编译器不会给你报错了）

​	坏消息：现在编译器再也不帮你检查非空限制了（运行时再出啥异常自己兜着吧）



## 延迟初始化

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



## 类型推断，但不是动态类型

在 Kotlin 中，如果一个变量声明时就赋值，你可以不写变量类型（是自动推断补上的，类型本身不可变）

```kotlin
var name: String = "Mike"
// 等价于
var name = "Mike"
```



## 声明只读变量

`var` 是 variable 的缩写，`val` 是 value 的缩写；

类似于 Java 的 `final` ， `val` 声明的只读变量只能赋值一次，不能再做任何修改了；

```kotlin
val size = 18
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

   

## 函数使用也要服从空安全设计

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















