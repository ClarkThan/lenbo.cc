---
layout:          post
title:           OCaml函数式编程 - 第2章
description:     Cornell大学 CS 3110 课程教材 Functional Programming in OCaml 翻译
date:            2020-03-04 23:54:52 +08:00
author:          "Clark Than"
category:        PL
tags:            OCaml PL
---


# The Basics of OCaml

本章将介绍 OCaml 的一些基本特性。 但是，在我们深入学习OCaml之前，让我们先谈一个更大的想法：学习语言一般套路。

本课程的其中一个次要目标是不仅让你学习一种新的编程语言，还使你提高学习 *如何学习* 新语言的技能。

学习一门编程语言有五个基本组成部分：语法，语义，习惯用法，库和工具。

## 语法

*语法 *是指使用形式优良的文本来构成程序的规则，包括关键字，对空格和格式的限制，标点，运算符等。学习一种门新语言的一个更烦人的方面可能是: 相比于你已经知道的语言, 新语言的语法显得更奇怪。 但是，随着你学习的语言越多，就会越习惯接受该语言的语法就是这样而不是希望它有所不同。 （如果你想查看某些语法不寻常的语言，请看一下 [APL](http://tryapl.org/)，它需要自己的扩展键盘，以及 Whitespace，其中的程序完全由空格，制表符和换行符组成。）你需要理解语法，以便 完全可以和电脑说话。

## 语义

You need to understand semantics to say what you mean to the computer, and you need to say what you mean so that your program performs the right computation.*语义*是指定义程序行为的规则。 换句话说，语义是关于程序的含义的--特定语法段表示特定的某种计算。 语义有两部分，*动态*语义和*静态*语义。 动态语义定义了程序在执行或求值计算时的运行时行为。 静态语义定义了编译时检查，以确保程序合法，超出了任何语法要求。 最重要的一种静态语义可能是*类型检查(type checking)*：定义程序是否是well typed的规则。 学习新语言的语义通常才是真正的挑战，即使语法可能是你必须克服的第一个障碍。 你需要了解语义以传达你想要对计算机表达的含义，还需要表达出期望程序正确执行所要表达的含义。

## 习惯用法

*习惯用法*是指使用编程语言特性来表达计算的常用方法。 假设你可以在一种语言中以多种方式表达同一种计算，那么你会选择哪种呢？ 有一些方式会比其他的更自然。 精通该语言的程序员偏好某些表达方式。 我们可以通过在语言中有效地使用主导范式（无论它们是命令式, 函数式, 面向对象等）来思考这一问题。 你需要理解习惯用法，以传达出不仅是对计算机还有其他程序员所要表达的含义。 当你以惯用的方式编写代码时，其他程序员将更好地理解你的代码。

## 库

much of which you would be unable to code up in the language yourself, such as file I/O.*库*是为你编写的代码束，可以使你成为更有效率的程序员，因为你不必重复造轮子。 （有人说 [懒惰是程序员的一种美德](http://threevirtues.com/)。）学习一种新语言的一部分是发现可用的库以及如何使用好它们。 一种语言通常提供一个*标准库*，使你可以访问一组核心功能(其中许多都是如果缺少你将无法使用该语言进行编码，例如文件 I/O)。

## 工具

最基本的任何一种语言实现都提供编译器或解释器作为使用该语言与计算机进行交互的工具。 但是还有其他一些工具：调试器； 集成开发环境（IDE）； 和用于性能，内存使用和正确性的分析工具。 学习使用与语言相关的工具也可以使你成为更有效率的程序员。 有时很容易将工具本身与语言混淆。 例如，如果你仅仅同时使用过 Eclipse 和Java，那么可能不太清楚 Eclipse 原来是可以使用多种语言的 IDE，并且可以在没有 Eclipse 的情况下使用 Java。

在本课程中学习  OCaml 时，我们的重点主要放在语义和习惯用法上。 当然，我们将不得不同时学习语法，但这并不是我们学习中有趣的部分。 我们将介绍 OCaml 标准库和其他两个库，尤其是 OUnit（类似于 JUnit，HUnit 等的单元测试框架）。 除了OCaml 编译器和构建系统之外，我们将使用的主要工具是 toplevel，它提供了交互地进行代码实验的功能。



# 2.1. Interacting with OCaml

*toplevel* 就像是 OCaml 的计算器或命令行界面。 类似于你在CS 2110中使用过的 DrJava；或者在 CS 1110中使用过的 交互式Python 解释器。对于尝试一小段代码很方便, 无需启动 OCaml 编译器。 但是不要太依赖它，因为创建，编译和测试大型程序将需要更强大的工具。 其他一些语言会将 toplevel 称为 REPL，它代表 read-eval-print-loop：它读取程序员的输入，对其进行求值，打印结果，然后重复执行。

在终端窗口中，键入 `utop` 以启动 toplevel。 按 Control-D 退出 toplevel。 你也可以输入 `#quit;;` 然后按回车键。 请注意，你必须在此处键入 `#` ：它是你已经看到的 `#` 提示的补充。

## 类型和值

你可以在 OCaml toplevel 输入表达式，用双分号`;;` 结束表达式 然后按回车键。 然后，OCaml 将对表达式求值，告诉你结果值以及值的类型。 例如：

```ocaml
# 42;;
- : int = 42
```

让我们从 utop 剖析该输出响应，从右到左阅读：

- `42` 是值
- `int` 是值的类型
- 该值未指定名称，因此使用符号 `-`

你可以使用 `let` 定义将值绑定到名称, 如下所示:

```ocaml
# let x = 42;;
val x : int = 42
```

同样，让我们剖析该输出响应，这次从左至右阅读：

- 值绑定到名称，因此使用val关键字
- `x` 是值绑定的名称
- `int` 是值的类型
- `42` 是值

你可以将整个输出说成 “ x的类型为int且值等于42”。

## 函数

可以使用以下语法在 toplevel 定义函数：

```ocaml
# let increment x = x+1;;
val increment : int -> int = <fun>
```

让我们剖析该输出响应：

- `increment` 是值绑定的标识符
- `int -> int` 是值的类型。 这是将 `int` 作为输入并产生 `int` 作为输出的函数的类型。 将箭头 `->` 视为一种将一个值转换为另一个值的可视化隐喻，这就是函数的作用。
- 该值是一个函数，toplevel 选择不打印该函数（因为它现在已经被编译，并且在内存中具有一种表示形式，因此不容易进行漂亮的打印）。 而是在 toplevel 打印的只是一个占位符，以表明存在一些不可打印的函数值。 **重要提示：函数本身不是值。**

你可以使用以下语法“调用”函数：

```ocaml
# increment 0;;
- : int = 1
# increment(21);;
- : int = 22
# increment (increment 5);;
- : int = 7
```

但是在OCaml 中，通常的词汇是我们“应用”函数而不是“调用”函数。

请注意，OCaml 在是否编写括号以及是否编写空格方面非常灵活。 首次学习 OCaml 的挑战之一可能是弄清楚何时真的需要括号。 因此，如果发现语法错误，一种策略是尝试添加一些括号。

## 文件中保存代码

将 OCaml 用作一种交互式计算器可能会很有趣，但是用这种方式编写大型程序走不了太远。 我们需要将代码存储在文件中。

打开终端并使用文本编辑器创建一个名为  `hello.ml` 的文件。 在文件中输入以下代码：

```ocaml
let _ = print_endline "Hello world!"
```

**重要说明：在该行代码的末尾没有双分号 `;;`。** 双分号严格用于  toplevel 的交互式会话，因此  toplevel 知道你已经输入了一段代码。 没有理由将其写入.ml文件，并且我们认为这是一种不好的风格。

上面的 `let _ =` 表示我们不需要给 `=` 右侧的代码绑定名称（因此为“空白”或下划线）。

保存文件, 回到命令行, 编译代码:

```shell
$ ocamlc -o hello.byte hello.ml
```

编译器名为 `ocamlc`。 `-o hello.byte` 选项是说要给输出的可执行文件命名为`hello.byte`。 可执行文件包含编译过的 OCaml 字节码。 此外，还会生成另外两个文件 `hello.cmi` 和  `hello.cmo`。 现在我们不需要关心那些文件。 运行可执行文件：

```shell
$ ./hello.byte
```

它应该打印 `Hello world!` 然后终止

现在，将打印的字符串更改为你选择的字符串。 保存文件，重新编译，然后重新运行。 尝试使代码打印多行。

如果你习惯于在Eclipse之类的IDE中进行操作，那么在编辑器和命令行之间的这种 edit-compile-run 循环可能会让你感到陌生。 不用担心 它将很快成为第二天性。

直接运行编译器很容易知道如何做，但是在较大的项目中，我们希望使用 OCaml 构建系统来自动查找和链接库。 让我们尝试使用它：

```shell
$ ocamlbuild hello.byte
```

你将从该命令中得到一个错误。 不用担心 继续阅读本练习。

The build system will automatically figure out that `hello.ml` is the source code for that desired bytecode.该构建系统名为 `ocamlbuild`。 我们要构建的文件是已编译的字节码  `hello.byte`。 构建系统将自动确定 `hello.ml` 将是该字节码所需要的源代码文件。

但是，构建系统喜欢负责整个编译过程。 当它看到直接调用编译器生成的剩余文件时，就像我们在上一个练习中所做的那样，它当然会感到不安并且拒绝继续处理。 如果查看错误消息，则表明已生成了一个脚本用来清除之前的编译。 运行该脚本，然后删除编译的文件：

```shell
$ _build/sanitize.sh
$ rm hello.byte
```

之后，请尝试再次构建：

```shell
$ ocamlbuild hello.byte
```

现在应该成功了。 将创建一个目录 `_build` ； 它包含所有已编译的代码。 与直接运行编译器相比，这是构建系统的一个优点：与其直接让一系列生成的文件污染源目录，还可以在一个单独的目录中干净地创建它们。 还创建了一个文件 `hello.byte` ，它实际上只是指向该名称“真实”文件的链接，该文件位于 `_build` 目录中。

现在运行可执行文件：

```shell
$ ./hello.byte
```

现在，你可以轻松清理所有已编译的代码：

```shell
$ ocamlbuild -clean
```

这将删除 `_build` 目录和 `hello.byte` 链接，仅保留你的源代码。

## Main函数呢?

与 C 或 Java 不同，OCaml 程序不需要具有名为 `main` 的特殊函数即可启动该程序。 通常的习惯用法是将文件中的最后一个定义用作开始执行任何其他计算之前的 `main` 函数。

## 在toplevel里加载代码

除了允许你定义函数外，toplevel 还将接受不是 OCaml 代码的*指令*(告诉 toplevel 本身进行某些操作)。 所有指令均以 `#` 字符开头。 也许最常见的指令是 `#use`，它会将文件中的所有代码加载到 toplevel，就像你已将该文件中的代码键入到 toplevel 一样。

例如，假设你创建一个名为 `mycode.ml` 的文件。
在该文件中放入以下代码：

```ocaml
let inc x = x + 1
```

启动 toplevel,  尝试输入以下表达式，然后观察错误：

```ocaml
# inc 3;;
Error: Unbound value inc
Hint: Did you mean incr?
```

该错误是因为 toplevel 尚不了解名为 `inc` 的函数。 现在向 toplevel 发出以下指令：

```ocaml
# #use "mycode.ml";;
```

请注意，上面的第一个 `#` 字符向你表明了 toplevel prompt。 第二个 `#` 字符是你键入的字符，用于告诉 toplevel 你正在发出指令。 没有该字符，toplevel 将认为你正在尝试应用名为 `use` 的函数。

现在再试一次：

```ocaml
# inc 3;;
- : int = 4
```

## toplevel工作流程

用存储在文件中的代码和 toplevel 打交道时，最佳的工作流程是：

- 编辑文件中的代码
- 在 toplevel 中使用 `#use` 加载代码
- 交互式试验代码
- 退出 toplevel. **警告:** 不要跳过这一步

假设你想修复代码中的错误：很想不退出 toplevel，编辑文件，然后重新发出 `#use` 指令到同一 toplevel 会话中。 抵制这种诱惑。 在同一会话中从较早的 `#use` 指令加载的 "stale code" 可能会导致令人惊讶的事情发生—无论如何，当你第一次学习该语言时会感到惊讶。 因此，**在重用文件之前，请始终退出 toplevel**。



# 2.2. Expressions

OCaml 语法的基本部分是表达式。 就像命令式语言中的程序主要是由命令构建而成的，函数式语言中的程序主要是由表达式构建的。 表达式的示例包括 `2+2` 和  `increment 21`。

OCaml 手册对语言中的[所有表达式](http://caml.inria.fr/pub/docs/manual-ocaml/expr.html)都有完整的定义。 尽管该页面的开头是一个比较隐晦的概述，但如果向下滚动，则会出现一些英文说明。 不用现在就急着去学习该页面。 只要知道它是可用参考手册就好。

函数式语言中的计算的主要任务是表达式求值。 值就是已经没有计算(computation)需要执行的表达式。 因此，所有值都是表达式，但并非所有表达式都是值。 值的示例包括 `2`, `true`, 和 `"yay!"`.。

OCaml 手册也对[所有值](http://caml.inria.fr/pub/docs/manual-ocaml/values.html)进行了定义，尽管如此，该页面还是最有用的参考而非研究。

有时，表达式可能无法求值。 可能有两个产生原因：

1. 会引发异常的表达式求值。
2. 永远不会终止的表达式求值（例如，它进入“无限循环”）。

## 断言

表达式 `assert e` 会对 `e` 求值。 如果结果为 `true`，则什么也不会发生，并且整个表达式将求值为一个称为 *unit* 的特殊值。 这个特殊的值写作 `()` ，其类型为 unit。 但是，如果结果为  `false`，则会引发异常。



# 2.2.1. Operators

运算符可用于构成表达式。 OCaml 多少有一些你在 C 或 Java 类语言中常用的运算符。 有关详细信息，请参见[OCaml 运算符表格手册](http://caml.inria.fr/pub/docs/manual-ocaml/expr.html#sec139) 。

开始时，请注意以下两点：

- OCaml 故意不支持运算符重载。 因此，整数和浮点数的运算符是不同的。 例如，要整数相加使用 `+`, 浮点数相加则使用 `+.`。
- OCaml 中有两个相等运算符， `=` 和 `==`，以及对应的不相等运算符 `<>` 和 `!=`。 运算符 `=` 和 `<>` 用于检查结构( *structural* )相等性，而 `==` 和 `!=` 检查物理( *physical* 可理解为内存相等性)相等性。 在我们理解 OCaml 的命令式特性之前，很难解释它们之间的区别。 （如果你现在好奇，请参阅 [Stdlib.(==)文档](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Stdlib.html)。 但是现在重要的是，你需要保持自己只使用 `=` 而不使用 `==`，如果你之前是写像 Java 之类的语言的话，则可能会有些困难。 那些语言通常使用 `==` 作为相等运算符。



# 2.2.2. If Expressions

如果其中的 `e1` 求值为 `true` 则整个表达式的求值为 `e2` 的值，否则为 `e3` 的值。 我们称 `e1` 为 if 表达式的 *guard*。

```ocaml
# if 3 + 5 > 2 then "yay!" else "boo!";;
- : string = "yay!"
```

与你在命令式语言中使用过的 if-then-else 语句不同，OCaml 中的 if-then-else 表达式与其他任何表达式一样。 它们可以放在表达式可以放置的任何地方。 这使得它们类似于有些语言中的三元运算符 `? :` 。

```ocaml
# 4 + (if 'a' = 'b' then 1 else 2);;
- : int = 6
```

If 表达式可以舒适地嵌套:

```ocaml
if e1 then e2
else if e3 then e4
else if e5 then e6
...
else en
```

无论你要编写单个 if 表达式还是高度嵌套的 if 表达式，最后的 else 部分都应该视为强制性的。 如果不提供，则很可能会收到一条报错消息(目前你可能还无法理解该错误消息)：

```ocaml
# if 2>3 then 5;;
Error: This expression has type int but an expression was expected of type unit
```

**语法.**  if 表达式的语法如下:

```ocaml
if e1 then e2 else e3
```

字母 `e` 在这里用于表示任何其他 OCaml 表达式, 它只是 *syntactic variable* 语法变量（又称 *metavariable* 元变量）的一个示例，它实际上不是 OCaml 语言本身的变量，而是某种语法构造的名称。 字母 `e` 之后的数字用于区分这里的三个不同出现。

**动态语义.**   if 表达式的动态语义:

- 如果 `e1` 求值为 `true`, 同时 `e2` 求值为 `v`, 那么 `if e1 then e2 else e3` 整个表达式的求值就是 `v`
- 如果 `e1` 求值为 `false`, 同时 `e3` 求值为 `v`, 那么 `if e1 then e2 else e3` 整个表达式的求值就是 `v`

我们称这些 *求值* 规则为：它们定义了如何求值表达式。 请注意，它是怎么样用两个规则来描述 if 表达式求值的，一个用于保护条件为 true 的规则，一个用于保护条件为 false 的规则。 字母 `v` 在这里用来表示任何 OCaml 值； 这又是一个元变量的例子。 在本学期晚些时候，我们将开发一种更数学的方式来表达动态语义，但是现在我们将继续采用这种非正式的解释方式。

**静态语义.** if 表达式的静态语义:

- 如果 `e1` 的类型是 `bool`, `e2` 的类型是 `t` 同时 `e3` 的类型也是 `t`, 那么 `if e1 then e2 else e3` 整个表达式的类型就是 `t`

我们称此为类型规则(*typing rule*)：它描述了如何对表达式进行类型检查。 注意它是如何只用一条规则来描述 if 表达式的类型检查的。 在编译时，类型检查完成后，guard 是否为 true 还是 false 无关紧要； 实际上，编译器无法知道 guard 在运行时具体会是什么值。 这里的字母 `t` 用于表示任何 OCaml 类型； OCaml 手册有[所有类型](http://caml.inria.fr/pub/docs/manual-ocaml/types.html)的定义（奇怪的是，它没有命名语言的基本类型，如int和bool）。

我们将大量编写“具有类型”，因此让我们为它引入一个更紧凑的表示法。 每当我们写“ `e` 的类型为 `t` ”时，我们都改写为 `e：t`。 冒号读作为“具有...类型”。 冒号的这种用法与 在 toplevel 输入表达式后求值的输出响应是一致的：

```ocaml
# let x = 42;;
val x : int = 42
```

在上面的示例中，变量 `x` 的类型为 `int`，即冒号指示的类型。



# 2.2.3. Let Expressions

到目前为止，在使用 `let` 一词时，我们一直在 toplevel 和 `.ml` 文件中进行定义。 例如，

```ocaml
# let x = 42;;
val x : int = 42
```

在 toplevel 里，将 `x` 定义为42之后, 我们就可以在后续的定义中使用 `x` 了。 我们称这种方式为 *let 定义*。

`let` 的另一种用法是作为表达式：

```ocaml
# let x = 42 in x+1
- : int = 43
```

在这里，我们将值绑定到名称 `x`，然后在另一个表达式 `x + 1` 中使用该绑定。 我们称这种使用 `let` 的方式为 *let 表达式*。 因为它是一个表达式，所以它可以计算得到一个值。 这是与 *let 定义* (不会执行求值)不同的地方。 如果尝试将 *let 定义* 放在期望是表达式的位置时, 你可以看到：

```ocaml
# (let x = 42) + 1
Error: Syntax error: operator expected.
```

语法上，不允许在 `+` 运算符的左侧使用 *let 定义*，因为那里需要一个值，而定义不会求值。 相反，一个 *let 表达式* 可以很好地工作：

```ocaml
# (let x = 42 in x) + 1
- : int = 43
```

在 toplevel 理解 *let 定义* 的另一种方法是，它们就像 *let 表达式* 一样，只是我们还没有提供它的 body 表达式 部分。 隐式地，body 表达式 部分是我们将来会输入的任何其他内容。 例如，

```ocaml
# let a = "big";;
# let b = "red";;
# let c = a^b;;
# ...
```

将会被 OCaml 理解为如下等价的形式

```ocaml
let a = "big" in
let b = "red" in
let c = a^b in
...
```

后面的这一系列 *let 绑定* 是一种在给定代码块内绑定多个变量的习惯用法。

**语法.**

```ocaml
let x = e1 in e2
```

通常，`x` 是一个标识符。 我们将 `e1` 称为绑定表达式，因为它就是绑定到 `x` 的内容； 我们将 `e2` 称为 *body expression* (主体表达式)，因为这就是那些绑定的有效范围所在的代码主体。

**动态语义.**

对 `let x = e1 in e2` 表达式求值:

- 求值 `e1` 得到 `v1`.
- 用 `v1` 替换掉 `e2` 中的 `x`,  得到一个新的表达式 `e2'`.
- 对 `e2'` 求值得到 `v2`.
- 则整个 let 表达式的求值结果就是 `v2`.

这里有一个例子:

```ocaml
    let x = 1+4 in x*3
-->   (evaluate e1 to a value v1)
    let x = 5 in x*3
-->   (substitute v1 for x in e2, yielding e2')
    5*3
-->   (evaluate e2' to v2)
    15
      (result of evaluation is v2)
```

**静态语义.**

- 如果 `e1: t1`, 并且在 `x: t1` 的条件下满足 `e2: t2`, 则 `(let x = e1 in e2): t2`

为了清楚起见，我们使用上面的括号。 像往常一样，编译器的类型推断器确定变量的类型是什么，或者程序员可以使用以下语法对其类型进行显式标注：

```ocaml
let x : t = e1 in e2
```





# 2.2.4. Scope

let 绑定仅在发生绑定的代码块中有效。 这几乎是你使用几乎所有现代编程语言所惯用的。 例如：

```ocaml
let x=42 in
  (* y is not meaningful here *)
  x + (let y="3110" in
         (* y is meaningful here *)
         int_of_string y)
```

变量的 *scope* (作用范围/作用域) 是其名称有意义的地方的范围。 变量 `y` 的作用域仅仅是: y 发生绑定的那个 let 表达式内部。

可能会出现同一名称的重叠绑定。 例如：

```ocaml
let x = 5 in
  ((let x = 6 in x) + x)
```

但是，这真是令人困惑，因此，再次非常低不鼓励这种风格 — 就像不鼓励自然语言中的有歧义的代词一样。 尽管如此，让我们考虑一下代码的含义。

该代码会求值达到什么？ 答案归结为 `x` 每次出现时如何用值替换 `x`。 这里有集中可能的替代方法：

```ocaml
(* possibility 1 *)
let x = 5 in
  ((let x = 6 in 6) + 5)

(* possibility 2 *)
let x = 5 in
  ((let x = 6 in 5) + 5)

(* possibility 3 *)
let x = 5 in
  ((let x = 6 in 6) + 6)
```

第一个是几乎所有合理的语言都会做的事情。 而且很可能正是你所猜测的，但是，为什么是这样呢？

答案就是我们称之为*名称无关性*的原则：变量的名称本质上不重要。 数学角度上你已经习惯了这样: 例如，这两个函数是相同的：$f(x) = x^2$ ,  $f(y) = y^2$

我们将参数称为 *x* 还是 *y *本质上无关紧要。 无论哪种方式，它都是是平方函数。 因此，在程序中，这两个函数应该是相同的：

```ocaml
let f x = x*x
let f y = y*y
```

the two functions are equivalent up to renaming of variables, which is also called *alpha conversion*, for historical reasons that are unimportant here.此原理更加被人所熟知的名称是 *alpha equivalence* (alpha 等效)：由于变量重命名，这两个函数是等效的。这又被称作  *alpha conversion*, 由于历史原因，此处不重要。

根据*不相关名称*原则，这两个表达式应该相同：

```ocaml
let x = 6 in x
let y = 6 in y
```

因此，下面两个表达式（其中嵌入了上面的表达式）也应该相同：

```ocaml
let x = 5 in (let x = 6 in x) + x
let x = 5 in (let y = 6 in y) + x
```

但是要使它们相同，我们必须从以上三种可能性中选择可能性1。 这是唯一使变量名称不相关的方式。

有一种通常用于此现象的术语：变量的新绑定会遮盖变量名称的所有旧绑定。 隐喻地，好像新的绑定临时在旧的绑定上蒙上了阴影。 但最终，随着阴影的消退，旧的绑定可以重新出现。

阴影不是可变赋值。 例如，以下两个表达式的计算结果均为11：

```ocaml
let x = 5 in ((let x = 6 in x) + x)
let x = 5 in (x + (let x = 6 in x))
```

同样，以下 utop 脚本不是可变赋值，尽管起初看起来像是：

```ocaml
# let x = 42;;
val x : int = 42
# let x = 22;;
val x : int = 22
```

回想一下，顶层中的每个 let 定义 实际上都是一个嵌套的let表达式。 因此，以上内容实际上是如下这样：

```ocaml
let x = 42 in
  let x = 22 in
    ... (* whatever else is typed in the toplevel *)
```

对此进行正确思考的方法是，第二个 let 绑定 绑定了一个刚好与第一个 let 绑定 中具有相同名称的全新变量。

这是另一个值得研究的 utop 脚本 ：

```ocaml
# let x=42;;
val x : int = 42
# let f y = x+y;;
val f : int -> int = <fun>
# f 0;;
: int = 42
# let x=22;;
val x : int = 22
# f 0;;
- : int = 42  (* x did not mutate! *)
```

To summarize, each let definition binds an entirely new variable. If that new variable happens to have the same name as an old variable, the new variable temporarily shadows the old. But the old variable is still around, and its value is immutable: it never, ever changes. So even though let expressions might superficially look like assignment statements from imperative languages, they are actually quite different.总而言之，每个 let定义 都绑定一个全新的变量。 如果该新变量恰好与旧变量具有相同的名称，则新变量会暂时遮盖旧变量。 但是旧变量仍然存在，并且它的值是不变的：它永远不会改变。 因此，即使 let 表达式从表面上看起来像命令式语言的赋值语句，它们实际上大不相同。



# 2.3. Functions

如下代码

```ocaml
let x = 42
```

其中有一个表达式:（42），但它本身不是表达式。 而是一个定义。 定义将值绑定到名称，在这种情况下，值42绑定到名称 `x`。 OCaml 手册有[所有的定义](http://caml.inria.fr/pub/docs/manual-ocaml/modules.html)（请参见该页面上标题为“ definition”的第三大类），该手册页面同样主要供参考而非学习。 定义既不是表达式，也不是表达式定义，它们是不同的语法类。 但是定义可以将表达式嵌套在其中，反之亦然。

现在，让我们集中讨论一种特定的定义，即函数定义。 非递归函数的定义如下：

```ocaml
let f x = ...
```

递归函数的定义如下：

```ocaml
let rec f x = ...
```

区别只是 `rec` 关键字。 必须显式地添加关键字以使函数可递归可能有点令人惊讶，因为大多数语言默认就可以递归。 但是，OCaml 并没有做这个假设。

阶乘函数是最著名的递归函数之一。 在 OCaml 中，可以这样编写：

```ocaml
(* requires: n >= 0 *)
(* returns: n! *)
let rec fact n =
  if n=0 then 1 else n * fact (n-1)
```

我们在函数上方提供了规范注释，以记录函数的前置条件（要求）和后置条件（返回）。

请注意，就像在许多语言中一样，OCaml 整数不是“数学”整数，而受限于固定的 bits (位数)。 [手册](http://caml.inria.fr/pub/docs/manual-ocaml/values.html#sec76)指定整数至少为30位，甚至更宽。 因此，如果你在足够大的输入上进行测试，则可能会看到奇怪的结果。 这个问题根源在于机器的算数运算，而非 OCaml。

这是另一个递归函数:

```ocaml
(* requires: y>=0 *)
(* returns: x to the power of y *)
let rec pow x y =
  if y=0 then 1
  else x * pow x (y-1)
```

请注意，我们不必在任何一个函数中编写任何类型：OCaml 编译器会自动地为我们推断出来。 编译器通过算法解决了这种*类型推导问题，但是我们也可以自己解决。 这就像是一个谜，可以通过我们的推理能力来解决：

- 由于 if 表达式可以在 `then` 分支中返回 `1`，因此可以通过类型规则知道整个 if 表达式是否具有 `int` 类型。
- 由于 if 表达式的类型为 `int`，因此函数的返回类型必须为 `int`。
- 由于使用相等运算符将 `y` 与 `0` 进行比较，因此 `y` 必须为整数。
- 由于 `x` 使用 `*` 运算符与另一个表达式相乘，因此 `x` 必须为 `int`。

如果我们确实出于某种原因想要写下类型，可以这样做：

```ocaml
(* requires: y>=0 *)
(* returns: x to the power of y *)
let rec pow (x:int) (y:int) : int =
  if y=0 then 1
  else x * pow x (y-1)
```

当我们为 `x` 和 `y` 编写 *类型注解* 时，括号是必需的。 我们通常会省略这些注解，因为让编译器推断它们更为简单。 在其他时候，你可能想要显式地写下类型。 一个特别有用的时候是当你从编译器收到没有意义(看不太懂)的类型错误时。 显示的类型注解可以帮助调试此类错误消息。

**语法.** 函数定义的语法:

```ocaml
let rec f x1 x2 ... xn = e
```

`f` 是一个元变量，指示它是用作函数名称的标识符。 这些标识符必须以小写字母开头。 其余 [小写标识符 ](http://caml.inria.fr/pub/docs/manual-ocaml/lex.html#lowercase-ident)的规则可在手册中找到。 名称 `x1` 到 `xn` 是表示参数标识符的元变量, 遵循与函数标识符相同的规则。 如果 `f` 要成为递归函数，需要关键字 `rec`。 否则可以省略。

请注意，上面的函数定义语法(syntax sugar)实际上简化 OCaml 真正许可的定义语法(原始定义语法)。 在接下来的几周中，我们将详细了解一些用于函数定义的增强语法。 但是目前，此简化版本有助于我们集中精力。

可以使用 `and` 关键字定义相互递归函数：

```ocaml
let rec f x1 ... xn = e1
and g y1 ... yn = e2
```

例如:

```ocaml
(* [even n] is whether [n] is even.
 * requires: [n >= 0] *)
let rec even n =
  n=0 || odd (n-1)

(* [odd n] is whether [n] is odd.
 * requires: [n >= 0] *)
and odd n =
  n<>0 && even (n-1);;
```

函数类型的语法:

```ocaml
t -> u
t1 -> t2 -> u
t1 -> ... -> tn -> u
```

`t` 和 `u` 是表示类型的元变量。 类型 `t-> u` 是接受类型 `t` 作为输入并返回类型 `u` 的函数的类型。 我们可以将 `t1-> t2-> u` 视为一个函数的类型，该函数需要两个输入，第一个输入为 `t1` 类型，第二个输入为 `t2` 类型，并返回一个 `u` 类型的输出。 对于需要 `n`个参数的函数也是一样。

**动态语义.** 函数定义没有动态语义。 没有什么需要求值计算的。 OCaml 只是记录名称 `f` 已绑定到具有给定参数 `x1..xn` 和给定主体 `e` 的函数。 只有稍后，当应用该函数时，才会进行一些求值计算。

**静态语义.** 函数定义的静态语义:

- 非递归函数: 如果假定 `x1: t1, x2: t2 ... xn: tn` 可以得出 `e: u`,  那么 `f: t1 -> t2 -> ... -> tn -> u`
- 递归函数: 如果假定 `x1: t1, x2: t2 ... xn: tn ` 并且 `f : t1 -> t2 -> ... -> tn -> u`, 如果可以得出 `e: u`, 那么 `f: t1 -> t2 -> ... -> tn -> u`

请注意，递归函数的类型检查规则如何假设函数标识符 `f` 具有特定类型，然后检查在此假设下函数主体是否类型正确。 这是因为 `f` 在函数主体本身的作用范围内（就像参数在作用范围内一样）。



# 2.3.1. Anonymous Functions

我们已经知道可以有不绑定名称的值。 例如，可以在 toplevel 输入整数42而无需命名：

```ocaml
# 42;;
- : int = 42
```

或者给它绑定到一个名称:

```ocaml
# let x = 42;;
val x : int = 42
```

同样，OCaml 函数不必具有名称。 他们可能是*匿名*的。 例如，这是一个匿名函数，可对其输入加1： `fun x-> x + 1`。 在这里，`fun` 是表示匿名函数的关键字，`x` 是参数，`->` 将参数与主体分开。

现在，我们有两种方式可以编写加1函数了：

```ocaml
let inc x = x + 1
let inc = fun x -> x+1
```

它们在语法上是不同的，但在语义上是等效的。 也就是说，即使它们包含不同的关键字并将某些标识符放在不同的位置，它们的含义也相同的。

匿名函数也被称为 *lambda 表达式*，它是来自 *lambda calculus* ( lambda演算) 的术语，它是一个在计算方面与图灵机等价的数学模型。 在 λ 演算中，`fun x-> e` 被写成 `λx.e`。 λ 表示这是一个匿名函数。

现在似乎有些神秘: 为什么我们想要的函数没有名称。 不用担心 我们将在本课程的后面看到它们的良好用途。 特别是，我们经常会创建匿名函数，并将其作为输入传递给其他函数。

**语法.**

```ocaml
fun x1 ... xn -> e
```

**静态语义.**

- 如果假定 `x1: t1, x2: t2 ... xn: tn` 可以得到 `e: u`, 那么 `fun x1 ... xn -> e : t1 -> t2 -> ... -> tn -> u`

**动态语义.**

匿名函数已经是一个值。 没有要执行的计算。



# 2.3.2. Function Application

与 OCaml 实际允许的函数相比，这里我们已经涵盖了函数应用的某种简化语法。

**语法.**

```ocaml
e0 e1 e2 ... en
```

第一个表达式 `e0`是函数，并将其应用于参数 `e1` 到 `en`。 请注意函数应用时，在参数周围不需要 C 系列语言（包括Java）那样使用括号。

**静态语义.**

- 如果 `e0 : t1 -> ... -> tn -> u` 且 `e1:t1`  ... 且 `en:tn` , 那么 `e0 e1 ... en : u`

**动态语义.**

`e0 e1 ... en` 求值:

1. `e0` 求值为一个函数. 同时对参数表达式  `e1 ... en` 分别求值得到 `v1 ... vn`. 对于 e0 ，求值结果可能是匿名函数 fun x1 ... xn-> e。 或者它可能是名称 f ，我们必须找到 f 的定义，在这种情况下，我们假设定义为 let rec f x1 ... xn = e。 无论哪种方式，我们现在都知道参数名称 x1 ... xn和函数体 e。

   对于 `e0` ，求值结果可能是匿名函数 `fun x1 ... xn-> e`。 或者它可能是名称 `f` ，我们必须找到 `f` 的定义，在这种情况下，我们假设定义为 `let rec f x1 ... xn = e`。 无论哪种方式，我们现在都知道参数名称 `x1 ... xn`和函数体 `e`。

2. 分别用值 `vi` 替换掉函数体 `e` 中对应的参数名 `xi`。 得到一个新的表达式 `e'`。

3. 对 `e'` 求值得到的 `v`，就是 `e0 e1 ... en` 的求值结果。

如果将这些求值规则与 let表达式的规则进行比较，你会注意到它们都涉及替换。 这不是意外。 实际上，在程序中任何地方出现的 `let x = e1 in e2` ，我们都可以将其替换为 `(fun x-> e2) e1`。 它们在语法上是不同的，但在语义上是等效的。 从本质上讲，let 表达式只是匿名函数应用的语法糖。

## 管道

OCaml 中有一个内置的中缀运算符 `|>`，用于编写函数应用。 想象一下，它描绘了一个指向右边的三角形。 它被称为管道运算符，隐喻是值通过管道从左到右发送。 例如，之前用过的 `inc` 函数 以及一个平方函数。 下面是编写计算的两种等效方法：

```ocaml
square (inc 5)
5 |> inc |> square
(* both yield 36 *)
```

后一种方法使用了管道运算符向 `inc` 函数发送5，然后将结果发送给平方函数。 在 OCaml 中这是表达计算的一种很好的惯用方式。 前一种方法还可以，但不是那么优雅，因为它涉及写多余的括号，并且要求读者的眼睛四处跳动，而不是从左到右线性移动。 当应用的函数数量增加时，后一种方法可以很好地扩展，而前一种方法需要更多的括号：

```ocaml
5 |> inc |> square |> inc |> inc |> square
square (inc (inc (square (inc 5))))
(* both yield 1444 *)
```

刚开始看起来可能很奇怪，但是下次你发现自己在编写大量函数应用时，请尝试使用管道运算符。

由于 `e1 |> e2` 只是编写 `e2 e1` 的另一种方式，因此我们无需说明 `|>` 的语义：它与函数应用相同。 这两个程序是语法上不同但语义上等效的表达式的另一个示例。



# 2.3.3. Polymorphic functions

语法上最简单的函数应该是 *identity* 函数，即 `let id x = x`。它的类型是什么？ 我们可以将其输入到 toplevel 看看：

```ocaml
# let id x = x;;
id : 'a -> 'a = <fun>
```

`'a` 是一个类型变量：它代表未知类型，就像常规变量代表未知值一样。 类型变量始终以单引号开头。 常用的类型变量包括 `'a, 'b 和 'c`，OCaml 程序员通常用希腊语发音：alpha，beta和gamma。

我们可以用任何类型的值去应用 identity 函数:

```ocaml
# id 42;;
- : int = 42

# id true;;
- : bool = true

# id "bigred";;
- : string = "bigred"
```

因为你可以将该函数应用于多种类型的值，所以它是一个 *polymorphic* (*多态*)函数。 （*poly* = 许多, *morph* = 形式）



# 2.3.4. Labeled and optional arguments

通常，函数的类型和名称使你对参数应该是啥样有一个很好的提示。 但是，对于具有许多参数（尤其是相同类型的参数）的函数，标记它们可能很有用。 例如，你可能会猜测函数 `String.sub` 返回给定字符串的子字符串。 你可以输入 `String.sub` 来查看其类型：

```ocaml
# String.sub;;
- : string -> int -> int -> string
```

但是从类型上看还是不太清楚如何使用它 — 不得不查阅文档。

OCaml 支持带标签的函数参数。 你可以使用以下语法声明这种函数：

```ocaml
# let f ~name1:arg1 ~name2:arg2 = arg1 + arg2;;
val f : name1:int -> name2:int -> int = <fun>
```

可以通过以下任一顺序传递带标签的参数来调用此函数：

```ocaml
f ~name2:3 ~name1:4;;
```

参数的标签通常与它们的变量名称相同。 OCaml 提供了这种情况的简写。 以下是等效的：

```ocaml
let f ~name1:name1 ~name2:name2 = name1+name2
let f ~name1 ~name2 = name1 + name2
```

使用带标签的参数在很大程度上只关乎个人品味：它们传达了额外的信息，但也可能使类型混乱。

如果你需要为函数同时写带标签的参数和显式的类型注释，那么可以像这样来做：

```ocaml
let f ~name1:(arg1:int) ~name2:(arg2:int) = arg1 + arg2
```

也可以将某些参数设为可选：当不带参数调用时，将使用提供默认值。 要声明这样的函数，请使用以下语法：

```ocaml
# let f ?name:(arg1=8) arg2 = arg1 + arg2;;
val f : ?name:int -> int -> int = <fun>
```

然后你可以调用带有或不带参数的函数:

```ocaml
# f ~name:2 7;;
- : int = 9

# f 7;;
- : int = 15
```





# 2.3.5. Partial application

我们可以如下定义一个 addition 函数:

```ocaml
let add x y = x + y
```

下面是一个非常类似的函数:

```ocaml
let addx x = fun y -> x + y
```

函数 `addx` 接受一个整数 `x` 作为输入，并返回一个 `int-> int` 类型的函数，该函数会将 `x` 加到传递给它的任何对象上。

`addx` 的类型为 `int-> int-> int`。 `add` 的类型也是 `int-> int-> int`。 因此，从类型角度来看，它们是相同的函数。 但是 `addx` 的形式表明了一些有趣的事情：函数可以仅应用于单个参数。

```ocaml
# let add5 = addx 5;;
add5 : int -> int = <fun>

# add5 2;;
- : int = 7
```

事实证明，可以使用 `add` 完成相同的操作：

```ocaml
# let add5 = add 5;;
add5 : int -> int = <fun>

# add5 2;;
- : int = 7
```

你刚刚做的事情称为 *partial application* (偏函数化应用)：即使你通常会将函数 `add` 视为一个多参数函数，我们也会将函数 `add` 偏函数化应用于一个参数。 Why does this work?为什么这样做？ 这是因为以下三个函数在语法上不同，但在语义上是完全等效的。 也就是说，它们以不同的方式表示相同计算：

```ocaml
let add x y = x+y
let add x = fun y -> x+y
let add = fun x -> (fun y -> x+y)
```

因此，`add` 实际上是一个接受参数 `x` 并返回一个函数的函数 `(fun y -> x+y)`。 这导致我们有了更深刻的理解...

## 函数结合性

你准备好接受事实了吗？ 开始...

**实际上 OCaml 函数只有一个参数**

为什么？ 考虑 `add:` , 尽管我们可以将其写为 `let add x y = x + y`，但我们知道在语义上等价于 `let add = fun x->（fun y-> x + y）`。 通常，

```ocaml
let f x1 x2 ... xn = e
```

语义上等价于

```ocaml
let f =
  fun x1 ->
    (fun x2 ->
       (...
          (fun xn -> e)...))
```

因此，即使你将 `f` 视为具有 `n` 个参数的函数，但实际上它是一个接受1个参数并返回一个函数的函数。

这种函数的类型:

```ocaml
t1 -> t2 -> t3 -> t4
```

其实和下面是一样的:

```ocaml
t1 -> (t2 -> (t3 -> t4))
```

也就是说，函数类型是 *right associative* (右结合)的：函数类型从右到左都有隐式括号。 直觉是: 一个函数接受一个参数，然后返回一个新的函数，该函数需要剩余的参数。

另一方面，函数应用是 *left associative* (左结合) 的：在函数应用周围，从左到右都有隐式括号。 所以

```ocaml
e1 e2 e3 e4
```

其实和下面是一样的:

```ocaml
((e1 e2) e3) e4
```

直觉上就是最左边的表达式将右边相邻的一个表达式作为单个参数。



# 2.3.6. Operators as Functions

加法运算符 `+` 的类型为in `t-> int-> int`。 它通常用用作中缀表示，例如 `3 +4`。通过加上括号，我们可以使其成为前缀运算符：

```ocaml
# (+);;
- : int -> int -> int = <fun>

# (+) 3 4;;
- : int = 7

# let add3 = (+) 3;;
- : int -> int = <fun>

# add3 2;;
- : int = 5
```

相同的技术适用于任何内置运算符。 但是要小心乘法运算符，用作前缀运算符时必须写成 `( * )`，因为 `(*)` 会被解析成注释的开头。

我们甚至可以定义自己的中缀运算符, 比如:

```ocaml
let (^^) x y = max x y
```

现在 `2 ^^ 3` 的值为 `3`。可用标点符号创建中缀运算符的规则以及该运算符的相对优先级不一定是符合直觉的。 因此，请谨慎使用此用法。



# 2.3.7. Documentation

OCaml 提供了一个称为 OCamldoc 的工具，其功能与 Java 的Javadoc 工具非常相似：它从源代码中提取特殊格式的注释，并将其呈现为 HTML，从而使程序员易于阅读文档。

## How to document

这是一个 OCamldoc 注释的示例：

```ocaml
(** [sum lst] is the sum of the elements of [lst]. *)
let rec sum lst = ...
```

- 双星号是导致注释被识别为应提取的 OCamldoc注释 的原因。
- 注释部分周围的方括号表示这些部分在 HTML 中应以打字机字体而不是常规字体显示。

与 Javadoc 一样，OCamldoc 也支持文档标签，例如`@ author，@ deprecated，@ param，@ return`等。例如，在大多数编程工作的第一行中，我们要求你完成如下注释：

```ocaml
(** @author Your Name (your netid) *)
```

有关OCamldoc 注释中可能的标记的完整范围，请参见 [OCamldoc手册](https://caml.inria.fr/pub/docs/manual-ocaml/ocamldoc.html)。 但是我们这里介绍的内容足以满足你需要编写的大多数文档的要求。

## What to document

我们在本课程中偏爱的文档风格类似于 `OCaml` 标准库的风格：简洁和声明式的 (declarative)。 例如，让我们重新查看 `sum` 的文档：

```ocaml
(** [sum lst] is the sum of the elements of [lst]. *)
let rec sum lst = ...
```

该注释以 `sum lst` 开头，这是一个函数对参数应用的示例。 注释以单词“ is”继续，从而声明性地描述了应用的结果。 （可以使用单词“ returns”代替，但是“ is”强调该函数的数学性质。）该描述使用参数名称 `lst` 来解释结果。

请注意，与 `Javadoc` 一样，无需额外添加标签来冗余地描述参数或返回值,  该说的都已经说了。 我们强烈建议你不要像下面遮掩使用文档：

```ocaml
(** Sum a list.
    @param lst The list to be summed.
    @return The sum of the list. *)
let rec sum lst = ...
```

这个糟糕的文档用了不必要且难以阅读的三行来表达与单行版本(还更清晰)相同的含义。

到目前为止，我们可以通过两种方法来改进文档。 首先是它忽视了 `lst` 为空时的情况。 在数学上，当然定义得很清楚：一个空序列的总和为0。但这是一个可能会在读者的脑海中出现的问题。 因此，将其添加到文档中不会带来任何伤害。

```ocaml
(** [sum lst] is the sum of the elements of [lst].
    The sum of an empty list is 0. *)
let rec sum lst = ...
```

第二个问题是，在文档中说明参数的类型并没有什么坏处(没有说明参数类型)，如下所示：

```ocaml
(** [sum lst] is the sum of the elements of list [lst].
    The sum of an empty list is 0. *)
let rec sum lst = ...
```

由于很自然地将变量名 `lst` 读作列表，因此这种用法可能有点过度了 (overkill)。 但是在下一节的示例中，提供额外的说明很有用。



# 2.3.8. Preconditions and Postconditions

以下是我们在本课程中比较喜欢的风格的其他一些注释示例。

```ocaml
(** [lowercase_ascii c] is the lowercase ASCII equivalent of
    character [c]. *)

(** [index s c] is the index of the first occurrence of
    character [c] in string [s].  Raises: [Not_found]
    if [c] does not occur in [s]. *)

(** [random_int bound] is a random integer between 0 (inclusive)
    and [bound] (exclusive).  Requires: [bound] is greater than 0
    and less than 2^30. *)
```

`index` 的文档指明了该函数可能引发的异常，以及异常是什么以及引发该异常的条件。 （我们将在下一章中更详细地介绍异常。）`random_int `的文档指明函数参数必须满足的条件。

在 CS 1110 和 2110 中学习Python 和 Java 时，你了解了前置条件和后置条件的思想。 前置条件, 后置条件就是在某些代码段之前, 之后必须为真的条件。

上面 `random_int` 文档中的“ Requires”子句是一种前置条件。 它是关于 `random_int` 函数的客户端(调用方)负责保证 `bound` 的值的事情。 同样，该文档的第一句话是一种后置条件。 它负责确保函数返回值的信息。

`index` 文档中的“Raises”子句是另一种后置条件。 它确保函数引发异常的信息。

注意，该子句不是前置条件，即使它确实根据输入描述了一种条件(情况)。

请注意，这些示例都没有“ Requires”来说明了输入的类型。 如果你来自动态类型的语言（例如 Python），这可能会令人惊讶。 Python 程序员经常记录(document)有关函数输入类型的前提条件。 但是，OCaml 程序员不用。 这是因为编译器本身会进行类型检查，以确保你永远不会将错误类型的值传递给函数。 再次考虑一下 `lowercase_ascii`：尽管英文注释可以帮助读者识别 `c` 的类型，但是该注释并未像下面这样声明“ Requires”子句：

```ocaml
(** [lowercase_ascii c] is the lowercase ASCII equivalent of [c].
    Requires: [c] is a character. *)
```

这样的注释对于 OCaml 程序员来说是很不符合习惯用法，读到该注释会感到困惑，也许会认为说：“`c` 当然是一个字符；编译器会保证。写这个注释的人到底是什么意思？ 是他们还是我漏掉了什么吗？”



# 2.4. Debugging

当其他所有操作均失败时，最后就只能诉诸调试了。 让我们退后一步，考虑一下调试之前的所有事情。

## 防御 Bugs

据 [Rob Miller](https://stellar.mit.edu/S/course/6/fa08/6.005/courseMaterial/topics/topic3/lectureNotes/Debugging/Debugging.pdf) 说, 针对 bugs 有四种防御措施:

1. **预防 Bug 的第一道防线是将他们扼杀在摇篮中**

   可以通过选择以保证 *[内存安全 ](http://www.pl-enthusiast.net/2014/07/21/memory-safety/)*的语言编程来消除所有错误类别，这些语言可以保证内存安全性（除非通过该内存区域的有效指针或引用(合法指针)，否则无法访问内存的任何部分）和 *[类型安全](http://www.pl-enthusiast.net/2014/08/05/type-safety/)*（不会出现值与其类型不一致的使用方式）。 例如，OCaml 的类型系统可以防止程序发生缓冲区溢出和无意义的操作（如将布尔值添加到浮点数），而 C 的类型系统做不到。

2. **预防 Bug 的第2道防线是使用定位 Bug 的工具**

   有一些自动的源代码分析工具，例如 [FindBugs](http://findbugs.sourceforge.net/)（可在 Java 程序中找到许多常见的错误）和 [SLAM](http://research.microsoft.com/en-us/projects/slam/)（用于在设备驱动程序中查找 Bug）。 被称为*形式方法 *的 CS 的一个子领域, 研究如何使用数学来指定和验证程序，即如何证明程序没有错误。 我们将在本课程后面学习验证。

   诸如代码审查和结对编程之类的 *Social methods (社交方法)* 也是查找 bugs 的有用工具。 IBM在1970年代至1990年代的研究表明，代码审查有非常显著的效果。 在一项研究中（Jones，1991），代码侦查发现了65％的已知编码错误和25％的已知文档错误，而测试仅发现了20％的编码错误而没有文档错误。

3. **预防 Bug 的第3防线是使它们立即可见**

   越早发现 bug，越容易诊断和修复。 如果计算继续进行到错误点之外，那么进一步的计算可能会掩盖真正发生故障的位置。 源代码中的 *断言* 使程序“快速失败”和“fail loudly (放大失败)”，从而使 bug 立即出现，进而程序员能确切地知道源代码查找的位置。

4. **预防 Bug 的第4道防线是广泛充分的测试**

   你如何知道一段代码是否存在特定错误？ 编写将会暴露该错误的测试用例，然后确认你的代码不会使这些测试用例失败。 在开发该代码的同时对相对较小的代码块（例如单个函数或模块）编写 *单元测试 *尤为重要。 运行这些测试应该是自动化的，这样，如果你破坏了代码，便会尽快发现。 （实际上又是防线3。）

在所有这些防御措施都失败之后，程序员被迫诉诸调试。

## 如何调试

因此，你发现了一个错误。 接下来是什么？

1. **将错误提炼成一个小的测试用例。** 调试是一项艰苦的工作，但是测试用例越小，你越有可能将注意力集中在潜伏 bug 的那段代码上。 因此，花时间(精力)提炼可以节省其他时间，因为你不必重新阅读很多代码。 在有一个小的测试用例之前，不要继续调试！
2. **采用科学的方法。 **提出关于 bug 发生原因的假设。 你甚至可以像在化学实验室一样在笔记本上写下该假设，以在自己的脑海中阐明该假设并跟踪你已经考虑过的假设。 接下来，设计一个实验来确认或否认该假设。 运行实验并记录结果。 根据所学知识，重新制定假设。 继续直到你已经合理科学地确定了错误的原因。
3. **修复 Bug。**  该修复程序可能是拼写错误的简单更正。 或者也可能揭示出导致你进行重大更改的设计缺陷。 考虑是否需要将修复应用于基于此代码的其他位置，例如，是否存在复制和粘贴错误？ 如果是这样，你是否需要重构代码？
4. **将小型测试用例永久添加到你的测试套件中。 ** 你不希望该错误重新出现在你的代码库中。 因此，通过将其作为单元测试的一部分来跟踪该小型测试用例。 这样，将来无论何时进行的更改，你都将自动防范相同的错误。 从以前的错误中提炼出来的反复运行的测试称为 *回归测试*。

## OCaml 调试

这里有一些提示，介绍如何在 OCaml 中进行调试（如果被迫调试）。

- **打印语句。** 插入打印语句以确定变量的值。 假设你想知道在下面的函数中 `x `的值是什么：

  ```ocaml
  let inc x =
    x+1
  ```

  只需要添加以下行即可打印该值:

  ```ocaml
  let inc x =
    let () = print_int(x) in
    x+1
  ```

  [Stdlib](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Stdlib.html) 模块包含许多其他打印语句。 我们将在下一部分中介绍其中的一些。

- **Function traces (函数跟踪)。** 假设你想查看递归调用栈跟踪和每个函数的返回。 使用 `#trace` 指令：

  ```ocaml
  let rec fib x = if x<=1 then 1 else fib(x-1) + fib(x-2)
  #trace fib;;
  ```

  如果求值 `fib 2`，将看到以下输出：

  ```ocaml
  fib <-- 2
  fib <-- 0
  fib --> 1
  fib <-- 1
  fib --> 1
  fib --> 2
  ```

  停止跟踪使用 `#untrace` 指令。

- **调试器。** OCaml 确实具有一个调试工具 `ocamldebug`。 你可以在 OCaml网 站上找到 [教程](https://ocaml.org/learn/tutorials/debug.html#The-OCaml-debugger)。 除非使用 Emacs 作为编辑器，否则你可能会发现此工具比仅插入打印语句更难使用。



# 2.4.1 Printing

OCaml 有针对多种内置基本类型的打印函数：`print_char，print_int，print_string 和 print_float`。 还有一个 `print_endline` 函数，类似于 `print_string`，但也输出换行符。

让我们看一下其中几个函数的类型：

```ocaml
# print_endline;;
- : string -> unit = <fun>

# print_string;;
- : string -> unit = <fun>
```

它们都接受一个字符串作为输入，并返回一个 `unit` 类型的值，这是我们之前从未见过的。 此类型只有一个值，该值写作 `()` ，也称为“unit”。 所以 unit 就像布尔，除了 unit 的值比布尔的少一个。 因此，当你需要接受一个参数或返回一个值，但是又没有有趣的值可以传递或返回时，使用 unit。 通常使用 unit 来编写或使用有副作用的代码。 打印是一个副作用的例子：它改变了世界，并且无法撤消。

如果要一遍又一遍打印，可以使用嵌套的 let 表达式序列一些打印函数：

```ocaml
let x = print_endline "THIS" in
let y = print_endline "IS" in
print_endline "3110"
```

但是上面所有 `let x = ...` 样板令人讨厌, 但又不得不写！ 我们真的不在乎为这些打印函数返回的单位值命名。 因此，有一种特殊的语法可用于将返回 unit 的多个函数链接在一起。 表达式 `e1; e2` 首先计算 `e1`，该 `e1` 应该计算为 `()`，然后丢弃该值，然后计算 `e2`。 因此，我们可以将以上代码重写为：

```ocaml
print_endline "THIS";
print_endline "IS";
print_endline "3110"
```

这是更多惯用的代码。

如果 `e1` 不是 `unit` 类型的，则 `e1; e2` 将发出警告，因为你正在丢弃有用的值。 如果这确实是你的意图，则可以调用内置函数 `ignore：'a-> unit` 将任何值转换为 `()`：

```ocaml
# 3; 5;;
Warning 10: this expression should have type unit.
- : int = 5

# ignore 3; 5;;
- : int = 5
```



# 2.4.2. Defensive Programming

正如我们在调试部分前面所讨论的，针对错误的一种防御措施是使所有 bugs 或 errors（异常）立即可见。 这个想法与前置条件的想法联系在一起。

考虑以下 `random_int` 规范：

```ocaml
(** [random_int bound] is a random integer between 0 (inclusive)
    and [bound] (exclusive).  Requires: [bound] is greater than 0
    and less than 2^30. *)
```

如果 `random_int` 的客户端(调用者)传递的 `bound` 值违反了“ Requires”子句（例如 `-1`），则 `random_int` 的实现可以自由执行任何操作。 当客户违反前置条件时, All bets are off。

但是 `random_int` 要做的最有用的事情是立即揭露前置条件被违反的事实。 毕竟，客户很有可能不是故意违反它。

因此，`random_int` 的实现者会很好地检查是否违反了前置条件，如果违反，则引发异常。 以下是这种*防御性编程*的三种可能性：

```ocaml
(* possibility 1 *)
let random_int bound =
  assert (bound > 0 && bound < 1 lsl 30);
  (* proceed with the implementation of the function *)

(* possibility 2 *)
let random_int bound =
  if not (bound > 0 && bound < 1 lsl 30)
  then invalid_arg "bound";
  (* proceed with the implementation of the function *)

(* possibility 3 *)
let random_int bound =
  if not (bound > 0 && bound < 1 lsl 30)
  then failwith "bound";
  (* proceed with the implementation of the function *)
```

第二种可能是对客户端最有用的信息，因为它使用内置函数 `invalid_arg` 引发名称明确的异常 `Invalid_argument`。 （实际上，此函数的标准库的实现正是这样做的。）

当你尝试调试自己的代码而不是选择向客户端暴露失败的断言时，第一种可能性可能是最有用的。

第三种可能性与第二种可能性仅在引发的异常名称（`Failure`）上有所不同。 在前置条件涉及的不只是单个无效参数的情况下，此方法可能很有用。

在此示例中，检查前置条件在计算上很便宜。 在其他情况下，它可能需要大量的计算，因此该函数的实现者可能更倾向于不检查前置条件，或者仅检查其一些便宜的近似值。

有时，程序员不必要地担心防御性编程会太昂贵 — 无论是从最初实施检查的时间花费，还是从检查断言中付出的运行时成本。 这些考虑经常被用错地方。 社会花费大量时间和金钱修复软件故障表明，我们都承受得起程序运行得稍慢一点。

最后，实现者甚至可以选择消除前提条件并将其重新声明为后置条件：

```ocaml
(** [random_int bound] is a random integer between 0 (inclusive)
    and [bound] (exclusive).  Raises: [Invalid_argument "bound"]
    unless [bound] is greater than 0 and less than 2^30. *)
```

 现在，不应该放任 `bound` 太大或太小以至于他可以随意地做任何事情，为此 `random_int` 必须引发异常。 对于此函数，这可能是最佳选择。

在本课程中，我们不会强迫你进行防御性编程。 但是，如果你精明，无论如何都将开始（或继续）进行。 你花费很少的时间编写此类防御措施，将节省你数小时的调试时间，从而使你成为更有效率的程序员。



# 2.5 Summary

语法和语义是学习编程语言的强大范例。 当我们学习 OCaml 的特性时，我们会小心地写下它们的语法和语义。 我们已经看到，可以存在多种语法来表达相同的语义思想，即相同的计算。

函数应用的语义是 OCaml 和函数式编程的核心，在整个课程中，我们将反复几次以加深理解。

## 术语和概念

- anonymous functions
- assertions
- binding
- binding expression
- body expression
- debugging
- defensive programming
- definitions
- documentation
- dynamic semantics
- evaluation
- expressions
- function application
- function definitions
- identifiers
- idioms
- if expressions
- lambda expressions
- let definition
- let expression
- libraries
- metavariables
- mutual recursion
- pipeline operator
- postcondition
- precondition
- printing
- recursion
- semantics
- static semantics
- substitution
- syntax
- tools
- type checking
- type inference
- values

## 扩展阅读

- *Introduction to Objective Caml*, 第3章
- *OCaml from the Very Beginning*, 第2章
- *Real World OCaml*, 第2章



# 2.6. Exercises

##### Exercise: values [✭]

以下每个OCaml表达式的类型和值是什么？

- `7 * (1+2+3)`
- `"CS " ^ string_of_int 3110`

*提示：在 toplevel 键入每个表达式，它将告诉你答案。 注意：`^`不取幂。*

##### Exercise: operators [✭✭]

检查 [OCaml手册运算符表格](http://caml.inria.fr/pub/docs/manual-ocaml/expr.html) （你必须向下滚动才能在该页面上找到它）。

- 编写一个 `42` 乘以 `10` 的表达式.
- 编写一个 `3.14` 除以 `2.0` 的表达式. *提示：在OCaml中，整数和浮点运算符的写法不同.*
- 编写一个表达式，计算得出 `4.2` 的 7次幂。 注意：OCaml 中没有内置的整数幂运算符（顺便说一下，C 中也没有），部分原因是它不是大多数 CPU 提供的操作。

##### Exercise: equality [✭]

- 编写一个表达式: 使用结构相等性对 `42` 与 `42` 进行比较。
- 编写一个表达式: 使用结构相等性对 `"hi"` 和 `"hi"` 进行比较的。 结果是什么？
- 编写一个表达式: 使用物理相等性对 `"hi"` 和 `"hi"` 进行比较的。 结果是什么？

##### Exercise: assert [✭]

- utop 中输入 `assert true;;` 看看会发生什么.
- utop 中输入 `assert false;;` 看看会发生什么.
- 写一个断言2110（结构上）不等于3110的表达式。.

##### Exercise: if [✭]

编写一个 if 表达式，如果 `2` 大于 `1`，则结果为 `42`，否则为 `7`。

##### Exercise: double fun [✭]

使用上面的 increment  函数作为指导，定义一个函数 `double`，它将其输入乘以2。例如，`double 7` 为 `14`。通过将其应用于几个输入来测试你的函数。 将那些测试用例转换为断言。

##### Exercise: more fun [✭✭]

- 定义一个计算浮点数的立方的函数。 通过将其应用于一些输入来测试你的函数。
- 定义一个计算整数的sign符号（1、0或-1）的函数。 使用嵌套的 if 表达式。 通过将其应用于一些输入来测试你的功能。
- Define a function that computes the area of a circle given its radius. Test your function with `assert`.定义一个函数，根据给定的半径计算圆的面积。 使用 `assert` 测试你的函数。

对于后者，请记住浮点算术是不精确的。 你应该断言结果是“足够接近”的，例如误差在 1e-5 之内，而不是断言确切的值。 如果你不熟悉该方法，那么 [浮点算法](https://floating-point-gui.de/) 是值得阅读的。

可以通过为 let 输入提供其他名称作为 let 定义的一部分来定义接受多个输入的函数。 例如，以下函数计算三个参数的平均值：

```ocaml
let avg3 x y z =
  (x +. y +. z) /. 3.
```

##### Exercise: RMS [✭✭]

定义一个函数来计算两个数字的均方根 — 即 $\sqrt{(x^2+y^2)/2}$. 使用 `assert` 断言测试你的函数.

##### Exercise: date fun [✭✭✭]

定义一个函数，该函数将整数 `d` 和字符串 `m` 作为输入，并仅在 `d` 和 `m` 形成有效日期时才返回true。 在这里，有效日期的月份是以下缩写之一：Jan, Feb, Mar, Apr, May, Jun, Jul, Aug, Sept, Oct, Nov, Dec。日必须是介于 1以及该月的最小天数（含）的数字。 例如，如果月份为 Jan，则日期在1到31之间（包括1和31），而如果月份为2月，则日期在1到28之间（包括1和28）。

你能使函数多么简洁（即几行代码）？ 绝对可以少于12行。

##### Exercise: editor tutorial [✭✭✭]

你使用哪个编辑器很大程度上取决于个人喜好。 Atom，Sublime 和 Komodo 均提供了现代GUI。 Emacs 和 Vim 更多基于文本。 如果你从未尝试过 Emacs 或 Vim，为什么不花10分钟呢？ 有很多理由使它们受到许多程序员的喜爱。

- 要开始学习Vim，请运行 `vimtutor -g`.
- 请运行 `emacs`，然后按 `C-h t`，即按 Control + H，然后按 t。

##### Exercise: master an editor [✭✭✭✭✭, advanced]

你将在整个职业生涯中继续从事这项练习！ 尽量不要卷入任何 [编辑器战争](https://xkcd.com/378/)。

##### Exercise: fib [✭✭]

定义递归函数 `fib : int -> int`, 使得 `fib n` 是 [斐波那契数列](https://en.wikipedia.org/wiki/Fibonacci_number) 的第 `n` 个数字, 即 1, 1, 2, 3, 5, 8, 13, ... :

- `fib 1 = 1`,
- `fib 2 = 1`,
- `fib n = fib (n-1) + fib (n-2)` 对任何 `n > 2`.

在 toplevel 中测试你的函数.

##### Exercise: fib fast [✭✭✭]

你实现的 `fib` 会多快计算出第50个斐波那契数？ 如果它几乎是立即完成的，那么恭喜！ 但是大多数人起初想出的递归解决方案似乎会无限期地挂起。 问题在于显而易见的解决方案会重复计算子问题。 例如，计算 `fib 5` 需要同时计算 `fib 3` 和 `fib 4`，并且如果分别计算它们，那么将重做大量工作（实际上是指数级的）。

创建一个函数 `fib_fast`，该函数只需要线性量的工作即可。 提示：编写一个递归辅助函数 `h：int-> int-> int-> int`，其中 `h n pp p` 的定义如下：

- `h 1 pp p = p`, and
- `h n pp p = h (n-1) p (pp+p)` for any `n > 1`.

`fib n = h n 0 1` 对任何 `n > 0`。`h` 的想法是假设前两个斐波那契数是 `pp` 和 `p`，然后再计算 `n` 个数。 因此，对于任何 `n> 0`，`fib n = h n 0 1`。

第一个使 `fib_fast n `为负的 `n` 的值是多少，表示发生了整数溢出吗？

##### Exercise: poly types [✭✭✭]

以下每个函数的类型是什么？ 你可以使用 toplevel 检查你的答案。

```ocaml
let f x = if x then x else x
let g x y = if y then x else x
let h x y z = if x then y else z
let i x y z = if x then y else y
```

##### Exercise: divide [✭✭]

Write a function. Apply your function.编写一个除法函数： `divide : numerator:float -> denominator:float -> float`。 应用你的函数。

##### Exercise: associativity [✭✭]

假设我们定义了 `let add x y = x + y`。 以下哪个产生整数，哪个产生函数，哪个产生错误？ 确定答案，然后在 toplevel 中检查你的答案。

- `add 5 1`
- `add 5`
- `(add 5) 1`
- `add (5 1)`

##### Exercise: average [✭✭]

定义一个中缀运算符 `+/.` 来计算两个浮点数的平均值. 例如,

- `1.0 +/. 2.0 = 1.5`
- `0. +/. 0. = 0.`

##### Exercise: hello world [✭]

在 utop 中输入:

```ocaml
# print_endline "Hello world!";;
# print_string "Hello world!";;
```

注意它们之间的区别



<br/>