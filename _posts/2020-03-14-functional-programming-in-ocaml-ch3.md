---
layout:          post
title:           OCaml函数式编程 - 第3章
description:     Cornell大学 CS 3110 课程教材 Functional Programming in OCaml 翻译
date:            2020-03-14 19:24:52 +08:00
author:          "Clark Than"
category:        PL
tags:            OCaml PL
---


# Data in OCaml

本章中，我们将学习 OCaml 的一些内置数据类型，包括 lists，variants，record，tuples 和 options。 其中有些可能是在很多其他编程语言中熟悉过的。 我们将学习 *模式匹配*，这是一个可能不那么熟悉的语言特性。

然后，我们将看到如何定义自己的 *代数数据类型*，以及有多少 OCaml 的内建数据类型是不必内置的，而实际上是可以用代数数据类型等效定义的。

我们还将学习如何使用上述特性的各种组合来定义 trees 和 maps 等数据类型。



# 3.1. Standard Data Types

在本章的上半部分，我们将研究 OCaml 内置的数据类型(可能在许多其他语言也很熟悉的)。 由于这种熟悉，我们称它们为标准数据类型。 它们包括：

- **列表(lists)** 和 **元组(tuples)**，可能感觉类似于 Python；
- **记录(records)** and **变体(variants)**，可能感觉与 C 或 Java 中的 `struct` 和 `enum` 类型相似。

我们还绕过了单元测试。



# 3.1.1. Lists

OCaml 列表是一个具有相同类型值的序列, 它们被实现为单链表。 列表在 OCaml 中享有第一类(first-class)的地位：为轻松创建和使用列表提供了特殊的支持。 这是 OCaml 与许多其他函数式语言共有的一个特征。 如今，诸如 Python 之类的主流命令式语言也具有这种支持。 也许是因为程序员觉得直接将列表作为语言的第一类部分而工作是一件令人愉快的事情，而不是像使用 C 和 Java 那样必须使用库才行。



# 3.1.1.1. Building Lists

**语法.** 有三种构建列表的语法形式：

```
[]
e1::e2
[e1; e2; ...; en]
```

空列表写做 `[]` ，读作为“ nil”，该名称来自 Lisp。 给定一个列表 `lst` 和元素 `elt`，我们可以通过编写 `elt::lst` 来将元素 `elt` 添加到列表 `lst` 前面从而得到一个新的列表。 双冒号运算符叫做 “ cons”，该名称来自 Lisp 中的一个运算符，用于在内存中构造对象。 “ Cons”也可以用作动词，例如 “我将把一个元素添加(Cons)到列表中”。 列表的第一个元素通常称为 *头部*，其余元素（如果有）称为 *尾部*。

方括号语法很方便，但并不是必须的。 任何列表 `[e1; e2; ...; en]` 可以使用更原始的 nil 和 cons 语法编写：`e1 :: e2 :: ... :: en :: []`。 在语言中可以用令人更愉快的语法来定义原始的语法时，我们将这些令人愉快的语法称为 “语法糖”：它使语言 “sweeter”。 将语法糖还原为更原始的语法形式称为  *desugaring*。

因为列表的元素可以是任意表达式，所以列表可以嵌套得很深，例如, `[[[]]; [[1;2;3]]]`

**动态语义.**

- `[]` 已经是一个值.
- 如果 `e1` 求值为 `v1`,  `e2` 求值为 `v2`，那么 `e1::e2` 的求值就是 `v1::v2`.

因为这些规则以及如何对方括号表示法进行 desugar，因此我们有以下派生规则：

- 如果 `ei` 对 `1..n` 中所有 `i` 求值为 `vi`，那么 `[e1; ...; en]` 的值为 `[v1; ...; vn]`.

在我们所有的求值规则中写 “求值为” 已经变得很乏味。 因此，让我们为其引入一个简短的表示法。 我们将  `e ==> v`  表示为 `e` 求值为 `v`。请注意，`==>` 不是 OCaml 语法的一部分。 相反，这是我们在语言描述中使用的一种符号，有点像元变量。 使用该表示法，我们可以重写上面的后两个规则：

- 如果 `e1 ==> v1`, 且 `e2 ==> v2`，那么 `e1::e2 ==> v1::v2`.
- 如 对 `1..n` 中所有 `i` 有 `ei ==> vi`，则 `[e1; ...; en] ==> [v1; ...; vn]`.

**静态语义.**

列表中的所有元素必须具有相同的类型。 如果该元素类型为 `t`，则列表的类型为 `t list`。 您应该从右到左阅读这种类型：`t list` 是 `t` 的列表，`t list list` 是 `t` 的列表的列表，等等。这里的单词 `list` 本身不是一个类型：OCaml 中无法建立一个类型是 `list` 的值。 相反，`list` 是一个类型构造器：给定一个类型，它将产生一个新类型。 例如，给定 `int`，它将产生 `int list` 类型。 您可以认为类型构造器就像是对类型进行操作的函数，而不是对值进行操作的函数。

类型检查规则:

- `[] : 'a list`
- 如果 `e1: t` 且 `e2: t list`，那么 `e1::e2: t list`.

在 `[]` 的规则中，请记住 `'a` 是类型变量：它代表一个未知类型。 因此，空列表是一个其元素具有未知类型的列表。 如果我们将 `int` 添加到它上面, 比如 `2::[]`，则编译器会推断出该特定列表的 `'a` 必然是 `int`。 但是，如果在另一个地方我们将布尔值添加到它上面, 比如 `true::[]`，则编译器会推断出该特定列表的 `'a` 必然是 `bool`。



# 3.1.1.2. Accessing Lists

实际上，只有两种方法可以建立列表，即使用 nil 和 cons。 因此，如果我们想将列表分解成各个组成部分，则必须阐明列表为空时怎么办，列表不为空时怎么办（即 cons 一个元素到另一个列表）。 我们使用称为模式匹配的语言特性来做到这一点。

这是一个使用模式匹配来计算列表总和的示例：

```ocaml
let rec sum lst =
  match lst with
  | [] -> 0
  | h::t -> h + sum t
```

该函数说的是取输入 `lst` 并查看它是否具有与空白列表相同的形态。 如果是，则返回0。否则，如果它的形状与列表 `h :: t` 相同，则让 `h` 为 `lst` 的第一个元素，让  `t` 为 `lst` 的其余元素，并返回 `h + sum t` 。 这里变量名的建议选择 “ head” 和 “ tail”，这是一个常见的习惯用法，但是如果需要，我们可以使用其他名称。 另一个常见的习惯用法是：

```ocaml
let rec sum xs =
  match xs with
  | [] -> 0
  | x::xs' -> x + sum xs'
```

即，输入列表是一个 xs（发音为EX-uhs）的列表，头部元素是 x，而尾部列表是 xs'（发音为EX-uhs质数）。

这是使用模式匹配来计算列表长度的另一个示例：

```ocaml
let rec length lst =
  match lst with
  | [] -> 0
  | h::t -> 1 + length t
```

注意，在模式匹配的右侧我们实际上并不需要变量 `h`。 当我们想在模式中指示某个值的存在但又不想为其命名时，我们可以写 `_`（下划线字符）：

```ocaml
let rec length lst =
  match lst with
  | [] -> 0
  | _::t -> 1 + length t
```

这是第三个示例，它将一个列表追加到另一个列表的开头：

```ocaml
let rec append lst1 lst2 =
  match lst1 with
  | [] -> lst2
  | h::t -> h::(append t lst2)
```

