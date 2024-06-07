Chisel: **C**onstructing **H**ardware **I**n a **S**cala **E**mbedded **L**anguage

When you're ready to build your own circuits in Chisel, **we recommend starting from the [Chisel Template](https://github.com/chipsalliance/chisel-template) repository**, which provides a pre-configured project, example design, and testbench. Follow the [chisel-template readme](https://github.com/chipsalliance/chisel-template) to get started.

- 有template了, 我这两天是在干嘛呢

# Chisel语法

## First Module

### basic

```scala
// Chisel Code: Declare a new module definition
class Passthrough extends Module {
  val io = IO(new Bundle {
    val in = Input(UInt(4.W))
    val out = Output(UInt(4.W))
  })
  io.out := io.in
}
```

- 在 Chisel 中，`Module` 是所有硬件组件的基类，代表一个可综合的硬件模块。
- `Bundle`: hardware struct type, 封装了两个值, in和out
  - in和out都是4位宽的无符号整数
- `:= `: 一个**Chisel的特殊操作符**, 表示符号右部drive左部

```scala
// Scala Code: Elaborate our Chisel design by translating it to Verilog
// Don't worry about understanding this code; it is very complicated Scala
println(getVerilog(new Passthrough))

Elaborating design...
Done elaborating.
module Passthrough(
  input        clock,
  input        reset,
  input  [3:0] io_in,
  output [3:0] io_out
);
  assign io_out = io_in; // @[cmd2.sc 6:10]
endmodule
```

### Parameterize

对于这里的`Passthrough`是一个Chisel模块, 也是一个Scala类, 就可以用构造参数来设置io的宽度

这时, `Passthrough`不仅仅代表一个Module, 而是一组由`width`定义的Module

- 将这种`Passthrough`称为==Generator==

```scala
// Chisel Code, but pass in a parameter to set widths of ports
class PassthroughGenerator(width: Int) extends Module { 
  val io = IO(new Bundle {
    val in = Input(UInt(width.W))
    val out = Output(UInt(width.W))
  })
  io.out := io.in
}

// Let's now generate modules with different widths
println(getVerilog(new PassthroughGenerator(10)))
println(getVerilog(new PassthroughGenerator(20)))

Elaborating design...
Done elaborating.
module PassthroughGenerator(
  input        clock,
  input        reset,
  input  [9:0] io_in,
  output [9:0] io_out
);
  assign io_out = io_in; // @[cmd4.sc 6:10]
endmodule

Elaborating design...
Done elaborating.
module PassthroughGenerator(
  input         clock,
  input         reset,
  input  [19:0] io_in,
  output [19:0] io_out
);
  assign io_out = io_in; // @[cmd4.sc 6:10]
endmodule

```

### 测试

```scala
// Scala Code: `test` runs the unit test. 
// test takes a user Module and has a code block that applies pokes and expects to the 
// circuit under test (c)
test(new Passthrough()) { c =>
    c.io.in.poke(0.U)     // Set our input to value 0
    c.io.out.expect(0.U)  // Assert that the output correctly has 0
    c.io.in.poke(1.U)     // Set our input to value 1
    c.io.out.expect(1.U)  // Assert that the output correctly has 1
    c.io.in.poke(2.U)     // Set our input to value 2
    c.io.out.expect(2.U)  // Assert that the output correctly has 2
}
println("SUCCESS!!") // Scala Code: if we get here, our tests passed!
```

- `test(new Passthrought())`: test结构一个参数, 为传入的电路模块的实例
  - `c =>`表示传递给test方法的电路模块的实例
  - 使用ChiselSim, 则改`test`为`simulate`
- `poke`和`expect`是Chisel中用于测试硬件电路的方法。
  - `poke`用于设置电路的输入
  - `expect`用于验证电路的输出是否符合预期
- `.U`是一个构造函数，用于创建无符号数 (来自Chisel库定义)



## Chisel数据类型

`UInt`, `SInt`, `Bool`

可以将scala的数值类型转换为chisel的类型:

```scala
123.U(4.W) // 如果没有指定位宽, 会自动进行推断
0xff.U(8.W)
"b10001000".U(8.W) // 0b10001000似乎不行
```

- 注意, chisel数值的比较必须用chisel定义的`===`, 直接使用`==`比较的是对象, 无法相等

## Combinational Logic

### 基本Chisel 类型

`UInt`, `n.U`: unsigned integer;

`SInt`, `n.S`: signed integer;

`Bool`, `true.B`or`false.B`:  true or false may be connected and operated upon

### Common Operators

