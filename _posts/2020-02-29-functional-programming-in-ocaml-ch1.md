---
layout:          post
title:           OCaml函数式编程 - 第1章
description:     Cornell大学 CS 3110 课程教材 Functional Programming in OCaml 翻译
date:            2020-02-29 16:44:52 +08:00
author:          "Clark Than"
category:        PL
tags:            OCaml PL
---


# 第一章 What is 3110 about?

你可能认为本课程是关于 OCaml 的。不是。

你可能认为本课程是关于数据结构的。不是。

你可能认为本课程是有关 "weeding out" CS 专业的。不是。


> 明仁时代(1868-1912)的日本大师 Nan-in 曾收到一位大学教授有关禅的询问. Nan-in 送茶, 他把教授的杯子倒满, 然后继续倒. 教授一直看着它溢出, 直到他再无法克制自己了.  “太满了, 不会再进去了！” “像这个杯子”,  Nan-in说，“您充满了自己的见解和猜测。除非您先清空杯子，否则我如何向您展示禅？”


<br/>
Folklore 曾表示专业程序员之间的生产力存在10倍的差异。 一项数据驱动的研究表明, 该因素更可能是2-4倍。 您想提高两倍的生产力, 或是更高吗？



**本课程旨在使您成为更好的程序员。**

编程并不难。 但好的编程非常困难。



# 1.1. Programming Language

一门伟大的通用编程语言...

- 让你以正确的抽象层次简洁明了地描述事物
- 让你可以使用特定领域的新特性扩展语言, 同时又可以与其他语言很好地融合在一起。
- 易于编写正确的代码, 并具有良好的性能
- 当您发现规范更改时, 还能轻松修改代码
- 易于重用代码
- 易于学习

差不多可能有数千种 general-purpose 编程语言。 但, 没有一种万能( universally )的编程语言。

通用编程语言来来去去。 在你的生活中, 您可能会学到一些。 今天, 它是 Java 和 C ++。 昨天是 Pascal 和 C。在那之前是 Fortran 和 Lisp。 谁知道明天会是什么呢？ 你可能会为特定项目使用数十种特殊用途的语言。 在这个瞬息万变的领域, 你需要能够快速适应。

一个好的程序员必须学会***如何学习***新语言。

我们使用无数种不同的编程语言与机器相互通信：

- 通用目标和脚本编写：Fortran，Lisp，Basic，C，Pascal，Scheme，C ++，Java，C＃，Visual Basic，Perl，Python，Ruby，PHP，Javascript，Clojure，Scala，Erlang，Swift，...
- 工具：awk，sed，tcl，sh，csh，bash，...
- 搜索：正则表达式，浏览器查询，SQL，...
- 显示和渲染：PostScript，PDF，HTML，XML，...
- 硬件：CCS，VHDL，Verilog，...
- 定理证明和数学：Mathematica，Maple，Matlab，R，NuPRL，Isabelle / HOL，ACL2，Coq

理解编程背后的原理(超越特定语言的细节)至关重要。 没有比从一个完全不同的视角进行编程更好地来掌握这些原理的方法了。



# 1.2. OCaml

我们以学习 OCaml 来开始本课程的根本原因：与大多数人在以前的编程课程中所见的观点截然不同。 由于您已经参加过 CS1110 和 CS2110 课程, 因此您已经学习了 how to program。 本课程使您有机会从头开始学习一种新语言, 并一路反思编程与编程之间的区别。。

> “不能影响您对编程思考方式的语言是不值得了解的。” - Alan J. Perlis（1922-1990），第一位图灵奖获得者

<br/>

OCaml 将改变您对编程的看法。

OCaml 是一门函数式编程语言。 函数式编程语言的核心语言抽象是数学函数。 函数将输入映射到输出； 对于相同的输入，它总是产生相同的输出。 也就是说，数学函数是无状态的：它们不维护任何额外的信息或在函数之间 persists 的状态。 函数是 *first-class*：您可以将它们用作其他函数的输入, 也可以将函数作为输出。 用函数表达一切就可以实现一个统一而简单的编程模型, 该模型比其他语言家族中的 procedures 和 methods 更容易理解。

