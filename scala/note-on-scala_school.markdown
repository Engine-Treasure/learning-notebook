# Scala 学堂

## 基础

### Why Scala

- 表达能力
    - 函数是一等公民
    - 闭包
- 简洁
    - 类型推断
    - 函数创建的文法支持
- Java互操作性
    - 可重用Java库
    - 可重用Java工具
    - 没有性能惩罚

### Syntax

#### 表达式

- Scala中（几乎）一切都是表达式。

#### 函数

- 函数不带参数时，可以不写括号。`three()` 等价于 `three`
- 匿名函数：`(参数列表) => 函数体`
- 匿名函数保存成不变量 `val addOne = (x: Int) => x + 1`
- 偏应用：
    - `_` 应用于一个函数，结果得到另一个函数，Scala 使用下划线表示不同上下文中的不同事物，通常可以将其视为没有命名的`通配符`
    - 可以部分应用参数列表中的任意参数
- `柯里化函数`
    - `def multiply(m: Int)(n: Int): Int = m * n`
    - `val timeTwo = multiply(2) _` - 部分应用第二个参数
    - `timeTwo(3)` - 结果为 2 \* 3 = 6
- 可以对任何多参数函数进行柯里化
- 参数类型后加 `*` - 可变长度参数

```scala
def capitalizeAll(args: String*) = {
  args.map { arg =>
    arg.capitalize
   }

}
```

- 方法就是可以访问类的状态的函数

#### 类

