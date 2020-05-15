---
layout: post
title: 详解CSS中基础的控制文本换行的方法
categories: [CSS]
description: 在使用CSS设计HTML页面样式时经常会遇到文本换行问题。一般来说，最简单的处理文本换行的方法就是使用word-wrap和word-break两个属性。使用这两个属性可以处理一些较不复杂的文本换行需求。
keywords: CSS, word-break, word-wrap
---

我们一般控制换行所用到的CSS属性一共有三个：`word-wrap`、`word-break`、`white-space`。这三个属性可以说是专为了文字断行而创造出来的。本文就简单介绍了这三种属性和如何使用它们来控制文本换行。

## `word-wrap`

`word-wrap`属性在各个浏览器均能识别，其属性值描述如下（默认是`normal`）：

```css
.text {
    word-wrap: normal | break-word
}
```

### 属性值含义

`normal`：允许内容顶开指定的容器边界。  
`break-word`：内容将在边界内换行。必要时会触发`word-break`（注意：请分辨清楚`word-break`和`break-word`这俩个是不同的东西，前者是属性，后者是属性值）。

### 属性说明

`word-wrap`是控制是否“为词断行”的，设置或检索当前行超过指定容器的边界时是否断开转行。如果不存在其他属性（例如`word-break`等）的影响，`word-wrap`的值为`break-word`的情况下，如果文本末端有长单词，则会被放到下一行，并且可能发生截断。

### 实例

一个`word-wrap`的实例如下图所示：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/word_wrap.jpg">
</div>

### Hint

作用范围仅为`div`这类标准块级元素，`th`，`td`这类`table`元素虽然识别但是没有效果（如果为`td`，`th`加上宽度`word-wrap`在IE下是能够发挥效果的）。

## `word-break`

`word-break`属性在各个浏览器均能识别，其属性值描述如下（默认是`normal`）：

```css
.text {
    word-break: normal | break-all | keep-all ｜ break-word
}
```

### 属性值含义

`normal`：默认值，依据默认的换行规则（英文不允许单词内截断）。  
`break-all`：防止文本溢出，单词允许在任意两字符之间截断。  
`keep-all`：与所有非亚洲语言的`normal`相同。对于中文，韩文，日文，不允许字断开。
`break-word`：为了防止文本溢出，单词允许在任意点截断（W3C规则，可能指的是字符不完整的截断）

### 属性说明

`word-break: break-all`，是断开单词。在单词到边界时，下个字母自动到下一行。不会出现长单词被强制放到下一行，然后再发生截断的问题。

### 实例

一个`word-break`的实例如下图所示：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/word_break.jpg">
</div>

### Hint

作用范围仅为`div`这类标准块级元素，`th`，`td`这类`table`元素虽然识别但是没有效果（经测试Chrome下`word-break: break-all`是有效果的）。

## `white-space`

`white-space`属性在各个浏览器均能识别，其属性值描述如下（默认是`normal`）：

```css
.text {
    white-space: normal | pre | nowrap
}
```

### 属性值含义

`normal`：默认。空白会被浏览器忽略。  
`pre`：空白会被浏览器保留。其行为方式类似HTML中的pre标签。  
`nowrap`：文本不会换行，文本会在在同一行上继续，直到遇到br标签为止。

### 实例

一个`white-space`的实例如下图所示：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/white_space.jpg">
</div>