例如，`append [1;2] [3;4]` 是 `[1;2;3;4]`。 该函数实际上可以作为内置运算符 `@` 使用，因此我们可以改写 `[1;2] @ [3;4]`。

作为最后一个示例，我们可以编写一个函数来确定列表是否为空：

```ocaml
let empty lst =
  match lst with
  | [] -> true
  | h::t -> false
```

但是还有有一种更简单的方法可以在不进行模式匹配的情况下编写相同的函数：

```ocaml
let empty lst =
  lst = []
```

请注意，以上所有递归函数如何类似于对自然数进行归纳证明：每个自然数要么是 0 要么是比其他某个自然数 n 大 1 的数，因此归纳证明有一个为 0 的基本情况和一个 n + 1 的归纳情况。 同样，我们的所有函数都有一个空列表的基本情况，一个比其他某个列表多一个元素的列表的递归情况。 这种相似性并非偶然。 归纳和递归之间有着很深的关系。 我们将在本课程的后面部分详细探讨这种关系。

顺便说一下，有两个库函数 `List.hd` 和 `List.tl` 分别返回列表的头部和尾部。 将这些直接应用于列表不是很好，不是 OCaml 中的习惯用法。 问题在于，当将它们应用于空列表时，它们将引发异常，并且您必须记得处理该异常。 相反，您应该使用模式匹配：然后您不得不同时匹配空列表和非空列表（至少），这将防止引发异常，从而使程序更加健壮。



# 3.1.1.3. Mutating Lists

列表是不可变的。 无法将列表的元素从一个值更改为另一个值。 实际上是从旧列表中创建新的列表。 例如，假设我们要编写一个函数，该函数返回与其输入列表相同的列表，但是第一个元素（如果有）加 1。我们可以这样做：

```ocaml
let inc_first lst =
  match lst with
  | [] -> []
  | h::t -> (h+1)::t
```

现在您可能会担心我们是否在浪费空间。毕竟，编译器至少可以通过两种方式实现上述代码：

1. 在以 cons 匹配的模式中创建新列表时，复制整个尾部列表 `t`，然后内存使用总量将增加 与 `t` 的长度成比例的量。
2. 在旧列表和新列表之间共享尾部列表 `t`，然后内存使用总量不会增加多少（除了需要额外存储 `h+1` 的内存量）。

实际上，编译器执行后者。因此，无需担心。编译器实现共享非常安全的原因是列表元素是不可变的。如果它们是可变的，那么我们将开始担心我的列表是否与您的列表之间有共享，以及我所做的更改在您的列表中是否可见。因此，不变性使代码推理变得更容易，也使编译器很安全地执行优化。



# 3.1.1.4. Pattern Matching with Lists

上面我们看到了如何使用模式匹配来访问列表。 让我们更仔细地看一下此功能。

## 语法和语义

**语法.**

```ocaml
match e with
| p1 -> e1
| p2 -> e2
| ...
| pn -> en
```

每个 `pi -> ei` 子句被称为一个 *分支* 或模式匹配的一种 *情形*(case)。 整个模式匹配中的第一个竖杠是可选的。

这里的 `p` 是一种称为 *模式* 的新语法形式。 目前，模式可能包含：

- 一个变量名称，例如 `x`
- 下划线字符  `_`，称为通配符
- 空列表 `[]`
- `p1::p2`
- `[p1; ...; pn]`

变量名在一个模式中不得出现多次。 例如，模式 `x::x`是非法的。 通配符可以出现多次。

随着我们了解更多的数据结构，我们将扩展模式的可能性。

**动态语义.**

在讲座中，我们给出了动态语义的简略版本。 在这里，我们提供完整的细节。

模式匹配涉及两个相互关联的任务：确定模式是否匹配一个值，以及确定值的哪些部分应与模式中的哪些变量相关联。 前一个任务是直观地确定模式和值是否具有相同的形状。 后一个任务是确定模式引入的变量绑定。 例如，在

```ocaml
match 1::[] with
| [] -> false
| h::t -> (h>=1) && (length t = 0)
```

（求值为 `true`）（在计算第二个分支的右侧时，`h=1` 和 `t=[]`）。让我们写 `h-> 1` 表示变量绑定，也就是说 `h` 的值为 `1`；这不是 OCaml 语法的一部分，而是我们用来推理该语言的一种表示法。因此，第二个分支产生的变量绑定将为 `h->1`，`t->[]`。

更仔细地讲，这是模式何时匹配值以及匹配的绑定产生的定义：

- 模式 `x` 匹配任何值 `v` 并产生变量绑定: `x -> v`。
- 模式 `_` 匹配任何值，并且不产生绑定。
- 模式 `[]` 与值 `[]` 匹配，并且不产生任何绑定。
- 如果 `p1` 匹配 `v1` 并生成绑定集 $b_1$，并且 `p2` 匹配 `v2` 并生成绑定集 $b_2$，则 `p1::p2` 匹配 `v1::v2` 并生成绑定集 $b_1∪b_2$。请注意，`v2` 必须是一个列表（因为它位于 `::` 的右侧），并且可以具有任何长度：0个元素，1个元素或更多元素。请注意，绑定的并集 $b_1∪b_2$ 永远不会有问题，因为 OCaml 在语法上限制了一个变量不可能同时出现在模式绑定 $b_1$和 $b_2$ 中。
- 如果对于 `1..n` 中的所有 `i`，都有 `vi` 匹配 `pi` 并产生绑定集 $b_i$，则 `[p1; ...; pn]` 将匹配 `[v1; ...; vn]` 并产生绑定集 $∪_i b_i$。请注意，此模式必须指定列表的确切长度。

现在我们可以说如何求值 `match e with p1 -> e1 | ... | pn -> en` 了:

- `e` 求值为 `v`.
- 尝试将 `v` 与 `p1` 进行匹配，与 `p2` 匹配 ... 按照匹配表达式出现的顺序依此类推。
- 如果 `v` 与任何模式都不匹配，则对匹配表达式的求值将引发 `Match_failure` 异常。 我们尚未在 OCaml 中讨论异常，但是您已经从 CS 1110（Python）和 CS 2110（Java）中了解过它们了。 在介绍 OCaml 中的其他一些内置数据结构之后，我们将回来讨论异常。
- 否则，在第一次成功匹配某个模式时，停止尝试(后面的)匹配。 令 `pi` 为该模式，令 $b$ 为 `v` 匹配 `pi` 而产生的变量绑定。
- 在 `ei` 内部替换这些绑定，产生一个新的表达式 `e'`。
- 对 `e'` 求值得到 `v'`.
- 整个匹配表达式的结果就是 `v'`。

例如，以下是该匹配表达式的求值方式：

```ocaml
match 1::[] with
| [] -> false
| h::t -> (h=1) && (t=[])
```

- `1::[]` 已经是一个值了
- `[]` 不匹配 `1::[]`
- `h::t` 匹配 `1::[]` 并产生变量绑定 `{h->1,t->[]}`, 因为:
  - `h` 匹配 `1` 并产生变量绑定 `h->1`
  - `t` 匹配 `[]` 并产生变量绑定 `t->[]`
- 在 `(h=1) && (t=[])` 内部用 `{h->1,t->[]}` 进行替换并得到一个新的表达式: `(1=1) && ([]=[])`
- 对这个新表达式 `(1=1) && ([]=[])` 求值得到 `true` (我们在这里省略了该事实的理由，但是它遵循了内置运算符和函数应用的其他求值规则)
- 因此整个匹配表达式的值就是 `true`.

**静态语义.**

