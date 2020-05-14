---
layout: post
title: 详解vertical-align属性实现行级元素居中的方法
categories: [CSS]
description: 本文主要分析使用CSS中vertical-align属性，和使用它实现行级元素的垂直居中的一些方法。
keywords: CSS, vertical-align
---

本文讲解的是`line-height`和`vertical-align`。至于各种垂直居中的方法，网上包括掘金里已有不少文章介绍，本文仅讲解使用`vertical-align`属性进行对齐的方式。

在很多项目中，`line-height`和`vertical-align`是使用频率非常高的两个CSS属性。其中`line-height`用于指定文字的行高，`vertical-align`用于指定元素的垂直方向对其方式。但是，我们常常在应用两个属性的过程中，遇到许多预想不到的结果，比如使用`vertical-align:middile`不能实现垂直居中（`vertical-align`无效这个问题是高频提问的问题）。这两个属性关系非常密切，随时随地都存在它们共同作用的结果，要使用好这两个属性，只有深入的理解了他们的作用机理，才能解决实际使用过程中遇到的种种疑惑。

## 几个概念

- 行高：两行文字基线之间的距离。
- 基线：英语字母中的概念，是指书写英语字母时，字母x底部所在的位置，即我们用英语字母本子书写时从上往下，格子中的倒数第二条线。在CSS中值为`baseline`。以下图为行高，基线，以及`vertical-align`属性中，`baseline`，`bottom`，`middle`，`top`几个值的关系。

  <div style="text-align: center;">
      <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/line_height_baseline_intro.png">
  </div>

  > 纠正：上图中的middle描述有错，正确应该为`baseline`往上二分之一`x-height`所在的位置，其中`x-height`指的是字母`x`的高度，关于`x-height`，具体可以查看后文**参考**中《字幕'x'在css世界中的角色和故事》

- 四种内联盒子：在CSS中有四种内联盒子，分别是`containing box`，`inline boxes`，`line box`，`content area`。分别如下：
  - `containing box`：外层盒子模型，包含了其他的盒模型

  - `line boxes`：由一个一个的`inline boxes`组成，一行即为一个`line box`，单个`line box`的高度由其包含的所有`inline boxes`中，高度最大的那个决定（由`line-height`起作用，后面解释），而一个一个的`line box`的高度就堆叠成了`containing box`的高度。

  - `inline boxes`：不会成块显示，而是并排显示在一行的`boxes`，如`span`，`a`，`em`等标签以及匿名`inline boxes`（即不含把标签的裸露的文字）。

  - `content area`：围绕文字看不见的`box`，其大小与`font-size`有关，其高度可以认为鼠标选中文字时背景色的高度(后面解释)。

  以下为几种`boxes`的关系（图片中，粉红色为鼠标选中的文字部分）

  ```html
  <div>
    这里是一个div，里面包含了独立的文字，
    <span>span标签</span>
    <em>em标签</em>，
    以及其他的一些文字。
  </div>
  ```

  <div style="text-align: center;">
      <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/boxes_type.png">
  </div>

  理解四种box非常重要，平时的使用浮动，定位，父级高度自动撑开等表现都是与`boxes`的作用有很大的关系。

## 深入理解行高`line-height`

### `line-height`与`line boxes`的高度关系

现在不少人认为，`line boxes`的高度是由内部文字撑开的，但是实际上并不是文字撑开，而是由`line-height`来决定的。通过以下的比较，我们可以证明这个结论。

```html
<div class="div div1">我是一行文字大小为100px，但是line-height为0的文字</div>
<div class="div div2">我是一行文字大小为0，但是line-height为100px的文字</div>
<div class="div div3">我是一行文字大小和line-height都为100px的文字</div>
```

