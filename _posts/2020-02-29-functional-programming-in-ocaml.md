---
layout:          post
title:           OCaml函数式编程
description:     Cornell大学 CS 3110 课程教材 Functional Programming in OCaml 翻译
date:            2020-02-29 16:44:52 +08:00
author:          "Clark Than"
category:        PL
tags:            OCaml PL
---


>  注: 此作品是对 CS3110 sp2020 课程教材 [Functional Programming in OCaml](https://www.cs.cornell.edu/courses/cs3110/2020sp/textbook/) 的个人翻译.
>
> 权当学习, 如有侵权请告知
>
>

Functional Programming in OCaml
===============================


基于 Michael R. Clarkson, Robert L. Constable, Nate Foster, Michael D. George, Dan Grossman, Daniel P. Huttenlocher, Dexter Kozen, Greg Morrisett, Andrew C. Myers, Radu Rugina 以及 Ramin Zabih 所教授的课程.

本著作基于20多年的课程笔记和上述作者的智慧贡献； 到目前为止，要弄清楚谁具体做出了什么贡献并不是一件容易的事。 Michael R. Clarkson 是本著作的主要编写者和作者，本著作的形式为统一教科书。

有关此作品的最新版本，请参见最新版本:  [CS 3110 course website](https://www.cs.cornell.edu/courses/cs3110).



## 目录

- [x] 1. [Introduction](/2020-02/functional-programming-in-ocaml-ch1.html)
  - 1.1. Programming Languages
  - 1.2. OCaml
  - 1.3. Mutability
  - 1.4. Summary
  - 1.5. Exercises
  - 1.6. A Brief History of CS 3110
- [x] 2. [The Basics of OCaml](/2020-03/functional-programming-in-ocaml-ch2.html)
  - 2.1. Interacting with OCaml
  - 2.2. Expressions
    - 2.2.1. Operators
    - 2.2.2. If Expressions
    - 2.2.3. Let Expressions
    - 2.2.4. Scope
  - 2.3. Functions
    - 2.3.1. Anonymous Functions
    - 2.3.2. Function Application
    - 2.3.2. Polymorphic Functions
    - 2.3.4. Labeled and Optional Arguments
    - 2.3.5. Partial Application
    - 2.3.6. Operators as Functions
    - 2.3.7. Documentation
    - 2.3.8. Preconditions and Postconditions
  - 2.4. Debugging
    - 2.4.1. Printing
    - 2.4.2. Defensive Programming
  - 2.5. Summary
  - 2.6. Exercises
- [x] 3. [Data in OCaml](/2020-03/functional-programming-in-ocaml-ch3.html)
  - 3.1. Standard Data Types
  - 3.2. Advanced Data Types
  - 3.3. Summary
  - 3.4. Exercises
- [ ] 4. Higher-order Programming
- [ ] 5. Modules
- [ ] 6. Specifications and Abstraction
- [ ] 7. Testing
- [ ] 8. Mutability
- [ ] 9. Efficiency
- [ ] 10. Interpreters
- [ ] 11. Formal Methods
- [ ] 12. Advanced Topics