OCaml 支持许多高级特性, 其中一些你之前已经遇到过呢, 而其中一些可能对你来说是新的：

- **代数数据类型：** 你可以轻松地在 OCaml 程序中构建复杂的数据结构, 而无需惊扰于指针和内存管理。模式匹配使得它们更加方便。
- **类型推导：** 你不必在任何地方写下类型信息。编译器会自动地出探寻出大多数类型。这可以使代码更易于阅读和维护。
- **参数化多态：** 函数和数据结构可以在类型上进行参数化。这对于能够重用代码至关重要。
- **垃圾回收：** 自动垃圾回收减轻了内存分配和释放的负担, 内存分配和释放是 C 等语言中常见的 Bug 来源。
- **Modules:** OCaml 可以通过使用 modules 轻松构建大型系统。Modules（称为 *structures* ）用于封装接口（称为 *signatures* ）之后的实现。通过提供操纵模块的 functions（称为函子）, OCaml 的模块化功能远远超越大多数语言。

OCaml 是一门静态类型且类型安全的编程语言。 静态类型的语言会在编译时检测类型错误, 因此不会执行具有类型错误的程序。 类型安全的语言限制了可以对哪种数据执行哪种操作。 实践中, 这可以防止很多愚蠢的错误（例如, 将整数视为函数）, 还可以防止很多安全问题：计算机紧急响应小组（CERT, 一个负责网络安全的美国政府机构）报告的 break-ins 事件中, 有超过一半是由于缓冲区溢出造成的, 这在类型安全的语言中是不太可能的。

有些语言（例如 Scheme 和 Lisp）是类型安全的, 却是动态类型(仅在运行时捕获类型错误)的。 其他语言（例如 C 和 C ++）是静态类型的, 但不是类型安全的, 其无法保证不会发生类型错误。

从分类上讲, OCaml 来自其祖父(root)是 Lisp 的编程语言族谱线, 这条族谱线上海包括像 Clojure, F＃, Haskell 和 Racket 等现代语言。 函数式编程语言有一种令人惊讶的趋势: 可以预测更多主流语言的未来。 Java 在1995年使垃圾收集成了主流, 然而 Lisp 找在1958年就已经有了。Java 直到2004年的第5版才拥有泛型, 然而, ML 系编程语言在1990年就有了。在过去10年, first-class 函数和类型推导(Type Inference)已被逐渐引入像 Java 和 C＃和 C ++ 之类的主流语言中, 而这已经是函数式编程语言引入很久之后的事了。 通过研究函数式编程, 你可以了解下一步(编程语言演化)可能发生的事情。 谁知道会是什么？（我赌模式匹配）

## 关于OCaml历史的题外话

Robin Milner 和苏格兰爱丁堡计算机科学实验室的其他人在70年代末和80年代初研究定理证明。 传统上, 定理证明是用 Lisp 等语言实现的。 Milner 经常遇到 theorem provers 有时会把不正确的“证明”（即非证明）放在一起并 claim 它们是 valid。 为此, 他尝试开发一种只允许构造有效证明的语言。 ML(即 “元语言”)就是这项工作的成果。 ML 的类型系统(Type System)经过精心构造, 因此你只能使用该语言构造有效的证明。 然后将 theorem prover 编写为构造证明的程序。 最终, 这种 “Classic ML” 演变成为一种成熟的编程语言。

在80年代初期，ML 社区出现了分裂: 法国一方, 英国和美国另一方。 法国人后来继续开发出了 CAML，后来又开发了 Objective CAML（OCaml）, 而英国人和美国人则开发了 Standard ML。 这两种 ML 方言非常相似。 微软也在2005年推出了自己的 OCaml变体,  F＃。