```css
.div {
  background: #f0f0f0;
  border:  1px solid #e0e0e0;
  margin: 10px;
}
.div1 {
  font-size: 16px;
  line-height: 0;
}
.div2 {
  font-size: 0;
  line-height: 100px;
}
.div3{
  font-size: 16px;
  line-height: 100px;
}
```

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/line_height_font_size_comparison.png">
</div>

结果显而易见，当文字的行高为0时，就算它的字号大小很大，`line box`的高度为0。相反，即使字体大小为0，但如果行高不会0，则会撑开标签的高度。同时，我们发现，行高还有一个特性就是垂直居中性，无论`line boxes`的高度是多少，其里面的文字都是公用一条垂直居中线，利用这一个特性，我们还可以实现一些近似的垂直居中效果。（为什么近似，后面解释）。

### 文本间距

两行文本之间的间距为文本间距，在CSS中，文本上下的间距均为文本间距的一半，即通过设置行高后，行高与字体大小差值将等分于文本上下。设文本顶部和底部的间距为x，行高为y，字体大小为z，则满足：`2x + z = y`.也就是`x = (y - z) / 2`.这就是为什么有时候我们在设置样式的时候文本的大小大于行高，导致多行文本之间会重叠在一起的现象。

### `line-height`值为1.5，150%和1.5em的区别

我们在给`line-height`赋值时，经常设置类似`1.5`，`1.5em`，`150%`的值。但是弄懂几个属性值的区别非常重要。`line-height`属性值具有继承性，即上级设置的属性值会在子级中继承。几个属性的区别在于：

- `1.5`是在子级继承后会重新根据自身的字体大小重新计算行高值
- `1.5em`和`150%`则会在父级计算完行高值后，原封不动的作用于子级元素。看以下例子，可以形象的说明这一特点。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/line_height_diff_values.jpg">
</div>

上图可以看到，当父级元素为的字体为`16px`，行高为`150%`（即`16x150%=24px`）时，最终行高`24px`将会被子元素继承，由于子元素的字体大小为`40px`，行高小于字体大小，根据上面第2点的公式我们可以得到，两行文字之间的距离为`2x = 24 - 40 = -16px`，所以两行文字重叠在了一起；而当`line-height`为1.5时，由于子元素会根据自身字体的大小重新计算行高，即`2x = (40 * 1.5) - 40 = 20px`，得到两行文字之间的距离为`20px`，文本将不会重叠在一起。`1.5em`与`150%`同理。 因此，我们在实际使用的过程中，绝大部分场景应该尽量的少用`em`和百分比值作为`line-height`的值，而使用`1.5`，`2`等不带单位的值替代。

## 深入理解vertical-align

初学者使用`vertical-align`属性时，经常会发现`vertical-align`属性并没有起效果。大部分是因为对于该属性理解不够透彻引起的，只有理解了该属性的特点，表现行为以及与其他属性（如`line-height`）的共同作用机制和效果，才能很好的解决`vertical-align`带来的一些问题，并有效的利用它。

### 起作用的前提

`vertical-align`起作用的前提是盒模型内部元素为`inline`水平元素或`table-cell`元素，包括`span`，`img`，`input`，`button`，`td`以及通过`display`改变了显示水平为`inline`水平或者`table-cell`的元素。这也意味着，默认情况下，`div`，`p`等元素设置`vertical-align`无效。

### 取值

`vertical-align`可以有以下取值方式：

- 关键字：`top`，`middle`，`baseline`(默认值)，`bottom`，`super`，`sub`，`text-bottom`，`text-top`
- 长度值：如`10px`，`-10px`(均为相对于`baseline`偏移)
- 百分比值：如`10%`，根据`line-height`作为基数进行计算后的值。

### 各种属性设置后的表现形式

平时，我们用得最多的应该是`top`，`bottom`，`middle`，`baseline`等几个关键字。而长度值等用得比较少。以下通过例子展示一下各个值的表现形式，并做解析。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/vertical_align_diff_values.jpg">
</div>