```scala
class MyModule extends Module {
  val io = IO(new Bundle {
    val in  = Input(UInt(4.W))
    val out = Output(UInt(4.W))
  })
}

class MyModule extends Module {
  val io = IO(new Bundle {
    val in  = Input(UInt(4.W))
    val out = Output(UInt(4.W))
  })

  val two  = 1 + 1 
  println(two)			// output 2
  val utwo = 1.U + 1.U
  println(utwo) 		// UInt<1>(OpResult in MyModule)
  
  io.out := io.in
}
println(getVerilog(new MyModule))


```

上面的two是Scala的`Int`类, 而 utwo是Chisel的`UInt`类

- `1.U`会进行类型转换
- `1.U + 1`是不合法的



`Mux()`: 可以理解为三元运算符`bool ? a : b` 

- Multiplexer: 多路选择器

`Cat()`: 连接两个数

`+&`: 加的结果拓展一位(用来看溢出)



更多的操作详见 cheatsheet

- 吐槽 这个格式做的有点丑啊 有空学学给他重做一下







## 控制流

### when

```scala
when(){
    
}
elsewhen() {
    
}
otherwise{
    
}
```

- 与scala的if else不同, 不会返回值

### Wire

一种类型, 描述硬件的结构, 与前面用到的`IO`, 还没用到的`Reg`同类

- 属于组合逻辑, 对应为硬件电路上的连线 



## Sequential Logic

> You can't write any meaningful digital logic without state.

### Register–`Reg`

Reg存放一个值, 直到下一个上升沿再输出 (在此之前, 输出保持上一次的值)

- `Module`内含时钟clock, 通过c.clock来访问
  - `c.clock.step(n)`时钟前进n个周期

**`Reg`初始化** 

```scala
val myReg = Reg(UInt(2.W)) // 指定位宽和类型
```

**`RegNext` 一个简单的Register对象**

```scala
class RegNextModule extends Module {
  val io = IO(new Bundle {
    val in  = Input(UInt(12.W))
    val out = Output(UInt(12.W))
  })
  
  // register bitwidth is inferred from io.out
  io.out := RegNext(io.in + 1.U)
  // 相当于
  val register = Reg(UInt(12.W))
  register := io.in + 1.U
  io.out := register
}
```

- 在下一个时钟周期, 将当前的io.in + 1.U输出给io.out



**`RegInit`方法**

```scala
val myReg = RegInit(UInt(12.W), 0.U)
val myReg = RegInit(0.U(12.W))
```

- 可以在定义一个寄存器的同时赋初值

**显示定义类型**

> 来自scala的语法

```scala
val reg: UInt = Reg(UInt(4.W))
```

- 表示reg是一个UInt类型的常量



## 例 FIRFilter

```scala
class My4ElementFir(b0: Int, b1: Int, b2: Int, b3: Int) extends Module {
  val io = IO(new Bundle {
    val in = Input(UInt(8.W))
    val out = Output(UInt(8.W))
  })

  // 移动和延迟信号
  // val in0 = RegNext(io.in)
  val in1 = RegNext(io.in) // 这里左边是常量, 而不是硬件, 要用 = 而不是:=
  val in2 = RegNext(in1)
  val in3 = RegNext(in2)

  // 计算滤波器输出, 即进行卷积元计算
  io.out := (io.in * b0.U) + (in1 * b1.U) + (in2 * b2.U) + (in3 * b3.U)
}
```



## Chisel Test

