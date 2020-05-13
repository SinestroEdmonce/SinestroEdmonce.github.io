---
layout: post
title: HTML5中div横向排列的方法。
categories: [Html5]
description: 一般情况下，HTML5中div盒子模型默认排列是垂直排列，这篇文章简单介绍了几种将div盒模型水平排列的方法。
keywords: Html5, layout
---

一般情况下，HTML5中div盒子模型默认排列是垂直排列，这篇文章简单介绍了几种将div盒模型水平排列的方法。

以下面这组 div 为例，wrap 的高度由内容撑开：

```html
<div id="wrap">
    <div id="div1">div1</div>
    <div id="div2">div2</div>
    <div id="div3">div3</div>
</div>
```

平时是这样的，上下排列：

<div style="text-align: center">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/div_vertical_align.png">
</div>

## `float`浮动

```css
#div1{
    float: left;
}
#div2 {
    float: right;
}
#div3 {
    float: right;
}
```

显示效果如下图所示：

<div style="text-align: center">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/float_align.png">
</div>

### `float`的特点

- 多个div右浮动时，顺序会颠倒，请注意看div2和div3，可以通过将它们再用一个div包起来，然后对它们设置左浮动，对父div设置右浮动来解决。

- 脱离文档流，若父元素高度由内容撑开，那么就撑不开，从图中可以看到wrap没了，因为高度变为了0，可通过清浮动来解决。

- 文字会环绕在浮动元素周围，图中未表现出来。

- 不能换行，图中未表现出来。

## `inline-block`行块标签

```css
#div1, #div2, #div3{
    display: inline-block;
}
```

显示效果如下图所示：

<div style="text-align: center">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/inline_align.png">
</div>

### `inline-block`特点

- 元素间会有空白。这个空白其实是空白符，因为`inline-block`会使元素在行内排列，也就是跟文字在一起排列，而我们源代码中div和div之间的空格、Tab、换行符在浏览器里会被合并成一个空白符，所以就会出现缝隙，常见的解决方案有：

    - 通过给父元素设置`font-size: 0;`，使空白符不可见。但会导致子元素中继承的字体大小也为0，解决方案： 

        - 可以明确子元素内字体大小的，为其单独设置文字大小。
        - 可以使用 rem 作为字体大小单位来继承 HTML 根元素的字体大小属性。

    - 在源代码里把前一个div的结束标签和后一个div的开始标签贴在一起。可读性极差，丑拒。

        ```css
        <div id="wrap">
            <div id="div1">div1
            </div><div id="div2">div2
            </div><div id="div3">div3</div>
        </div>
        ```

- 可以换行，如下图：

    ![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/inline_block_wrap.png)


## `flex`弹性盒模型

最佳解决方案是使用`flex`弹性盒模型布局，给父元素设置`display: flex;`即可。

```css
#wrap{
    display: flex;
}
```

显示效果如下图所示：

<div style="text-align: center">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/flexbox_align.png">
</div>

可以通过`justify-content`属性调整子元素的水平对齐方式：

```css
#wrap{
    display: flex;
    justify-content: flex-start;
}
```

`flex`使用不再深入探讨，在我的博客里也有专门文章详解`flexbox`布局的使用：[CSS语法基础的一些速记[5]](https://sinestroedmonce.github.io/2020/05/12/css-basic-5/)。

## 参考

[html5:div的横向排列方法](https://blog.csdn.net/hunhun1122/article/details/78591103)