以上所有外层标签的行高`为50px`，背景为灰色，所有的对齐方式为对图片进行设置，红色背景文字为图片的兄弟标签`span`。通过观察我们可以看到，各个属性值的表现如下：

- `baseline`：默认的对齐方式，基线对齐，与父元素的基线对齐；
- `top`：与行中的最高元素的顶端对齐，一般是父级元素的最顶端对齐；
- `middle`：与父元素中线对齐（近似垂直居中）；
- `bottom`：与top相反，与父级元素的最低端对齐 ；

- `text-top`：与父级元素`content area`的顶端对齐，不受行高以及周边其他元素的影响。（如图，由于行高为`50`，但为了保证与`content area`顶部对齐，故图片上面有空隙，可以与`top`进行对比）；

- `text-bottom`：与`text-top`相反，始终与父级元素`content area`的低端对齐。同理可以与`bottom`进行对比区分。注意，从图中可以看到，貌似该值的表现行为与`baseline`一致，但仔细观察，可以看到，实际上`text-bottom`所在的线会比`baseline`低一点。

- `数值与百分比`：当数值为正值时，对齐方式将以基线为基准，往上偏移响应的大小，当为负值时，往下偏移。而百分比则是根据行高的大小，计算出相应的数值，再以数值表现的方式进行偏移。

- `super`与`sub`：这两个值均是找到合适的基线作为对齐的基线进行对齐，类似`super`标签和`sub`标签，但不缩放字体大小。

## `line-height`与`vertical-align`的密切关系与应用

一些`vertical-align`和`line-height`相互辅助来进行布局的方法详见下文**参考**中的文章《彻底搞定vertical-align垂直居中不起作用疑难杂症》，这里不再赘述。最后了解几个定义

- `inline-block`基线： 在CSS2可视化格式模型文档中，指出了`inline-block`的基线是正常流中最后一个`line box`的基线，但是，如果这个`line box`里面没有`inline boxes`或者其`overflow`属性值不是`visible`，那么其基线就是`margin bottom`的边缘。什么意思呢？我们用一张图片说明一下：

  <div style="text-align: center;">
      <figure>
          <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/inline_block_baseline.jpg"/>
          <figcaption>纠正：图片上面描述文字中的“红色线为设置了middle”改为“黄色线为设置了middle”</figcaption>
      </figure>
  </div>

- `middle`对齐：指元素的垂直中心线与父级基线往上二分之一`X`所在的位置的线对齐。有点绕，看下图：

  <div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/middle_align.png">
  </div>

- 文字下沉特性：文字是具有下沉特性的，就是文字的垂直中心点在文字所在区域的中线往下沉一点，不同字体的文字下沉的幅度不同，同时，文字大小越大，下沉越明显。如上图，X的中线点相对白色中线往下沉。

## 总结

要掌握彻底`line-height`和`vertical-align`两个属性有很多细节需要去分析去发现，是有点难度的。但是我们如果弄清楚了常见的几个特性和行为，就能避免实践的过程中遇到的一些坑。从而提高工作效率。通过以上的一些分析，总结以下几点：

- 理清`line-height`与`height`之前的关系；
- 理清`vertical-align`不同值的表现行为；
- 理解四种内联盒子的关系；

其实在CSS3发布以后，实现块级元素垂直居中可以使用`Flex`布局，详见我的博客中的文章：[CSS语法基础的一些速记[5]](https://sinestroedmonce.github.io/2020/05/12/css-basic-5/)。而实现行级元素的垂直居中则需要使用`vertical-align`属性或者采用行级元素块级化的方法，使用`Flex`布局解决。

## 参考

- [CSS深入理解vertical-align和line-height的基友关系](https://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/)
- [彻底搞定vertical-align垂直居中不起作用疑难杂症](https://juejin.im/post/5a7d6b886fb9a06349129463)
- [字幕'x'在css世界中的角色和故事](https://www.zhangxinxu.com/wordpress/2015/06/about-letter-x-of-css/)