- 如果 `e:ta` 并且对于所有 `i` 都包含 `pi:ta` 和 `ei:tb`, 则 `(match e with p1 -> e1 | ... | pn -> en): tb`.

该规则取决于能够判断模式是否具有特定类型。 像往常一样，类型推断在这里起作用。 OCaml 编译器会推断任何模式变量的类型以及所有出现的通配符模式。 至于列表模式，它们具有与列表表达式相同的类型检查规则。

## 额外的静态检查

除了类型检查规则之外，编译器还对每个匹配表达式进行其他两项检查：

- **Exhaustiveness:** 无论该表达式在运行时的值如何，编译器都会检查以确保有足够的模式保证至少有会有一个模式与表达式 `e` 匹配。 这样可以确保程序员不会忘记任何分支。 例如，下面的函数将导致编译器发出警告：

  ```ocaml
  # let head lst = match lst with h::_ -> h;;
  Warning 8: this pattern-matching is not exhaustive.
  Here is an example of a value that is not matched:
  []
  ```

  通过向程序员显示警告，编译器正在帮助程序员防御运行时可能的 `Match_failure` 异常。

- **Unused branches:** 编译器检查是否有前面的某个分支一定会匹配而导致后面的其他分支永远都不会匹配(后面的分支就没有意义了)。 例如，下面的函数将导致编译器发出警告：

  ```ocaml
  # let rec sum lst =
      match lst with
      | h::t -> h + sum t
      | [h] -> h
      | [] -> 0;;
  Warning 11: this match case is unused.
  ```

  第二个分支未使用，因为第一个分支将匹配第二个分支匹配的任何内容。

  未使用的匹配 case 通常表示程序员编写了超出预期的内容。 因此，通过发出警告，编译器正在帮助程序员检测其代码中的潜在错误。

  这是一个导致出现未使用匹配 case 警告的最常见错误之一的示例。 理解它也是检验您对匹配表达式动态语义的理解的一种好方法：

  ```ocaml
  let length_is lst n =
    match length lst with
    | n -> true
    | _ -> false
  ```

  程序员认为，如果 `lst`  的长度等于 `n`，则此函数将返回 `true`，否则将返回 `false`。 但实际上，此函数始终返回 `true`。 为什么？ 因为模式变量 `n` 与函数参数 `n` 不同。

假设 `lst` 的长度为5。则模式匹配变为：`match 5 with n -> true | _ -> false`。 `n` 是否匹配5？ 是的，根据上述规则：一个变量模式将匹配任何值，并在此处产生绑定 `n->5`。 然后应用该绑定求值得到 `true`，用 5 替换掉 `true` 内部所有出现的 `n`。然而，`true` 内部并没 `n` 出现。 至此我们完成了整个求值, 结果就是 `true`。

程序员真正要想写的是：

  ```ocaml
  let length_is lst n =
    match length lst with
    | m -> if m=n then true else false
    | _ -> false
```

或更好：

  ```ocaml
  let length_is lst n =
    match length lst with
    | m -> m=n
    | _ -> false
```

甚至更好：

  ```ocaml
  let length_is lst n =
    length lst = n
  ```

## 深度模式匹配

模式可以嵌套。 这样做可以使您的代码深入查看列表结构。 例如：

- `_::[]` 匹配只有一个元素的列表
- `_::_` 将与至少有一个元素的列表匹配
- `_::_::[]` 匹配有两个元素的列表
- `_::_::_::_` 将与至少有三个元素的列表匹配



# 3.1.1.5. Tail Recursion

如果函数以递归方式调用自身，但在递归调用返回后不执行任何计算，并立即将其递归调用的值返回给调用者，则该函数为 *尾递归 * 函数。 考虑这两个实现，即对列表求和的 `sum` 和 `sum_tr`，我们在其中提供了一些类型标注来帮助您理解代码：

```ocaml
let rec sum (l : int list) : int =
  match l with
    [] -> 0
  | x :: xs -> x + (sum xs)

let rec sum_plus_acc (acc : int) (l : int list) : int =
  match l with
    [] -> acc
  | x :: xs -> sum_plus_acc (acc + x) xs

let sum_tr : int list -> int =
  sum_plus_acc 0
```

请观察上面的 `sum` 和 `sum_tr` 函数之间的以下区别：在 `sum` 函数（非尾递归）中，递归调用返回值后，我们将 `x` 加到它上面。在 `sum_tr` (尾递归) 中，或者在 `sum_plus_acc` 中，递归调用返回之后，我们无需进一步计算就立即返回了该值。

我们为什么要关心尾递归？实际上，有时函数式程序员对此过于痴迷。如果您只关心编写函数的初稿，则可能无需担心。

但是，如果要在非常长的列表上编写函数，则尾递归对于性能至关重要。回顾 CS 1110 中的一个调用堆栈，该堆栈是一个栈（具有推入和弹出操作的数据结构），其中每个已开始但尚未完成的函数调用都有一个元素。每个元素都存储诸如局部变量的值之类的东西，以及该函数尚未完成求值的部分。当在一个函数体中的求值需要调用另一个函数时，新元素将被压入调用堆栈，并在被调用函数完成时弹出。

当函数对其自身进行递归调用，并且在被调用方返回之后，调用者无需执行其他操作，这种情况称为**尾调用**。诸如 OCaml 之类的函数式语言（甚至是诸如 C++ 之类的命令式语言）通常都包含一个非常有用的优化：当一个函数是尾部调用时，调用方的栈帧在函数调用前直接被弹出 — 被调用方的栈帧会替换掉调用方的栈帧。这是有意义的：无论如何，调用者将直接返回被调用者的结果。通过这种优化，递归调用有时可以和命令式语言中的 while 循环一样高效（此类循环不会使调用栈变大。）这里的 “有时” 恰好是尾调用的时候，您和编译器都可以（经常）弄清楚。通过尾调用优化，可以将递归算法的空间性能从 O(n) 降低到 O(1)，即从每次调用一个栈帧变成所有调用一个栈帧。

因此，当您在使用尾递归函数与非尾递归函数之间进行选择时，最好在很长的列表上使用尾递归函数以实现空间效率。为此，List 模块记录了哪些函数是尾递归的，哪些不是。

