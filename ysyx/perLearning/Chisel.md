Chisel: **C**onstructing **H**ardware **I**n a **S**cala **E**mbedded **L**anguage

When you're ready to build your own circuits in Chisel, **we recommend starting from the [Chisel Template](https://github.com/chipsalliance/chisel-template) repository**, which provides a pre-configured project, example design, and testbench. Follow the [chisel-template readme](https://github.com/chipsalliance/chisel-template) to get started.

- 有template了, 我这两天是在干嘛呢

# 语法

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
- `:= `: 一个Chisel的操作符, 表示符号右部drive左部

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

## Combinational Logic

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

