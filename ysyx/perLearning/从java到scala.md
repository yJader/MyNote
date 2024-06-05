# Scala基本语法

## Scala特性

- 支持嵌入式特定语言(embedded DSL)
- 数据结构库
- 严格的类型系统, 进行编译时检查
- 函数式编程

## 安装 scala-cli

简易

```shell
curl -sSLf https://scala-cli.virtuslab.org/get | sh
```

advanced install

```shell
curl -sS "https://virtuslab.github.io/scala-cli-packages/KEY.gpg" | sudo gpg --dearmor  -o /etc/apt/trusted.gpg.d/scala-cli.gpg 2>/dev/null
sudo curl -s --compressed -o /etc/apt/sources.list.d/scala_cli_packages.list "https://virtuslab.github.io/scala-cli-packages/debian/scala_cli_packages.list"
sudo apt update
sudo apt install scala-cli
```



## var & val

`var`: 变量声明

`val`: 常量声明

语句末尾可以没有`;`

变量声明可以不指定类型, 让编译器自行猜测



## Coditionals

`if`可以有返回值, 返回结果为执行分支的最后一句的值

```scala
val likelyCharactersSet = if (alphabet.length == 26)
    "english"
else 
    "not english"

println(likelyCharactersSet)
```

## Methods(Functions)

基本格式

```scala
def methodName(arg1: Type1, arg2: Type2): Type = {
    ...
}

// 单行
// Simple scaling function with an input argument, e.g., times2(3) returns 6
// Curly braces can be omitted for short one-line functions.
def times2(x: Int): Int = 2 * x

// More complicated function
def distance(x: Int, y: Int, returnPositive: Boolean): Int = {
    val xy = x * y
    if (returnPositive) xy.abs else -xy.abs
}

// 嵌套函数
/** Prints a triangle made of "X"s
  * This is another style of comment
  */
def asciiTriangle(rows: Int) {
    
    // This is cute: multiplying "X" makes a string with many copies of "X"
    def printRow(columns: Int): Unit = println("X" * columns)
    
    if(rows > 0) {
        printRow(rows)
        asciiTriangle(rows - 1) // Here is the recursive call
    }
}
```



## Lists

初始化

```scala
val x = 7
val y = 14
val list1 = List(1, 2, 3)
val list2 = x :: y :: y :: Nil       // An alternate notation for assembling a list
```

访问元素

```scala
val third = list1(2)
```



```scala
val headOfList = list1.head          // Gets the first element of the list
val restOfList = list1.tail          // Get a new list with first element removed
```



```scala
val list3 = list1 ++ list2           // Appends the second list to the first list
val m = list2.length
val s = list2.size
```

## for

基本

- 可以将`<-`理解为赋值

```scala
// 0, 1, 2, 3
for(i <- 0 to 3) {print(i + " ")}
println()
// 0, 1, 2 没有3
for (i <- 0 until 3) { print(i + " ") }
println()
// 0, 2, 4, 6
for(i <- 0 to 6 by 2) { print(i + " ") }
println()
```

```scala
val randomList = List(scala.util.Random.nextInt(), scala.util.Random.nextInt(), scala.util.Random.nextInt(), scala.util.Random.nextInt())
var listSum = 0
for (value <- randomList) {
  listSum += value
}
println("sum is " + listSum)
```



## Packages and Imports

```scala
import chisel3._
import chisel3.iotesters.{ChiselFlatSpec, Driver, PeekPokeTester}
```



# Mill

## zsh 插件

[carlosedp/mill-zsh-completions: Zsh plugin adding Scala Mill build tool completions and prompt display (github.com)](https://github.com/carlosedp/mill-zsh-completions?tab=readme-ov-file)



## 依赖中的`::`和`:`

1. **`:`（单冒号）**：

   - 用于指定具体的依赖坐标，包括组织（organization）、模块（module）、版本（version）等。
   - 不会自动添加 Scala 版本号。

   示例：

   ```scala
   ivy"edu.berkeley.cs:chisel3-plugin_2.13.10:3.6.0"
   ```

   这里的 `edu.berkeley.cs:chisel3-plugin_2.13.10:3.6.0` 指定了具体的组织、模块和版本。这种格式适用于所有库，包括那些不依赖 Scala 版本号的库。

2. **`::`（双冒号）**：

   - 用于 Scala 库依赖，会自动添加当前项目的 Scala 版本号。
   - 适用于需要匹配 Scala 版本号的库，例如那些发布在不同 Scala 版本下的库。

   示例：

   ```scala
   ivy"edu.berkeley.cs::chisel3:3.6.0"
   ```

   这里的 `edu.berkeley.cs::chisel3:3.6.0` 中的 `::` 表示 Ivy 会自动在模块名后面添加当前项目的 Scala 版本号（如 `_2.13.10`），变为 `edu.berkeley.cs:chisel3_2.13.10:3.6.0`。



#### bug记录

```xml
<!-- https://mvnrepository.com/artifact/edu.berkeley.cs/chisel3-plugin -->
<dependency org="edu.berkeley.cs" name="chisel3-plugin_2.13.10" rev="3.6.0"/>
```

对于plugin, 需要指定到`2.13.10`中的`.10`, 而`::`只会补全为`_2.13`, 如果使用`::`就会找不到库



## BSP

> [Installation and IDE Support :: Mill (mill-build.com)](https://mill-build.com/mill/Installation_IDE_Support.html#_intellij_idea_support)

```bash
mill mill.bsp.BSP/install
```

**Using Metals**

When using Metals by default Bloop will be used as your build server unless you explicitly choose Mill. When in a Mill workspace use the "**Switch Build Server**" command from Metals which will allow you to switch to **using Mill as your build server**. If no `.bsp/mill-bsp.json` file exists, Metals will automatically create it for you and then connect to Mill.