Milner 于1991年获得图灵奖, 很大程度上是因为他在 ML 方面所做的工作。 The [ACM website for his award](https://amturing.acm.org/award_winners/milner_1569367.cfm) includes this praise:

> ML远远领先于它的时代。 它建立在简洁明了的数学思想之上，teased apart so that they can be studied independently and relatively easily remixed and reused。 ML影响了许多实用编程语言，包括 Java, Scala和 微软的 F＃。 确实, 没有一份严肃的语言设计师可以忽略这个优秀的设计例子。

## 工业

OCaml 和其他函数式编程语言远没有 C, C ++ 和 Java 那样流行。 OCaml 的真正优势在于语言操作（即编译器, 分析器, 验证器, 证明等）。 这并不奇怪, 因为 OCaml 是从定理证明领域发展而来的。

这并不是说函数式编程语言在工业中就没有什么应用。 除其他语言外, 还有许多[使用OCaml的工业项目](https://ocaml.org/learn/companies.html)和 [使用Haskell的工业项目](https://wiki.haskell.org/Haskell_in_industry)。 康奈尔人 Yaron Minsky（PhD 2002）写了一篇关于[在金融行业使用OCaml](http://dx.doi.org/10.1017/S095679680800676X)的论文（必须从康奈尔网络内部访问该链接）。 它说明了 OCaml 的特性如何使其成为快速构建可运行复杂软件的理想选择。

但是最终, 这门课程是关于你作为一名程序员的学历，而不是为了找工作。

> “当人们忘记了在学校学到的一切东西之后，剩下的就是教育。” - Albert Einstein

<br/>

OCaml 在澄清和简化函数式编程的本质方面做得很好，这是其他比如混合函数式和命令式编程（例如Scala）或将函数式编程发挥到极致（例如Haskell）的语言所不具备。 学习了 OCaml 之后，你将可以自学任何其他函数式（启发性）语言。

## 美学

我的观点: 一个非科学, 非客观的学习 OCaml 的理由是 -- OCaml is beautiful.

> "Beauty is our Business" —title of a book in honor of Edsger W. Dijkstra

注: (Dijkstra因“对编程的基本贡献”而于1972年获得图灵奖。David Gries 是该书的编辑。)

OCaml 优雅, 简单且优美。 你的代码可以写得有风格且雅致。 起初, 这可能并不明显。 毕竟, 毕竟正在学习一门新的语言--您不会期望在[SANSK 1131](https://classes.cornell.edu/browse/roster/FA17/class/SANSK/1131)的第一天就欣赏梵文诗歌。实际上, 刚开始当你用一门新语言难以表达自己时, 你可能会感到沮丧。 所以给一些时间吧。 我已经记不得有多少学生在之后的学期回来告诉我: 在学习 3110课程 之后回过头看自己之前写的代码是何其的“丑陋”啊。

美学确实很重要。 代码不只是为了由机器执行而编写。 它也可以与人交流。 简洁的代码更易于阅读和维护。 但, 这并容易。



# 1.3. Mutability

诸如 C 和 Java 之类的命令式编程语言涉及可变状态，该可变状态在整个执行过程中都可能会发生变化。 命令指定通过破坏性地更改状态来进行计算。 过程（或方法）除了产生返回值外，还具有更新状态的副作用。

可变性的幻想是很容易理解的：机器先执行此操作, 然后执行其他操作, 依此类推。

可变性的现实意义在于: 尽管机器擅长复杂的状态操作, 但人类却不擅长理解状态。 这样做的本质将是，可变性打破了**引用透明**(用表达式对应的值替换表达式而不影响最后计算结果的能力)。 在数学中，如果 f(x) = y，则可以用 y 替换掉你看到的任何位置的 f(x)。 在命令式语言中, 你不能这样做：因为 f 可能会有副作用, 因此在时间 t 计算的 f(x) 可能和在时间 t' 计算得到的值不一样。

很容易让人信以为真的是: 机器会操纵一个单一的状态，并且机器一次只能做一件事。 计算机系统竭尽全力提供这种错觉, 但这仅仅是一种幻想。 现实中, 有许多状态分布在线程, 内核, 处理器和网络计算机上。 机器可以同时执行许多操作。 可变性使得对分布式状态和并发执行的推理变得极为困难。

但是，不变性使程序员摆脱了这些担忧。 它提供了构建正确的并发程序的强大方法。 与大多数函数式编程语言一样，OCaml 是一门主要基于不可变性质的语言。 它确实支持具有可变状态的命令式编程，但是直到该课程开始约两个月后我们才会使用这些功能，部分原因是因为我们根本不需要它们, 部分原因是让你从一种可能不知道自己有的依赖中退出"cold turkey"。摆脱可变性是 3110课程 可为你带来的最大视角变化之一。



# 1.4. Summary

本课程旨在让你成为一名更好的程序员。 学习函数式编程将对此有所帮助。 我们遇到的最大障碍是说一种新语言的挫败感，尤其是放任可变状态。 但是好处将是巨大的：发掘超越使用任何特定语言或语言族的编程，对高级语言特性的了解以及对美的欣赏。

## 术语和概念

- dynamic typing
- first-class functions
- functional programming languages
- immutability
- Lisp
- ML
- OCaml
- referential transparency
- side effects
- state
- static typing
- type safety

## 拓展阅读

- [Introduction to Objective Caml](http://courses.cms.caltech.edu/cs134/cs134b/book.pdf): 第1, 2章, 本课程推荐的免费教科书
- [OCaml from the Very Beginning](http://ocaml-book.com/): 第1章, 一本相对便宜的教科书(PDF)，推荐用于本课程
- [A guided tour [of OCaml\]](https://realworldocaml.org/v1/en/html/a-guided-tour.html): Real World OCaml 第1章, 一本更具野心的书，几个康奈尔人写的，有些学生可能会很喜欢阅读
- [The history of Standard ML](http://sml-family.org/history/): 尽管它专注于 SML (ML 语言的一个变体)，但它与 OCaml 有关
- [The value of values](https://www.infoq.com/presentations/Value-Values): Clojure（a modern dialect of Lisp）之父的一个演讲，主题是命令式编程的时代已经过去了
- [The perils of JavaSchools](https://www.joelonsoftware.com/2005/12/29/the-perils-of-javaschools-2/): Stack Overflow 首席执行官的一篇论文，关于为什么CS 2110还不够，以及为什么同时还需要 CS 3110 和 CS 3410 的问题。
- [Teach yourself programming in 10 years](http://norvig.com/21-days.html): Google 研究总监撰写的一篇文章，其中介绍了成为一个受过良好教育的程序员所需要的时间



# 1.5. Exercises

该教科书后续的章节将在最后包含练习。 练习以难度等级进行注释：

- 一星 [✭]: 简单的练习，只需一两分钟。
- 两星 [✭✭]: 简单的练习，需要几分钟。
- 三星 [✭✭✭]: 可能需要五到二十分钟左右时间的练习。
- 四星 [✭✭✭✭] 以及更多: 为想要更深入地学习材料的学生提供的具有挑战性或比较耗时的练习。

一些练习被标注为“高级”或“可选”。 我们可能会时不时地误判了问题的难度。 如果您认为标注需要关闭，请告诉我们。

请不要在任何地方发布习题的答案，尤其是不要在一些公共代码仓库中，搜索引擎可能会找到这些答案。 过去，其中一些练习已用作家庭作业和考试习题，其中一些将来可能会被再次使用。

该课程的习题答案代码仓库可供当前课程的学生使用。 有关如何访问它的说明将在其他地方提供。 其他大学的讲师也可能要求访问。

习题是考试题目的很好来源。 对于考试, 好的学习方法是完成所有尚未完成的练习，并对以前完成的习题进行重做。 回顾以前的练习，你甚至可能会发现更优的解决方案。



# 1.6. A Brief History of CS 3110

曾几何时，麻省理工学院开设了一门课程，称为 6.001 计算机程序的结构和解释（SICP）。 它有一本同名的[教科书](http://web.mit.edu/alexmv/6.037/sicp.pdf) ，并使用了一门函数式编程语言 - Scheme。 紧随其后, Tim Teitelbaum 于1988年秋在康奈尔（Cornell）使用 Scheme 讲授了该课程的一个版本，

**CS 212.** Dan Huttenlocher 曾在麻省理工学院担任过 6.001 的助教, 后来他在康奈尔大学任教。 1989年，他开设了CS 212 课程(算法表达模式)。 基于 SICP 课程，他为教学材料引入了更加严格的方法。 在1990年代中期之前，Huttenlocher 使用各种方言的 Scheme 持续开发 CS 212 课程。

其他教师开始定期教授该课程。 Ramin Zabih 曾在麻省理工学院（MIT）作为一年级学生参加了 6.001 课程,  并在1994年春在康奈尔大学任教，讲授 CS212。Dexter Kozen（1977 Cornell博士）于1996年春首次教授这门课程。该课程最早的在线记录似乎是[1998年春](http://www.cs.cornell.edu/courses/cs212/1998sp/Outline.html),，由 Greg Morrisett 在 Dylan 讲授。 在Dylan, 该课程的名称已变成“计算机程序的结构和解释”。

到[1999年秋](http://www.cs.cornell.edu/courses/cs212/1999FA/Materials.html)，CS 212 已经有了自己的课程讲义。 就像 CS 3110 一样，CS 212 涵盖了函数式编程, 替换和环境模型, 某些数据结构和算法以及编程语言实现。

**CS 312.** 当时，CS专业有两门入门编程课程，分别是 CS 211 Computers and Programming 和 CS212。学生可选择其中一门，类似于今天的学生要么选择 CS 2110, 要么选择 CS2112 ... 然后继续选择修读 CS 410 Data Structure 课程。CS 410 最早的在线记录似乎是在 [1998年春](http://www.cs.cornell.edu/courses/cs410/1998sp/schedule.html)。它涵盖了 CS 212 未涵盖的许多数据结构和算法，包括平衡树和图，并且它使用Java作为编程语言。

根据他们修读的课程（CS 211或212），学生进入的是具有不同技能的高级课程。 经过广泛讨论，教师选择CS 211 的作为发展寄托，并将 CS 212 重命名为 CS 312 - 数据结构和函数式编程，同时让 CS 211 作为 CS 312 的前置课程。与此同时，CS 410 从课程表中删除， 其内容包装后分散为 CS 312 和 CS 482 - 算法分析简介。 Dexter Kozen于[1999年秋](http://www.cs.cornell.edu/courses/cs410/1999fa/)讲授最后一期 CS 410。

Greg Morrisett 于 [2001年春](http://www.cs.cornell.edu/courses/cs312/2001SP/notes.html)为新的 CS 312 揭幕。他将编程语言从 Scheme 转到 Standard ML。 Kozen于2001年秋季首次授课，而[2002年秋](http://www.cs.cornell.edu/courses/cs312/2002fa/lectures.htm)则由 Andrew Myers 授课。Myers 开始将另一本MIT教科书《 *Program Development in Java: Abstraction, Specification, and Object-Oriented Design*》（Barbara Liskov和John Guttag）纳入模块化编程教学的材料。 Huttenlocher 于2006年春季首次教授该课程。

**CS 3110.** 在[2008年秋](http://www.cs.cornell.edu/courses/cs3110/2008fa/schedule.html) ，发生了两个重大变化：编程语言切换为 OCaml，切换为四位数的课程号。 CS 312变成 CS3110。Myers, Huttenlocher, Kozen 和 Zabih 分别于2008年秋, 2009年春, 2009年秋和2010年秋首次讲授该修订课程。 Nate Foster 于2012年春首次讲授该课程；Bob Constable 和 Michael George 则于2013年秋首次共同授课。

Michael Clarkson（2010 Cornell 博士）在 [2008年春](http://www.cs.cornell.edu/courses/cs312/2008sp/overview.html)以博士生的身份成为该课程的授课 TA 后，于 [2014年秋](http://www.cs.cornell.edu/courses/cs3110/2014fa/course_info.php)首次正式教授该课程。他开始修改有关 OCaml 编程的教学材料，结合 Dan Grossman（2003 Cornell博士）提出的一种通过将编程语言分解为**语法**, **动态语义**和**静态语义**来学习编程语言的方法。 Grossman 在华盛顿大学的 **CSE 341 Programming Languages** 课程以及 MOOC平台上的超人气课程 **[*Programming Languages*](https://www.coursera.org/learn/programming-languages)**  中都使用了这种方法。

该教科书于 [2018年秋](https://www.cs.cornell.edu/courses/cs3110/2018fa/textbook) 开始编写。 它综合了康奈尔大学超过20年的函数式编程指令的工作成果。 用康奈尔[Evening Song](https://alumni.cornell.edu/download/3542/) 中的话来说就是:

> 'Tis an echo from the walls
> Of our own, our fair Cornell.

<br/>