---
layout: post
title: 表达式求值“利器”：逆波兰算法详解
categories: [Algorithm]
description: 本文比较详细地阐述了利用逆波兰算法将中缀表达式转为后缀表达式并求值的方法。
keywords: RPN, Algorithm
---

给定一个合法表达式，其中含有任意合法运算符，包括加减乘除、括号和某些自定义的多元运算符，如何编写一个算法对这个合法表达式求值呢？类似的这类表达式求值问题都可以利用逆波兰算法来求解。本博文将会详解逆波兰算法——通用的表达式求值“利器”。

### 算法本质

在表达式中，括号、括号的多层嵌套以及运算符的优先级不同等因素往往会令运算顺序变得极为复杂，最终会导致对中缀表达式的求值变得十分困难。而逆波兰算法则将中缀表达式转换为等价的后缀表达式，然后对后缀表达式求值，从而得到原式的值。

#### 为什么要将中缀表达式转换为后缀表达式呢？

要解答这个问题，首先需要了解什么是中缀表达式，而什么又是后缀表达式。定义如下，

- 中缀表达式：运算符位于与其相关的操作数中间，如`(1+2)*(3-4)`。

- 前缀表达式（波兰式）：运算符位于与其相关的操作数前面，如`* + 1 2 - 3 4`。

- 后缀表达式（逆波兰式）：运算符位于与其相关的操作数后面，如`1 2 + 3 4 - *`。

由上述定义不难发现，后缀表达式无需考虑复杂的运算顺序，从左到右依次求值即可。这样一来，即便中缀表达式的运算顺序极为复杂，我们将其转换为后缀表达式后都可以从左到右求值而无需考虑运算优先级等因素，所以后缀表达式更有利于我们求值。

所以逆波兰算法的本质就是将中缀表达式转换为后缀表达式来求值，从而简化表达式求值的流程，避免过多考虑运算符优先级等因素。

### 算法详解

逆波兰算法的核心步骤有2个：

1. 将中缀表达式转换为后缀表达式，例如输入的原始表达式是`(1+2)*(3-4)`，转换得到`1 2 + 3 4 - *`。

2. 根据后缀表达式，按照特定的计算规则得到最终计算结果

接下来本文将分别详细介绍这两个操作。

#### 中缀表达式转换为后缀表达式

首先需要设定一个栈`S`，和一个线性表`L`。`S`用于临时存储运算符，`L`用于存储后缀表达式。遍历原始的中缀表达式中的每一个表达式元素，

- 如果是操作数，则直接追加到`L`中。只有运算符或者分界符才需要保存到`S`中。
- 如果是分界符，

    - 如果是左括号， 则压入`S`，等待下一个最近的右括号与之配对。

    - 如果是右括号，则说明有一对括号已经配对。丢弃右括号，然后从`S`中出栈，得到元素`e`，将`e`依次追加到`L`里。一直循环，直到出栈元素`e`是左括号，同样丢弃这个左括号。

- 如果是运算符（用`op1`表示），
    - 如果`S`栈顶元素（用`op2`表示）不是运算符，则二者没有可比性，则直接将此运算符`op1`压栈。

    - 如果`S`栈顶元素（用`op2`表示）是运算符，则比较`op1`和`op2`的优先级。如果`op1 > op2` ，则直接将此运算符`op1`压栈。否则将`op2`出栈并追加到`L`，再重复执行本操作直到栈空或者满足`op1 > op2`的条件为止，然后将`op1`压栈。  
    也就是说，如果在`S`栈中，存在两个相邻的元素都是运算符，则下层运算符的优先级一定小于上层元素的优先级。

- 最后，如果`S`中还有元素，则依次弹出追加到`L`后得到后缀表达式。

该部分算法的具体流程可以参照下面的动图演示。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/rpn_transform.gif">
</div>

#### 后缀表达式求值

首先需要设定一个栈`S`，遍历后缀表达式中的每一个表达式元素，

- 如果是操作数，则压入`S`中。

- 如果是运算符，则根据运算符的单目或者多目性质，从`S`中弹出相应个数的操作数进行运算，将运算结果压入栈`S`中。

- 最后，栈`S`的栈顶元素是表达式的最终计算结果。

该部分算法的具体流程可以参照下面的动图演示。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/rpn_evaluate.gif">
</div>