[ChiselSim](# ChiselSim)

# Scala语法

## 泛型

泛型类

```scala
class Box[T](val content: T)

val intBox = new Box 
val stringBox = new Box[String]("hello")
```

泛型方法

```scala
def getFirst[T](items: List[T]): T = items.head

val firstInt = getFirst(List(1, 2, 3))  // T 是 Int
val firstString = getFirst(List("a", "b", "c"))  // T 是 String
```

### 泛型约束

**上界`<:`**

```scala
class Animal
class Dog extends Animal

class Cage[T <: Animal](val animal: T)

// Chisel
class AliasedBundle[T <: Data](gen: T) extends Bundle {
  val foo = gen
  val bar = gen
}
```

- 用`T <: Animal`表示T必须是Animal的子类
- Data: Chisel所有类型的(抽象?忘记了)父类



**下界`:>`**

表示类型参数必须是某个类型的超类型，用 `>:` 表示。

```scala
def printAnimalName[T >: Dog](animal: T): Unit = {
  println(animal.toString)
}
```



**协变和逆变**





# 框架

## ChiselTest → ChiselSim

[Migrating from ChiselTest | Chisel (chisel-lang.org)](https://www.chisel-lang.org/docs/appendix/migrating-from-chiseltest)

[Versioning | Chisel (chisel-lang.org)](https://www.chisel-lang.org/docs/appendix/versioning)

> With the release of Chisel 5, Chisel moved off of the legacy [Scala FIRRTL Compiler (SFC)](https://github.com/chipsalliance/firrtl) to the MLIR FIRRTL Compiler (MFC), part of the [llvm/circt](https://github.com/llvm/circt) project.

根据文档, Chisel从3.6.0开始更换了编译器, 但是ChiselTest使用的是SFC, 不好与使用MFC的ChiselTest兼容

现在应该使用Chisel包内自带的ChiselSim(不用额外引依赖了 好耶)

**例**

```scala
import chisel3._
class MyModule extends Module {
  val io = IO(new Bundle {
    val in = Input(UInt(16.W))
    val out = Output(UInt(16.W))
  })

  io.out := RegNext(io.in)
}
```

**原ChiselTest**

```scala
import chisel3._
import chiseltest._
import org.scalatest.flatspec.AnyFlatSpec

class MyModuleSpec extends AnyFlatSpec with ChiselScalatestTester {
  behavior of "MyModule"
  it should "do something" in {
    test(new MyModule) { c =>
      c.io.in.poke(0.U)
      c.clock.step()
      c.io.out.expect(0.U)
      c.io.in.poke(42.U)
      c.clock.step()
      c.io.out.expect(42.U)
      println("Last output value : " + c.io.out.peek().litValue)
    }
  }
}
```

**迁移为ChiselSim**

```scala
import chisel3._
import chisel3.simulator.EphemeralSimulator._
import org.scalatest.flatspec.AnyFlatSpec

class MyModuleSpec extends AnyFlatSpec {
  behavior of "MyModule"
  it should "do something" in {
    simulate(new MyModule) { c =>
      c.io.in.poke(0.U)
      c.clock.step()
      c.io.out.expect(0.U)
      c.io.in.poke(42.U)
      c.clock.step()
      c.io.out.expect(42.U)
      println("Last output value : " + c.io.out.peek().litValue)
    }
  }
}
```



## ChiselSim

> Module的本质是一个封装的电路模块, 所以test只能对外部接口进行检查
>
> 即只能访问`Input`, `Output`, 和内置的`clock`

`poke(激励值)`: 给相应的端口添加想要的激励值

`peek()`: 返回端口的当前值

`expect(期望值)`: 检查端口值是否与期望值相同

`step(n)`: 前进n个时钟周期

- 或许应该说是`clock.step(n)`, 只有clock能step



### Modules with Decoupled Interfaces

```scala
class QueueModule[T <: Data](ioType: T, entries: Int) extends MultiIOModule {
  val in = IO(Flipped(Decoupled(ioType)))
  val out = IO(Decoupled(ioType))
  out <> Queue(in, entries)
}
```

**参数解释**

- `T <: Data`: 这是一个类型参数，表示 `ioType` 必须是 `Data` 的子类型。Chisel 的所有硬件数据类型都是 `Data` 的子类型，如 `UInt`、`SInt`、`Bool` 等。
- `ioType`: 输入和输出接口的数据类型。
- `entries`: 队列的深度，即队列中可以容纳的元素数量。

**使用 `Queue` 模块**

- `Queue(in, entries)`: 创建了一个带有 `entries` 个元素的队列，队列的输入接口连接到 `in`。
- `out <> Queue(in, entries)`: 使用 Chisel 中的连接操作符 `<>` 将 `out` 接口与队列的输出接口连接起来。`<>` 操作符表示双向连接，即将 `out` 和队列的输出信号（`valid`、`ready` 和 `bits`）相互连接。



## mill命令行参数

### 单元测试

```bash
# 运行某个project的所有测试
mill project_name.test
# 运行project下的某个测试类
mill project_name.test.testOnly project_name.class_name
```



### stack trace

> [scala - How can I get complete stacktraces for exceptions thrown in tests when using sbt and testng? - Stack Overflow](https://stackoverflow.com/questions/7237824/how-can-i-get-complete-stacktraces-for-exceptions-thrown-in-tests-when-using-sbt)

```
D - show durations
S - show short stack traces
F - show full stack traces
W - without color
```

like that

```bash
mill coder.test -oF 
```



# 实验实现

## 1. 选择器

### MuxLookup函数

### `MuxLookup` 语法

```scala
MuxLookup(key: UInt, default: Data, mapping: Seq[(UInt, Data)]): Data
```

- **key**: `UInt` 类型，表示选择信号。
- **default**: `Data` 类型，当 `key` 不匹配任何 `mapping` 中的值时，返回的默认值。
- **mapping**: 一个序列，包含一组键值对 `(UInt, Data)`，表示 `key` 对应的输出值。