- `构造函数`不是特殊的方法，是除了类的方法定义之外的代码
- `Scala 是高度面向表达式的：大多数都是表达式而非指令`。
- [Function vs Method](http://stackoverflow.com/questions/2529184/difference-between-method-and-function-in-scala)
- 如果子类和父类实际上没有区别，类型别名应优先于继承

#### 继承

- 抽象类 - 定义了方法但没有实现它们，由扩展抽象类的子类来实现。`不能创建抽象类的实例`
- (存在方法只有定义没有实现的类，就是抽象类)

#### 特质

- Traits (特质) 是一些字段和行为的集合，可以`扩展`或混入 (mixin) 类中。(类似于 Java 的接口)
- 通过 `with` 关键字，一个类可以扩展多个特质：`class BMW extends Car with Shiny`
- [特质的观点](http://twitter.github.io/effectivescala/#Object oriented programming-Traits)
- 什么时候应该使用特质而不是抽象类？(两者都可以定义一个类型的一些行为，并要求继承者定义一些其他行为)
    - `优先使用特质`。一个类扩展多个特质是很方便的，但只能扩展一个抽象类
    - 如果需要构造函数参数，使用抽象类。因为抽象类可以定义带参数的构造函数，但特质不行

#### 类型

- 函数也可以是`泛型`，来使用所有类型。`方括号语法引入类型参数`
- 使用泛型键值的缓存

```scala
trait Cache[K, V]{
    def get(key: K): V
    def put(key: K, value: V)
    def delete(key: K)
}
```

- 引入类型参数的方法：`def remove[K](key: K)`

#### apply 方法

- `apply` 使实例化对象看起来像是在调用一个方法

#### 单例对象

- 单例对象用于一个类的唯一实例。通常用于工厂模式
- 单例对象可以和类具有相同的名称，此时该对象也被称为“伴生对象”。我们通常将伴生对象作为工厂使用。

```scala
class Bar(foo: String)

object Bar {
  def apply(foo: String) = new Bar(foo)
}
```

#### 函数即对象

- `函数是一些特质的集合`。具体而言，`具有一个参数的函数是 Function1 特质的一个实例`。该特质定义了 `apply()`语法糖，使调用对象就是调用一个函数：

```scala
object addOne extends Function1[Int, Int]{
    def apply(m: Int): Int = m + 1
}
```

- `Function` 特质集合下标从 0 到 22。
- `apply 语法糖有助于统一对象和函数式编程的二重性`。可以传递类，并当作函数使用。
- 函数本质上是类的实例
- 在类中定义的方法是方法而不是函数。
- 在 repl 中独立定义的方法是 Function\* 的实例
- 类可以扩展 Function，这样类的实例可以使用 () 调用 `class AddOne extends Function1[Int, Int]`，更快捷的方式：`class AddOne extends (Int => Int)`

#### 包

- 值和函数不能在类或单例对象之外定义。单例对象是组织静态函数(static function)的有效工具。

#### 模式匹配

- `_` - 通配符，保证了模式匹配可以处理所有情况
- [A Tour of Scala: Pattern Match](http://docs.scala-lang.org/tutorials/tour/pattern-matching.html)
- [什么时候使用模式匹配](http://twitter.github.io/effectivescala/#Functional programming-Pattern matching)
- [模式匹配格式化](http://twitter.github.io/effectivescala/#Formatting-Pattern matching)
- 匹配类成员：

```scala
def calcType(calc: Calculator) = calc match {
  case _ if calc.brand == "HP" && calc.model == "20B" => "financial"
  case _ if calc.brand == "HP" && calc.model == "48G" => "scientific"
  case _ if calc.brand == "HP" && calc.model == "30B" => "business"
  case _ => "unknown"

}
```

#### 样本类

- 样本类可以方便得存储和匹配类的内容，不用 `new` 关键字就能创建实例
- 样本类就是被设计用在模式匹配中的：

```scala
def calcType(calc: Calculator) = calc match {
  case Calculator("HP", "20B") => "financial"
  case Calculator("HP", "48G") => "scientific"
  case Calculator("HP", "30B") => "business"
  case Calculator(ourBrand, ourModel) => "Calculator: %s %s is of unknown type".format(ourBrand, ourModel)
  // case Calculator(_, _) => "Calculator of unknown type"  //另一种通配写法
  // case _ => "Calculator of unknown type"  // 不将匹配对象指定为 Calculator 类型的写法
  // case c@Calculator(_, _) => "Calculator: %s of unknown type".format(c)  // 重命名匹配的值
}
```

#### 异常

- 异常可以在 try-catch-finally 语法中通过模式匹配使用。

## 集合

- [怎样使用集合](http://twitter.github.io/effectivescala/#Collections)
- List - 元素线性存储
- Set - 无序，互异
- Tuple
    - 在不使用类的前提下，将元素组合起来形成简单的逻辑组合
    - 使用位置下标来读取对象，下标基于 1：`t._1`
    - 创建 2 个元素的 Tuple，可以使用特殊语法 `->`：`1 -> 2`
    - [解构绑定](http://twitter.github.io/effectivescala/#Functional programming-Destructuring bindings)
- Map
    - 可以持有基本数据类型
    - `Map(1 -> "one", 2 -> "two")`，也可以是 `Map((1, "one"), (2, "two"))`
- Option
    - 表示一个可能包含值的容器
    - Option本身是泛型的，并且有两个子类： `Some[T]` 或`None`
    - `Map.get` 使用 `Option` 作为其返回值
    - `getOrElse()` - 带默认值的 `get`
    - 或者使用模式匹配来处理 `get` 返回 None 的情况
    - [使用 Option 的建议](http://twitter.github.com/effectivescala/#Functional programming-Options)
    - Option 的基本接口：

```scala
trait Option[T] {
  def isDefined: Boolean
  def get: T
  def getOrElse(t: T): T

}
```

#### 函数组合子

- [函数组合子](http://stackoverflow.com/questions/7533837/explanation-of-combinators-for-the-working-man)
- `map` - 对列表中每个元素应用一个函数/偏应用函数，返回应用后的元素组成的列表
- `foreach` - 类似于 `map`, 仅用于有副作用的函数，没有返回值
- `filter` - 移除任何对传入函数计算结果为 false 的元素。返回一个布尔值的函数通常称为谓词函数，或判定函数
- `zip` - 将两个列表的内容聚合到一个对偶列表。同 Python 的 zip
- `partition` - 使用给定的谓词函数分割列表
- `find` - 返回集合中第一个匹配谓词的元素
- `drop` - 删除前 i 个元素
- `dropWhile` - 删除元素直到第一个匹配谓词函数的元素
- `foldLeft` - 向左折叠：`numbers.foldLeft(0)((m: Int, n: Int) => m + n)` (0 作为初始值)
- `foldRight` - 向右折叠
- `flatten` - 将嵌套结构扁平化为一个层次的集合
- `flatMap` - 结合了map 与扁平化。需要一个处理嵌套列表的函数，然后将结果扁平化。可以看作先映射后扁平化的快捷操作
- 以上每个函数组合子都可以用 fold 方法实现。

#### Map？

- 所有函数组合子都可以在 Map 上使用。Map 可以看作一个二元组的列表
- 使用模式匹配更优雅地提取键和值：`extensions.filter({case (name, extension) => extension < 200})`


## 模式匹配与函数组合

#### 函数组合

- `compose` - 组合其他函数形成一个新的函数

```scala
def f(s: String) = "f(" + s + ")"
def g(s: String) = "g(" + s + ")"
val fComposes = f _ compose g _  // 将得到 f(g(x))
```

- `andThen` - 先调用第一个函数，再调用第二个函数。`val fAndThenG = f _ andThen g _` 将得到 `g(f(x))`

#### 柯里化 vs 偏应用

> PartialFunction 不同于部分偏应用函数

- case 语句
    - 实质是：`一个名为 PartialFunction 的函数的子类`
    - 多个 case 语句的集合就是：`组合在一起的多个 PartialFunction`
- 对给定的输入参数类型，函数可接受该类型的任何值；但偏函数只能接受该类型的某些特定值。
- `isDefinedAt` - 是 PartialFunction 的一个方法，用来确定偏应用函数是否能接受一个给定的参数
- [PartialFunction 的意见](http://twitter.github.com/effectivescala/#Functional programming-Partial functions)

```scala
scala> val one: PartialFunction[Int, String] = { case 1 => "one"  }
one: PartialFunction[Int,String] = <function1>

scala> one.isDefinedAt(1)
res0: Boolean = true

scala> one.isDefinedAt(2)
res1: Boolean = false
```

- PartialFunction 可用 `orElse` 组成新的函数：`val partial = one orElse two orElse three orElse wildcard`
- 为什么在本该使用函数的地方可以使用 case 语句：
    - PartialFunction 是 Function 的子类，case 语句是 PartialFunction 类的子类

## 基础与多态基础

> 类型系统是一个语法方法，它们根据程序计算的值的种类对程序短语进行分类，通过分类结果错误行为进行自动检查。

- 类型允许你表示函数的定义域和值域
- 一般说来，类型检查只能保证 不合理 的程序不能编译通过。它不能保证每一个合理的程序都 可以 编译通过。
- 所有的类型信息会在编译时被删去，因为它已不再需要。这就是所谓的擦除。
- Scala 类型的主要特性：
    - 泛型 (参数化多态性)
    - (局部)类型推断
    - 存取量化 - 粗略地讲，为一些没有名称的类型进行定义
    - 视窗 - 粗略地讲，就是类型的"强制转换"

### 参数化多态

- 多态性是在不影响静态类型丰富性的前提下，用来（给不同类型的值）编写通用代码的。
- `asInstanceOf[]` - 类型转换。但运行时动态的类型转换可能缺乏安全的保障
- 多态性通过指定类型变量实现, 比如: `def drop1[A](l: List[A]) = l.tail`
- `有秩多态性`: 粗略地说，这意味着在Scala中，有一些想表达的类型概念`过于泛化`以至于编译器无法理解

### 类型推断

- 在函数式编程语言中，类型推断的经典方法是 Hindley Milner 算法，它最早是实现在 ML 中的。
- Scala 类型推断系统的实现,类似于`推断约束`, 并试图统一类型
- 在Scala中所有类型推断是局部的, 一次分析一个表达式

### 变性 Variance (不懂)

- Scala 的类型系统必须同时解释类层次和多态性。类层次结构可以表达子类关系
- 在混合 OO 和多态性时，一个核心问题是：如果 T' 是 T 一个子类，Container[T’] 应该被看做是 Container[T] 的子类吗？

|     | 含义| Scala 标记 |
| :-: | :-: | :-: |
| 协变covariant | C[T']是 C[T] 的子类 | [+T] |
| 逆变contravariant | C[T] 是 C[T']的子类 | [-T] |
| 不变invariant | C[T] 和 C[T']无关 | [T] |

- 如何使用, 不慎懂

### 边界 (不懂)

- Scala 允许通过 `边界` 来限制多态变量。边界 (`<:`) 表达了子类型关系。
- 语法是: `[T <: Type]`

### 量化 (不懂)

- 有时候并不关心是否能够命名一个类型变量: `def count[A](l: List[A]) = l.size`
- 但使用通配符量化可能会使结果难以理解: `def drop1(l: List[_]) = l.tail`
- 但可以为通配符变量应用边界: `def hashcodes(l: Seq[_ <: AnyRef]) = l map (_.hashCode)`

### 视界 ("类型类")

- 一个视界指定一个类型可以被“看作是”另一个类型。这对对象的只读操作是很有用的。
- 视界，就像类型边界，要求对给定的类型存在这样一个函数, 可以使用 `<%` 指定类型限制
- `class Container[A <% Int] { def addIt(x: A) = 123 + x  }` - 表示 A 必须"可被视"为 Int, 即可以不是 Int, 但可以隐式转换为 Int 使用

### 其他类型限制

- Scala 的 math 库对适当类型 T 定义了一个隐含的 `Numeric[T]`, 可以像这样使用: `sum[B >: A](implicit num: Numeric[B]): B`
- 在没有设定陌生的对象为 Numeric 的时候, 方法可能会要求某种特定类型的"证据", 有下列类型-关系运算符:
    - `A=:=B` - A 必须和 B 相等
    - A <:< B - A 必须是 B 的子类
    - A <%< B - A 必须可以被看做是 B

- Scala 标准库中，视图主要用于实现集合的通用函数, 例如 min 函数的实现:

```scala
def min[B >: A](implicit cmp: Ordering[B]): A = {
  if (isEmpty)
    throw new UnsupportedOperationException("empty.min")

  reduceLeft((x, y) => if (cmp.lteq(x, y)) x else y)

}
```

- 上述方法的优点是: 集合中的元素并不必须实现 Ordered 特质, 但 Ordered 的使用仍然可以执行静态类型检查。

### More collections

- `JavaConverters` 包在 Java 和 Scala 集合类型之间进行转换
- Scala 鼓励不变性, 但不惩罚可变性.

### Concurrency in Scala

- `Runnable` 接口只有一个没有返回值的方法
- `Callable` 与之类似，除了它有一个返回值
- Scala 并发是建立在 Java 并发模型基础上的,在 Sun JVM 上，对 IO 密集的任务，可以在一台机器运行成千上万个线程
- 一个线程需要一个 `Runnable`, 必须调用线程的 `start` 方法来运行 `Runnable`

```scala
val hello = new Thread(new Runnable {
    def run(){
        println("Hello world.")
    }
})

hello.start

// another demo
class Handler(socket: Socket) extends Runnable{
    ...

    def run() { ...  }
}
```

- 通过 `Executors` 对象的静态方法得到一个 `ExecutorService` 对象, 如线程池
    - `val pool: ExecutorService = Executors.newFixedThreadPool(poolSize)`
- `Future` 代表异步计算, 将计算包装在 `Future` 中, 当需要结果时, 调用一个阻塞的 `get()` 方法获得.
- 一个 `Executor` 返回一个 `Future`. 一个 `FutureTask` 是一个 Runnable 实现, 被设计为 `Executor` 运行

```scala
val future = new FutureTask[String](new Callable[String](){
  def call(): String = {
    searcher.search(target)
  }
})
executor.execute(future)

val blockingResult = Await.result(future)
```

- 线程安全
    - 同步 - 互斥锁 (mutex) 提供所有权语义. 同步是 JVM 中使用互斥锁最常见的方式. 在 JVM 中, 可以同步任何不为 null 的实例
    - volatile - 与同步基本相同, 但允许空值. `synchronized` 允许更细粒度的锁, `volatile` 对每次访问同步
    - AtomicReference - 低级别的并发原语.

```scala
// synchronized
class Person(var name: String) {
    def set(changedName: String) {
        this.synchronized {
      name = changedName
        }
    }
}

// volatile
class Person(@volatile var name: String) {
    def set(changedName: String) {
    name = changedName
    }
}

// AtomicReference
import java.util.concurrent.atomic.AtomicReference

class Person(val name: AtomicReference[String]) {
    def set(changedName: String) {
    name.set(changedName)
    }
}
```

- AtomicReference 是最昂贵的, 因为必须去通过方法调度 (method dispatch) 来访问值。
- volatile 和 synchronized 是建立在 Java 的内置监视器基础上的。如果没有资源争用，监视器的成本很小。由于 synchronized 允许进行更细粒度的控制权，从而会有更少的争夺，所以 synchronized 往往是最好的选择。
- 当进入同步点，访问 volatile 引用，或去掉 AtomicReferences 引用时，Java 会强制处理器刷新其缓存线从而提供了一致的数据视图。
- `CountDownLatch` 是一个简单的多线程互相通信的机制。这是一个优秀的单元测试。比方说，你正在做一些异步工作，并要确保功能完成。函数只需要 `倒数计数（countDown）` 并在测试中 `等待（await）` 就可以了。
- AtomicInter 和 AtomicLong
- `ReadWriteLocks` - 读写锁, 拥有读线程和写线程的锁控制. 当写线程获取锁的时候读线程只能等待
- 粒度控制 - 一定要试图在互斥锁以外做尽可能多的耗时的工作。如果不存在资源争夺，锁开销就会很小。如果在锁代码块里面做的工作越少，争夺就会越少。
- 这都行! `def this() = this(new mutable.HashMap[String, User] with SynchronizedMap[String, User])`
- SynchronizedMap
- Java 的 ConcurrentHashMap, 线程安全, 通过 `JavaConverters` 获得其 Scala 语义
- 异步计算的一个常见模式是将消费者和生产者分离, 让它们只能通过 `Queue` 沟通
- 并发实例:

```scala
import java.util.concurrent.{BlockingQueue, LinkedBlockingQueue}

// Concrete producer
class Producer[T](path: String, queue: BlockingQueue[T]) extends Runnable {
  def run() {
    Source.fromFile(path, "utf-8").getLines.foreach { line =>
      queue.put(line)
    }
  }
}

// Abstract consumer
abstract class Consumer[T](queue: BlockingQueue[T]) extends Runnable {
  def run() {
    while (true) {
      val item = queue.take()
      consume(item)
    }
  }

  def consume(x: T)
}

val queue = new LinkedBlockingQueue[String]()

// One thread for the producer
val producer = new Producer[String]("users.txt", q)
new Thread(producer).start()

trait UserMaker {
  def makeUser(line: String) = line.split(",") match {
    case Array(name, userid) => User(name, userid.trim().toInt)
  }
}

class IndexerConsumer(index: InvertedIndex, queue: BlockingQueue[String]) extends Consumer[String](queue) with UserMaker {
  def consume(t: String) = index.add(makeUser(t))
}

// Let's pretend we have 8 cores on this machine.
val cores = 8
val pool = Executors.newFixedThreadPool(cores)

// Submit one consumer per core.
for (i <- i to cores) {
  pool.submit(new IndexerConsumer[String](index, q))
}
```

- 在实际代码中使用 `Future` 时，你通常不会调用 `get`，而是使用回调函数。`get` 仅仅是方便在REPL修修补补
- 如果定义一个异步 API，传入一个请求值，API 应该返回一个包装在 `Future` 中的响应。
- 有一个 Future 并且想在异步 API 使用其值, 使用 flatMap: 接受一个Future和一个异步函数，并返回另一个Future. 给定一个 `Future` 成功的值，函数 f 提供下一个 Future. 当输入的 Future 成功完成，flatMap 自动调用f。只有当这两个 Future 都已完成，此操作所代表的 Future 才算完成。
- 如果要在 `Future` 中应用一个同步函数，可以使用 map:

```scala
class RawCredentials(u: String, pw: String){
  val username = u
  val password = pw
}

class Credentials(u: String, pw: String){
  val username = u
  val password = pw
}

def normalize(raw: RawCredentials) = {
  new Credentials(raw.username.toLowerCase(), raw.password)
}

val praw = new Promise[RawCredentials]
val fcred = praw map normalize // apply normalize to future
Await.result(fcred)  // 有问题
praw.setValue(new RawCredentials("Florence", "nightingale"))
Await.result(fcred).username
```

- Scala 可以用 for 表达式快捷地调用 flatMap
- Future 提供了一些`并发组合子`。一般来说，他们都是将 Future 的一个序列转换成包含一个序列的 Future，只是方式略微不同。这是很好的，因为它（本质上）可以把几个 Future 封装成一个单一的 Future。
    - `collect` - 参数是具有相同类型 Future 的一个集合, 返回一个 Future, 其类型是包含类型值的一个序列. 当所有的 Future 都成功完成或者当中任何一个失败，都会使这个 Future 完成。返回序列的顺序和传入序列的顺序相对应。

```scala
val f2 = Future.value(2)
val f3 = Future.value(3)
val f23 = Future.collect(Seq(f2, f3))
val f5 = f23 map (_.sum)
Await.result(f5)
// 5
```

    - `join` - 参数是混合类型的 Future 序列, 返回一个 Future[Unit]. 当所有的相关Future完成时（无论他们是否失败）该Future完成。其作用是标识一组异构操作完成

```scala
val ready = Future.join(Seq(f2, f3))  // Future[Unit]
Await.result(ready)  // doesn't ret value, but futures are done
```

    - `select` - 当传入的 Future 序列的第一个 Future 完成的时候，select 会返回一个 Future. 然后将完成的 Future 和其他未完成的 Future 一起放在 Seq 中返回 (不会做任何事情来取消剩余的 Future.)