但这并不意味着尾递归的实现是绝对地更好。例如，尾递归函数可能更难阅读（考虑 `sum_plus_acc`）。 此外，在某些情况下，实现尾递归函数必须进行预处理或后处理才能反转列表。在中小型列表上，反转列表的开销（在时间以及内存分配上）都会使尾递归版本的时间效率降低。什么构成 “小”与“大”？这很难说，但是根据 [`List` 模块标准库文档](http://caml.inria.fr/pub/docs/manual-ocaml/libref/List.html)，也许 10,000 是一个很好的估计。



# 3.1.1.6. More List Syntax

这是与列表和模式匹配有关的另外几句语法。

**立即匹配:**

当您有一个可以立即与其最终参数进行模式匹配的函数时，可以使用一段不错的语法糖来避免编写额外的代码。 这是一个例子：

相比于:

```ocaml
let rec sum lst =
  match lst with
  | [] -> 0
  | h::t -> h + sum t
```

你可以这样:

```ocaml
let rec sum = function
  | [] -> 0
  | h::t -> h + sum t
```

`fuction` 是关键字。请注意，我们可以省去包含 `match` 和参数名称的那行。但是，在这种情况下，在规范注释中为函数记录该参数的含义尤为重要，因为代码不再为其提供描述性的名称。

**OCamldoc:**

OCamldoc 是类似于 Javadoc 的文档生成器。它从源代码中提取注释并生成 HTML（以及其他输出格式）。例如，[List 模块标准库 Web 文档](http://caml.inria.fr/pub/docs/manual-ocaml/libref/List.html)就是 OCamldoc 从该模块的标准库源代码生成的 。在本课程中，我们不会过于强调 OCamldoc，但出于某些原因，我们确实在这里提出了它：其中有一个语法约定，可能会与列表有所混淆。

在 OCamldoc 注释中，源代码用方括号括起来。该代码将以打字机的字体及语法高亮的形式在输出 HTML 中显示。在这种情况下，方括号不表示列表。

例如，这是源代码中 `List.hd` 的注释：

```ocaml
(** Return the first element of the given list. Raise
   [Failure "hd"] if the list is empty. *)
```

`[Failure "hd"]` 并不表示包含 `Failure "hd"` 的列表。 而是表示将表达式 `Failure "hd"` 排版为源代码，如您在此处看到的。

当您想将列表作为文档的一部分讨论时，这可能会特别令人困惑。 例如，这是一种我们可以重写该注释的方法：

```ocaml
(** returns:  the first element of [lst].
  * raises:  [Failure "hd"] if [lst = []].
  *)
```

在 `[lst = []]` 中, 外部方括号指示源代码作为注释的一部分，而内部方括号指示空列表。

**列表推导:**

包括 Python 和 Haskell 在内的某些语言都具有被称为 *comprehension* 的语法，该语法允许将列表写成类似于数学中的集合推导。最早的 comprehensions 例子似乎是1977年设计的函数式语言 NPL。OCaml 并没有内置的语法支持来 comprehensions，尽管这并没有使该语言的表达能力降低：comprehensions 可以做的一切都可以用更基础(primitive)的语言特性来完成。 而且，在本课程中我们将不会使用 comprehensions，因此您可以无视此小节的其余部分。

*截至 2019年秋季，以下说明目前不起作用;  `ppx_monadic` 软件包（及其依赖项）尚未更新为可与 OCaml 4.08.x 一起使用。 我们将其留在此处以供将来参考，希望有一天能恢复支持。*

通过 `ppx_monadic` 软件包可以获得对它们(comprehensions)的支持。 使用以下命令安装它：

```ocaml
opam install -y ppx_monadic
```

然后在utop中，您可以用类似Haskell的表示法编写 comprehensions：

```ocaml
# #require "ppx_monadic";;
# [%comp x+y || x <-- [1;2;3]; y <-- [1;2;3]; x<y];;
- : int list = [3; 4; 5]
```



# 3.1.2. Variants

*变体* 是一种用来表示一个属于几种可能值之一的数据类型。 简单来说，变体就像 C 或 Java 的枚举：

```ocaml
type day = Sun | Mon | Tue | Wed | Thu | Fri | Sat
let d:day = Tue
```

在 OCaml 中变体的各个值的名称叫做 *构造器*。 在上面的示例中，构造器是 `Sun`, `Mon`, 等。这与 C++ 或 Java 中构造器的用法有些不同。

对于 OCaml 中的每种数据类型，我们一直在讨论如何构建和访问它。 对于变体，构建很容易：只需编写构造器的名称。 为了进行访问，我们使用模式匹配。 例如：

```ocaml
let int_of_day d =
  match d with
  | Sun -> 1
  | Mon -> 2
  | Tue -> 3
  | Wed -> 4
  | Thu -> 5
  | Fri -> 6
  | Sat -> 7
```

没有任何一种将构造器名称映射到 `int` 的自动方法，带有枚举的语言也一样。

**语法.**

定义变体类型:

```ocaml
type t = C1 | ... | Cn
```

构造器名称必须以大写字母开头。 OCaml 使用它来区分构造器和变量标识符。

编写构造器值的语法就是其名称，例如 `C`。

**动态语义.**

- 构造器已经是一个值。 没有需要执行的计算。

**静态语义.**

- 如果 `t` 定义为 `type t = ... | C | ...` 的类型，那么 `C : t`。

假设有两种类型定义使用了重叠的构造器名称，例如，

```ocaml
type t1 = C | D
type t2 = D | E
let x = D
```

当 `D` 在这些定义之后出现时，它指的是哪种类型？ 也就是说，上面的 `x` 是什么类型？ 答案是后定义的类型会获胜。 所以 `x：t2` , 这对于程序员来说可能是令人惊讶的，因此在任何给定的范围内（例如，一个文件或一个模块，尽管我们还没有介绍模块），以一些区别字符作为重叠构造器名称的前缀，是习惯用法。 例如，假设我们正在定义表示神奇宝贝的类型：

```ocaml
type ptype =
  TNormal | TFire | TWater

type peff =
  ENormal | ENotVery | ESuper
```

因为 “Normal” 自然是是神奇宝贝以及其攻击效率的种类的构造器名称，所以我们在每个构造器名称的前面添加一个额外的字符，以指示它是宝贝类别的类型还是攻击效率类别的类型。

**模式匹配.**

正如我们在上一讲中所说的，每次我们引入一种新的数据类型时，我们都需要引入与之相关的新模式。 对于变体，这很容易。 我们将以下新模式形式添加到合法模式列表中：

- 一个构造器名称 `C`

并且我们扩展了模式何时与值匹配并产生绑定的定义，如下所示：

- 模式 `C` 匹配值 `C`，不产生绑定。

**注意.** 变体比我们在这里看到的强大得多。 我们将尽快再次与回到他们那儿。



# 3.1.3. Unit Testing with OUnit

*这一部分与我们对数据类型的研究有所不同，但是这是绕开它的好地方：我们现在已经足够了解如何在 OCaml 中完成单元测试，并且已经没有更好的借口来继续等待了解它了。*

使用 toplevel 测试函数仅适用于非常小的程序。较大的程序需要有包含许多 *单元测试* 的 *测试套件*，并且每次我们更新代码库时都可以重新运行。单元测试是对程序中一小部分功能（例如单个函数）的测试。

现在，我们已经学习了足够的 OCaml 特性，以了解如何使用名为 OUnit 的库进行单元测试。它是一种流行的测试框架（在 [OCaml Forge](https://forge.ocamlcore.org/) 上的下载中排名第一），类似于 Java 中的 JUnit，Haskell 中的 HUnit 等。使用 OUnit 的基本工作流程如下：

- 在文件 `f.ml` 中编写一个函数。该文件中也可能还有许多其他函数。
- 在一个单独的文件 `f_test.ml` 中编写该函数的单元测试。带有下划线和“test”的确切名称实际上并不是必需的，但这是在 3110 中经常遵循的约定。
- Build 并运行 `f_test.byte` 以执行单元测试。

如果您想阅读 [OUnit 文档](http://ounit.forge.ocamlcore.org/api-ounit/index.html)，则可以使用它。



# 3.1.3.1. An Example of OUnit

以下示例显示了如何创建 OUnit 测试套件。 该示例中的某些内容起初看起来似乎很神秘； 它们将在下一节中被讨论。

创建一个名为 `sum.ml` 的文件，并将以下代码放入其中：

```ocaml
let rec sum = function
  | []    -> 0
  | x::xs -> x + sum xs
```

现在创建另一个名为 `sum_test.ml` 的文件，并将以下代码放入其中：

```ocaml
open OUnit2
open Sum

let tests = "test suite for sum" >::: [
  "empty"  >:: (fun _ -> assert_equal 0 (sum []));
  "one"    >:: (fun _ -> assert_equal 1 (sum [1]));
  "onetwo" >:: (fun _ -> assert_equal 3 (sum [1; 2]));
]

let _ = run_test_tt_main tests
```

（根据您使用的编辑器，现在您可能会看到有关 OUnit2 和 Sum 的一些错误。暂时忽略这些错误：您的代码正确，但是您的编辑器尚不了解。我们稍后将通过创建一个 `.merlin` 文件来修复它。）

最后，运行以下命令：

```shell
$ ocamlbuild -pkgs oUnit sum_test.byte
$ ./sum_test.byte
```

您将得到如下响应：

```ocaml
...
Ran: 3 tests in: 0.12 seconds.
OK
```

现在假设我们修改 `sum.ml` 通过将其中的代码更改为以下内容来引入一个 bug：

```ocaml
let rec sum = function
  | []    -> 1  (* bug *)
  | x::xs -> x + sum xs
```

如果我们重新构建并执行 `sum_test.byte`，则所有测试用例现在都会失败。 输出告诉我们失败案例的名称。 这是输出的开始，其中我们用 `...` 替换了一些依赖于您本地计算机的字符串：

```ocaml
FFF
==============================================================================
Error: test suite for sum:2:onetwo.

File ".../_build/oUnit-test suite for sum-...#01.log", line 8, characters 1-1:
Error: test suite for sum:2:onetwo (in the log).

Called from unknown location

not equal
------------------------------------------------------------------------------
```

输出的第一行

```ocaml
FFF
```

告诉我们 OUnit 运行了三个测试用例，都失败了。

接着有趣的一行

```ocaml
Error: test suite for sum:2:onetwo.
```

告诉我们，在名为 `test suite for sum` 的测试套件中，在索引2处名为 `onetwo` 的测试用例失败了。 该测试用例的其余输出并不是特别有趣。 让我们暂时忽略它。



# 3.1.3.2. Explanation of the OUnit Example

让我们更仔细地研究上一节中所做的事情。
我们有一个名为 `sum.ml` 的源文件，其中包含以下代码：

```ocaml
let rec sum = function
  | []    -> 0
  | x::xs -> x + sum xs
```

还有一个名为 `sum_test.ml` 的测试文件，其代码如下：

```ocaml
open OUnit2
open Sum

let tests = "test suite for sum" >::: [
  "empty"  >:: (fun _ -> assert_equal 0 (sum []));
  "one"    >:: (fun _ -> assert_equal 1 (sum [1]));
  "onetwo" >:: (fun _ -> assert_equal 3 (sum [1; 2]));
]

let _ = run_test_tt_main tests
```

在测试文件中，`open OUnit2` 将 OUnit2（OUnit框架的v2版本）中的许多定义引入到作用域。`open Sum` 将 `sum.ml` 的定义引入作用域。 在本课程的后面，我们将学习有关作用域和 `open` 关键字的更多信息。

然后，我们创建了一个测试用例列表：

```ocaml
[
  "empty"  >:: (fun _ -> assert_equal 0 (sum []));
  "one"    >:: (fun _ -> assert_equal 1 (sum [1]));
  "onetwo" >:: (fun _ -> assert_equal 3 (sum [1; 2]));
]
```

每行代码都是一个单独的测试用例。 每个测试用例用一个字符串给它一个描述性的名字，以及一个作为测试用例运行的函数。 在名称和函数之间，我们编写 `>::`，这是由 OUnit 框架自定义的运算符。 让我们看下上面的第一个函数：

```ocaml
fun _ -> assert_equal 0 (sum [])
```

每个测试用例函数都接受一个在 OUnit 中叫做测试上下文的参数作为输入。 在这里（以及我们编写的许多测试用例中），我们实际上不需要担心上下文，因此我们使用下划线指示该函数忽略其输入。 然后，该函数调用 `assert_equal`，这是 OUnit 提供的函数，用于检查其两个参数是否相等。 如果相等则测试用例成功。 如果不，则测试用例失败。

然后我们创建了一个测试套件：

```ocaml
let tests = "test suite for sum" >::: [
  "empty"  >:: (fun _ -> assert_equal 0 (sum []));
  "one"    >:: (fun _ -> assert_equal 1 (sum [1]));
  "onetwo" >:: (fun _ -> assert_equal 3 (sum [1; 2]));
]
```

`>:::` 运算符是另一个 OUnit 自定义运算符。 它介于测试套件名称和该套件的测试用例列表之间。

然后我们运行测试套件：

```ocaml
let _ = run_test_tt_main tests
```

函数 `run_test_tt_main` 由 OUnit 提供。 它运行一个测试套件，并将哪些测试用例通过哪些不通过的结果打印到标准输出。 在这里使用下划线表示我们不在乎函数返回的值。 它只是被丢弃。

最后，当我们编译测试文件时，我们链接到 OUnit 包中，该包的大小写有些不同寻常（某些平台在乎，而另一些平台则是不可知的）：

```shell
$ ocamlbuild -pkgs oUnit sum_test.byte
```

如果您厌倦了输入 `pkgs oUnit` 部分，则可以在同一目录中创建一个名为 `_tags`（注意下划线）的文件，并将以下内容放入其中：

```ocaml
true: package(oUnit)
```

现在，每次在此目录中进行编译时，Ocamlbuild 都会自动链接 OUnit，而无需给出 `pkgs` 标志。 代价是是您现在必须将不同的标志传递给 Ocamlbuild：

```shell
$ ocamlbuild -use-ocamlfind sum_test.byte
```

只要 `_tags` 文件存在，您就将继续传递该标志。 为什么这样更好呢？ 因为, 如果要链接的程序包很多，则使用标记文件最终在命令行中只需要传递一个选项，而不是多个。



# 3.1.3.3. Improving OUnit Output

在我们 buggy 式实现的示例中，得到了以下输出：

```ocaml
==============================================================================
Error: test suite for sum:2:onetwo.

File ".../_build/oUnit-test suite for sum-...#01.log", line 8, characters 1-1:
Error: test suite for sum:2:onetwo (in the log).

Called from unknown location

not equal
------------------------------------------------------------------------------
```

让我们看看如何改善输出以提供更多信息。

## 堆栈跟踪

`Called from an unknown location`  表示 OCaml 无法提供堆栈跟踪。 发生这种情况是因为，默认情况下堆栈跟踪是被禁用了的。 我们可以通过使用 debug 标记编译代码来启用它们：

```shell
$ ocamlbuild -pkgs oUnit -tag debug sum_test.byte
$ ./sum_test.byte

==============================================================================
Error: test suite for sum:2:onetwo.

File "/Users/clarkson/tmp/sum/_build/oUnit-test suite for sum-...#01.log", line 9, characters 1-1:
Error: test suite for sum:2:onetwo (in the log).

Raised at file "src/oUnitAssert.ml", line 45, characters 8-27
Called from file "src/oUnitRunner.ml", line 46, characters 13-26

not equal
------------------------------------------------------------------------------
```

现在我们看到了由 `assert_equal` 产生异常引起的堆栈跟踪。 您可能会同意，堆栈跟踪还不是十分有用(informative)：重要的是哪个测试用例失败，而不是 OUnit 实现中的哪些文件引发了异常。 我们已经可以从输出的第一行中识别出失败的测试用例。 （这是一个名为 `onetwo` 的测试用例，位于第2个名为 `test suite for sum` 的测试套件中。）

因此，我们通常不必为 OUnit 测试套件启用堆栈跟踪。 但是，如果您自己的代码引发了异常想要跟踪，它可能会很有用。

## 输出值

OUnit 输出中的 `not equal` 意味着 `assert_equal` 发现在该测试用例中传递给它的两个值不相等。这并不是那么有用(informative)：我们想知道为什么它们不相等。 特别是，我们想知道 `sum` 所产生的实际输出是该测试用例的结果。 为了找出答案，我们需要传递另一个参数给 `assert_equal`。 该参数（其标签是 `printer`）应该是一个可以将输出转换为字符串的函数。 在这种情况下，输出是整数，因此 Stdlib 模块中的 `string_of_int` 就足够了。 我们将测试套件修改如下：

```ocaml
let tests = "test suite for sum" >::: [
  "empty"  >:: (fun _ -> assert_equal 0 (sum []) ~printer:string_of_int);
  "one"    >:: (fun _ -> assert_equal 1 (sum [1]) ~printer:string_of_int);
  "onetwo" >:: (fun _ -> assert_equal 3 (sum [1; 2]) ~printer:string_of_int);
]
```

现在我们得到了更多输出信息：

```ocaml
==============================================================================
Error: test suite for sum:2:onetwo.

File "/Users/clarkson/tmp/sum/_build/oUnit-test suite for sum-sauternes#01.log", line 8, characters 1-1:
Error: test suite for sum:2:onetwo (in the log).

Called from unknown location

expected: 3 but got: 4
------------------------------------------------------------------------------
```

该输出意味着名为 `onetwo` 的测试断言 `3` 和 `4` 相等。预期输出为 `3`，因为这是 `assert_equal` 的第一个输入，并且该函数的规范指出，在 `assert_equal x y`中，您（作为测试人员）期望得到的输出应该是 `x`，被测试的函数实际产生的输出应该是 `y`。

注意我们的测试套件如何累积大量冗余代码。 特别是，我们必须将 `printer` 参数添加到这几行中。 让我们通过分解构造测试用例的函数来改进该代码：

```ocaml
let make_sum_test name expected_output input =
  name >:: (fun _ -> assert_equal expected_output (sum input) ~printer:string_of_int)

let tests = "test suite for sum" >::: [
  make_sum_test "empty" 0 [];
  make_sum_test "one" 1 [1];
  make_sum_test "onetwo" 3 [1; 2];
]
```

对于比整数更复杂的输出类型，最终您将需要编写自己的函数以传递给  `printer`。 这类似于 Java 中编写 `toString()` 方法：对于您自己定义的复杂类型，该语言不知道如何将它们呈现为字符串。 您必须提供执行此操作的代码。



# 3.1.3.4. OUnit and Files

创建更多文件可以使 OUnit 的使用更加愉快。

## 标签文件

编译 OUnit 测试文件，我们必须指定 OUnit 的链接：

```shell
$ ocamlbuild -pkgs oUnit sum_test.byte
```

如果您厌倦了输入 `pkgs oUnit` 部分，则可以在同一目录中创建一个名为 `_tags`（注意下划线）的文件，并将以下内容放入其中：

```ocaml
true: package(oUnit)
```

现在，每次在此目录中进行编译时，Ocamlbuild 都会自动链接 OUnit，而无需给出 `pkgs` 标记。 代价是您必须将不同的标记传递给 Ocamlbuild：

```shell
$ ocamlbuild -use-ocamlfind sum_test.byte
```

只要 `_tags` 文件存在，您就将继续传递该标记。 为什么这样更好呢？ 因为, 如果要链接的程序包很多，则使用标记文件最终在命令行中只需要传递一个选项，而不是多个。

## Merlin 文件

如果您使用的是 Merlin（例如，您的编辑器是 VS Code 或 Emacs），那么您会注意到有两点在目前还不是很理想。 首先，Merlin 不了解 OUnit 代码。 其次，当您编辑 `sum_test.ml` 时，Merlin 并不理解位于 `sum.ml` 中的代码。 要解决这两个问题，请在同一目录中创建一个文件，并将其命名为 `.merlin`。 （请注意该文件名中的前导点。）在该文件中放入以下内容：

```ocaml
B _build
PKG oUnit
```

第一行告诉 Merlin 从 `_build`目录中的其他源文件中查找编译的代码，这是 Ocamlbuild 放置编译代码的位置。 第二行告诉 Merlin 查找 oUnit 软件包。 您可能需要重新启动编辑器才能使这些更改生效。 之后，您必须使用 Ocamlbuild 编译代码，以便 Merlin 在 `_build` 目录中找到已编译的代码。



# 3.1.3.5. Test-driven Development

编写代码后，通常不必严格进行测试。 但在测试驱动开发（TDD）中，测试是第一位的！ 它强调代码的 *增量* 开发：总有一些可以测试的东西。 测试不是在代码实现后发生的事情； 相反， *持续测试* 用于及早发现错误。 因此，在编写代码时立即开发单元测试非常重要。 自动化测试套件至关重要，因此持续测试基本上不需要任何努力。

这是 TDD 的示例。 我们特意选择一个非常简单的函数来实现，以使流程清晰明了。 假设我们正在使用星期的数据类型：

```ocaml
type day = Sunday | Monday | Tuesday | Wednesday
         | Thursday | Friday | Saturday
```

我们想编写一个函数 `next_weekday：day -> day`，该函数返回给定日期之后的下一个工作日。 首先，我们可以编写该功能的最基本的 残破版本：

```ocaml
let next_weekday d = failwith "Unimplemented"
```

然后，我们编写我们可以想象的最简单的单元测试。 例如，我们知道星期一之后的下一个工作日是星期二。 因此，我们添加了一个测试：

```ocaml
let tests = "test suite for next_weekday" >::: [
  "tue_after_mon"  >:: (fun _ -> assert_equal (next_weekday Monday) Tuesday);
]
```

然后，我们运行 OUnit 测试套件。 失败，正如预期的那样。 很好！ 现在我们有了一个具体的目标，那就是使该单元测试通过。 我们对 `next_weekday` 进行修改以实现这一目标：

```ocaml
let next_weekday d =
  match d with
  | Monday -> Tuesday
  | _ -> failwith "Unimplemented"
```

我们编译并运行测试； 它通过了。 是时候添加更多测试了。 剩下可能最简单的是仅涉及工作日而不是周末的测试。 因此，让我们添加工作日的测试。

```ocaml
let tests = "test suite for next_weekday" >::: [
  "tue_after_mon"  >:: (fun _ -> assert_equal (next_weekday Monday) Tuesday);
  "wed_after_tue"  >:: (fun _ -> assert_equal (next_weekday Tuesday) Wednesday);
  "thu_after_wed"  >:: (fun _ -> assert_equal (next_weekday Wednesday) Thursday);
  "fri_after_thu"  >:: (fun _ -> assert_equal (next_weekday Thursday) Friday);
]
```

我们编译并运行测试； 许多失败。 很好！ 那我们添加新功能：

```ocaml
let next_weekday d =
  match d with
  | Monday -> Tuesday
  | Tuesday -> Wednesday
  | Wednesday -> Thursday
  | Thursday -> Friday
  | _ -> failwith "Unimplemented"
```

我们编译并运行测试； 他们通过了。 在这一点上，我们可以继续添加对周末的处理，但是我们应该首先注意到关于我们编写的测试的一些知识：它们涉及很多重复代码。 实际上，我们可能是通过复制并粘贴第一个测试，然后通过修改后面三个来完成编写。 这就表明我们应该重构代码了。 （就像我们之前对 `sum` 函数进行的测试一样。）

让我们抽象一个为 `next_weekday` 创建测试用例的函数：

```ocaml
let make_next_weekday_test name expected_output input=
  name >:: (fun _ -> assert_equal expected_output (next_weekday input))

let tests = "test suite for next_weekday" >::: [
  make_next_weekday_test "tue_after_mon" Tuesday Monday;
  make_next_weekday_test "wed_after_tue" Wednesday Tuesday;
  make_next_weekday_test "thu_after_wed" Thursday Wednesday;
  make_next_weekday_test "fri_after_thu" Friday Thursday;
]
```

现在，我们通过处理周末来完成测试和代码实现。 首先，我们添加一些测试用例：

```ocaml
  ...
  make_next_weekday_test "mon_after_fri" Monday Friday;
  make_next_weekday_test "mon_after_sat" Monday Saturday;
  make_next_weekday_test "mon_after_sun" Monday Sunday;
  ...
```

然后我们继续完成这个函数：

```ocaml
let next_weekday =
  match d with
  | Monday -> Tuesday
  | Tuesday -> Wednesday
  | Wednesday -> Thursday
  | Thursday -> Friday
  | Friday -> Monday
  | Saturday -> Monday
  | Sunday -> Monday
```

当然，大多数人即使不使用 TDD 也可以编写该函数而不会出错。 但实际上我们很少实现如此简单的函数。

**流程.** 让我们回顾一下 TDD 的流程：

- 编写失败的单元测试用例。 运行测试套件以证明测试用例失败。
- 实现足够的功能以使测试用例通过。 运行测试套件以证明测试用例通过。
- 根据需要改进代码。 在上面的示例中，我们重构了测试套件，但是通常我们需要重构正在实现的功能。
- 重复，直到测试套件提供的证据证明您的实现是正确的为止。



# 3.1.4. Records

*记录* 是程序员可以在 OCaml 中定义的的一种数据类型。 它是其他类型数据(每种都被命名)的某种组合。 OCaml 记录非常类似于 C 中的结构体。这是神奇宝贝(Pokémon)的记录类型定义示例：

```ocaml
type mon = {name: string; hp : int; ptype: ptype}
```

此类型定义了一个记录，其中包含三个字段，分别为 `name`，`hp`（命中点）和 `ptype`（如上定义）。 还给出了每个字段的类型。 注意， `ptype` 既可以用作类型名称，也可以用作字段名称。 这些名称空间在 OCaml 中是不同的(distinct)。

要构建记录类型的值，我们编写一个记录表达式，如下所示：

```ocaml
{name = "Charmander"; hp = 39; ptype = TFire}
```

因此，在类型定义中，我们在字段的名称和类型之间写一个冒号，但在表达式中写一个等号。

要访问记录并从中获取字段，我们使用许多其他语言所期望的点符号。 例如：

```ocaml
# let c = {name = "Charmander"; hp = 39; ptype = TFire};;
# c.hp;;
- : int = 39
```

也可以使用模式匹配来访问记录字段：

```ocaml
match c with
| {name=n; hp=h; ptype=t} -> h
```

这里的 `n`，`h` 和 `t` 是模式变量。 如果要对字段和模式变量使用相同的名称，则提供语法糖：

```ocaml
match c with
| {name; hp; ptype} -> hp
```

这里，模式 `{name; hp; ptype}` 是  `{name=name; hp=hp; ptype=ptype}` 的语法糖。 在每个子表达式中，出现在等号左侧的标识符是字段名称，而出现在右侧的标识符是模式变量。

**语法.**

记录表达式写作:

```ocaml
{f1 = e1; ...; fn = en}
```

记录表达式中 `fi = ei` 的顺序无关紧要。 例如，`{f=e1;g=e2}` 完全等同于 `{g=e2;f=e1}`。

字段访问写作:

```ocaml
e.f
```

其中 `f` 是字段名称的标识符，而不是表达式。

**动态语义.**

- 如果对于 `1..n` 中的所有 `i` 都有 `ei ==> vi`, 那么 `{f1=e1; ...; fn=en} ==> {f1=v1; ...; fn=vn}`.
- 如果 `e ==> {...; f=v; ...}` , 则 `e.f ==> v`.

**静态语义.**

记录类型为:

```ocaml
{f1 : t1; ...; fn : tn}
```

记录类型中 `fi:ti` 的顺序无关紧要。 例如，`{f:t1;g:t2}` 完全等同于  `{g:t2;f:t1}`。

请注意，必须先定义记录类型，然后才能使用它们。 这使得 OCaml 可以进行更好的类型推断，这比不使用定义的记录类型可以做得更好。

类型检查规则:

- 如果对于 `1..n` 中的所有 `i` 都有 `ei:ti`，并且如果 `t` 定义为 `{f1:t1; ...; fn:tn}`，则 `{f1=e1; ...; fn=en}: t`。 请注意，记录表达式中提供的字段集必须是记录定义类型的完整字段集。
- 如果 `e: t1` 并且 `t1` 定义为 `{...; f:t2; ...}`, 则 `e.f: t2`.

**记录拷贝.**

还有另一种用来从旧的记录中构造新记录的语法：

```ocaml
{e with f1 = e1; ...; fn = en}
```

这不会改变旧的记录； 它构造了一个包含新值的新记录。 在 `with` 之后提供的字段集不必是记录定义类型的完整字段集。 在新的记录中，未作为 `with` 一部分提供的任何字段都将从旧记录中复制。

您可能会期望它的动态和静态语义，尽管它们在数学上难以写下来。

**模式匹配.**

我们将以下新模式形式添加到合法模式列表中：

- `{f1=p1; ...; fn=pn}`

并且我们扩展了模式何时与值匹配并产生绑定的定义，如下所示：

- 如果对于 `1..n` 中的所有 `i`，都有 `pi` 与 `vi` 匹配并产生绑定 $b_i$，则记录模式 `{f1=p1; ...; fn=pn}` 与记录值 `{f1=v1; ...; fn=vn; ...}` 匹配并生成绑定集 $\cup _ib_i$。 请注意，记录值可能比记录模式具有更多的字段。

作为语法糖，提供了另一种记录模式：`{f1; ...; fn}`。它是  `{f1=f1; ...; fn=fn}` 的语法糖形式。



# 3.1.5. Tuples

*元组* 是 OCaml 中程序员可以定义的另一种类型。 像记录一样，它是其他类型数据的组合。 但是，不用命名 *组件* (元素)，而是通过位置来标识它们。 以下是一些元组的示例：

```ocaml
(1,2,10)
1,2,10
(true, "Hello")
([1;2;3], (0.5,'X'))
```

具有两个元素的元组称为 *pair*。 具有三个元素的元组称为 三元组 (triple)。 除此之外，我们通常只使用元组一词，而不是继续基于数字的命名方案。 而且，除此之外，使用记录代替元组可以说是更好的选择，因为程序员很难记住哪个元素应该代表什么信息。

创建元组很容易：如上所述，只需编写元组即可。 访问再次涉及到模式匹配，例如：

```ocaml
match (1,2,3) with
| (x,y,z) -> x+y+z
```

**语法.**

一个元组写作

```ocaml
(e1, e2, ..., en)
```

括号是可选的，但有时括号可能是必需的以确保编译器按您希望的方式解析代码。 在某些习惯用法中可以忽略它们的一个地方是在 `match` 和 `with` 关键字之间的 match 表达式中（以及在随后分支的模式中）。

**动态语义.**

- 如果对于 `1..n` 中的所有 `i` 都有  `ei ==> vi`, 则 `(e1, ..., en) ==> (v1, ..., vn)`.

**静态语义.**

元组类型使用新的类型构造器 `*` 来编写，该构造器与乘法运算符不同。 类型 `t1 * ... * tn` 是元组的类型，其第一个元素的类型为 `t1`，...，第 `n` 个元素的类型为 `tn`。

- 如果对于 `1..n` 中的所有 `i` 都有 `ei: ti`, 则 `(e1, ..., en): t1 * ... * tn`.

**模式匹配.**

我们将以下新模式形式添加到合法模式列表中：

- `(p1, ..., pn)`

括号是可选的，但有时括号可能是必需的以确保编译器按您希望的方式解析代码。

并且我们扩展了模式何时与值匹配并产生绑定的定义，如下所示：

- 如果对于 `1..n` 中的所有 `i` ，都有 `pi` 匹配 `vi` 并产生绑定 $b_i$，则元组模式 `(p1, ..., pn)` 匹配元组值 `(v1, ..., vn)` 并产生绑定集 $⋃_ib_i$。 注意，元组值必须具有与元组模式完全相同的元素个数。



# 3.1.6. One-of vs. Each-of

变体与元组/记录之间的最大区别是，变体类型的值是一组可能性中的一个，而元组/记录类型的值则提供了一组可能性中的每一个。 回到我们的示例，类型 `day` 的值是 `Sun` 或 `Mon` 或其他中的一个。但是类型 `mon` 的值包含了 一个 `string` 和一个 `int` 以及一个 ptype。 请注意，在前两个句子中，单词“或”如何与变体类型相关联，而单词“与”如何与元组/记录类型相关联。 如果您要尝试决定使用变体还是元组/记录，这将是一个很好的线索：如果需要的是这条数据或其他数据，则使用变体； 如果您同时需要这条数据和那条数据，则使用元组/记录。

One-of 类型通常称为和类型( *sum types* )，each-of 类型称为积类型(*product types*)。 这些名称来自集合论。 变体就像 [disjoint union](https://en.wikipedia.org/wiki/Disjoint_union) (不相交集)，因为变体的每个值都来自许多基础集合中的一个（到目前为止，这些集合中的每个都只是一个构造器，因此具有基数 1）。 不相交集有时用求和运算符 Σ 编写。 元组/记录类似于笛卡尔乘积 ([Cartesian product)](https://en.wikipedia.org/wiki/Cartesian_product)，因为元组/记录的每个值都包含多个基础集中的每个值。 笛卡尔积通常是由乘积运算符 × 编写的。



# 高级模式

以下是其他的一些有用的模式形式：

- `p1 | ... | pn`:  “或”模式； 任何单个模式 `pi` 的匹配成功就算匹配成功，这些模式按从左到右的顺序进行尝试。 所有模式都必须绑定同一个变量。
- `(p : t)`: 具有显式类型注解的模式。
- `c`: 在这里，`c` 表示任何常量，例如整数字面量，字符串字面量和布尔值。
- `'ch1'..'ch2'`: 在这里， `ch` 表示一个字符字面量。 例如， `'A'..'Z'` 匹配任何大写字母。
- `p when e`: 与 `p` 匹配，但前提是 `e` 等于 `true`。

您可以阅读手册中的 [所有模式形式](http://caml.inria.fr/pub/docs/manual-ocaml/patterns.html) 。



# 3.1.7.1. Pattern Matching with Let

实际上，到目前为止，我们一直用于 let 表达式的语法是 OCaml 允许的完整语法的一种特殊情况。 该语法是：

```ocaml
let p = e1 in e2
```

也就是说，绑定的左侧实际上可以是模式，而不仅仅是标识符。 当然，变量标识符在我们的有效模式列表中，因此这就是我们到目前为止研究的语法只是一个特例的原因。

给定这种语法，我们将重新审视 let 表达式的语义。

**动态语义.**

为了求值 `let p = e1 in e2`:

1. 求值 `e1` 为 `v1`.
2. 将 `v1` 与模式 `p` 匹配。 如果不匹配，则引发异常  `Match_failure`。 否则，将产生一组绑定 $b$。
3. 在 `e2` 中替换这些绑定，得到一个新的表达式 `e2'`。
4. 对 `e2'` 求值得到 `v2`.
5. 整个 let 表达式的求值结果即为 `v2`.

**静态语义.**

- 如果以下条件都满足:

  - `e1:t1`
  - `p` 中的模式变量是 `x1..xn`
  - `e2:t2` 满足对于 `1..n` 中的所有 `i` 都有 `xi:ti`,

  那么 `(let p = e1 in e2): t2`.

**Let 定义.**

和以前一样，let 定义可以理解为尚未给出其 body 的 let 表达式。 所以他们的语法可以概括为

```ocaml
let p = e
```

像以前一样，它们的语义也来自 let 表达式的语义。



# 3.1.7.2. Pattern Matching with Functions

到目前为止，我们一直在使用的函数定义语法也是 OCaml 允许的完整语法的特例。 该语法是：

```ocaml
let f p1 ... pn = e1 in e2   (* function as part of let expression *)
let f p1 ... pn = e          (* function definition at toplevel *)
fun p1 ... pn -> e           (* anonymous function *)
```

我们需要关心的真正原始的句法形式是 `fun p -> e`。 让我们以这种形式重新回顾匿名函数的语义及其应用。 其他形式的更改如下：

**静态语义.**

- 令 `x1..xn` 为出现在 `p` 中的模式变量。 假设 `x1:t1`,  `x2:t2`  ...  `xn:tn` 我们可以得出 `p:t` 和 `e:u` 的结论，那么 `fun p -> e : t -> u`。
- 函数应用的类型检查规则不变。

**动态语义.**

- 匿名函数的求值规则不变。
- 为了求值 `e0 e1`:
  1. `e0` 求值为一个匿名函数 `fun p -> e`, 同时 `e1` 求值为 `v1`。
  2. 将 `v1` 与模式 `p` 匹配。 如果匹配将产生一组绑定 $b$。否则，引发异常 `Match_failure`。
  3. 在 `e` 中替换这些绑定，得到一个新的表达式  `e'`。
  4. 将 `e'` 求值得到 `v`, 这就是 `e0 e1` 表达式的求值结果。



# 3.1.7.3. Pattern Matching Examples

以下是有关如何对数据使用模式的许多示例：

```ocaml
(* Pokemon types *)
type ptype =
  TNormal | TFire | TWater

(* A record to represent Pokemon *)
type mon = {name: string; hp : int; ptype: ptype}

(*********************************************
 * Several ways to get a Pokemon's hit points:
 *********************************************)

(* OK *)
let get_hp m =
  match m with
  | {name=n; hp=h; ptype=t} -> h

(* better *)
let get_hp m =
  match m with
  | {name=_; hp=h; ptype=_} -> h

(* better *)
let get_hp m =
  match m with
  | {name; hp; ptype} -> hp

(* better *)
let get_hp m =
  match m with
  | {hp} -> hp

(* best *)
let get_hp m = m.hp

(**************************************************
 * Several ways to get the 3rd component of a tuple
 **************************************************)

(* OK *)
let thrd t =
  match t with
  | (x,y,z) -> z

(* good *)
let thrd t =
  let (x,y,z) = t in z

(* better *)
let thrd t =
  let (_,_,z) = t in z

(* best *)
let thrd (_,_,z) = z

(*************************************
 * How to get the components of a pair
 *************************************)

let fst (x,_) = x
let snd (_,y) = y

(* both fst and snd are functions already provided in the standard library *)
```


<br/>