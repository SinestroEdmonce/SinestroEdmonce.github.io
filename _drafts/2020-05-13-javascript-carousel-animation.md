---
layout: post
title: 原生Javascript实现轮播图
categories: [Javascript]
description: 轮播图的动画效果，就是图片按照规定的方向，不停滚动以展示每一张图片，同时还可以通过点击以改变原本的展示顺序
keywords: Javascript, Carousel
---

这学期的`csci571`的`hw6`要求自己手写一个轮播图，用于展示`Google News`的前五条`headline`新闻。我在实现的时候参考了掘金上的一篇博客（具体博客见下文**参考**），对我纯Javascript实现轮播图带来了很大帮助，故整理到自己的博客中。

轮播图的动画效果，就是图片按照规定的方向，不停滚动以展示每一张图片，同时还可以通过点击以改变原本的展示顺序。实现有一下几点：

- 不做点击操作，图片自行滚动（这里我们规定自右向左滚动）；
- 点击左右箭头，实现图片翻页；
- 点击提示圆点，显示不同图片；
- 功能整合；

## 自动循环滚动

图片自行滚动务必就要使用到定时器`setInterval`或者`setTimeout`，使得移动函数每隔一段时间执行一次，以达到循环滚动的动画效果。

### 定时器的介绍

- `setInterval()`方法会重复调用一个函数或执行一个代码段，在每次调用之间具有固定的时间延迟，返回一个`intervalID`，可以作为参数传给`clearInterval()`来取消该定时器。

- `setTimeout()`方法设置一个定时器，在定时器到期后会执行一个函数或一段代码，返回值`timeoutID`是一个正整数，表示定时器的编号。这个值可以传递给`clearTimeout()`来取消该定时器。

#### `setInterval(*)`

```javascript
let intervalID = window.setInterval(func, delay);
```

- `intervalID`是此重复操作的唯一辨识符，可以作为参数传给`clearInterval()`。
- `func`是想要重复调用的函数。
- `delay`是想要间隔的时间（毫秒数）

#### `setTimeout(*)`

```javascript
var timeoutID = scope.setTimeout(func, delay);
```

- `func`是你想要在`delay`毫秒之后执行的函数。
- `delay`是延迟的毫秒数 (1秒等于1000毫秒)，函数的调用会在该延迟之后发生。

### 实例研究

首先，理解图片的循环滚动，实际上就是多次连续的移动，每次移动固定的距离，若干次的移动，就形成了循环效果，那么此处我们应该首个实现的是移动一次的效果，即移动函数：`movefunc`。

我们先回顾一下各个容器之间的层级关系：

```html
<!--最外层的父级容器-->
    <div class="imgScroll">
        <!--图片展示区-->
        <div class="showContainer">
            <div class="container" style="left: 0px;">
                <img src="https://dimg02.c-ctrip.com/images/100b11000000qezw729A4_R_1600_10000_Mtg_7.jpg" alt="A cat">
                <img src="https://dimg05.c-ctrip.com/images/100u0x000000le38uA71D_R_1600_10000_Mtg_7.jpg" alt="A dog">
                <img src="https://dimg08.c-ctrip.com/images/100811000000qrlfxA3E0_R_1600_10000_Mtg_7.jpg" alt="dandelion">
            </div>
        </div>
        <!--底部提示区-->
        <div class="dots">
            <span class="dot active"></span>
            <span class="dot"></span>
            <span class="dot"></span>
        </div>
        <!--左右箭头-->
        <div class="left-triangle triangle">
            <span>&lt;</span>
        </div>
        <div class="right-triangle triangle">
            <span>&gt;</span>
        </div>
    </div>
```

最外层`div.imgScroll`的是整个实现的父容器，其子元素`div.showContainer`是展示容器，里层`div.ontainer`是滚动容器，该滚动容器相对于展示容器是绝对定位，那么移动函数就是要让滚动容器进行移动，即要改变滚动容器的`style.left`属性。

#### 移动函数：`movefunc(*)`

我们规定了图片要自右向左滚动，即滚动容器每次向左移动一张图片的宽度（记变量为`oneImgWidth`）即`div.container.style.left`每次减小一个`oneImgWidth`。

首先要获取滚动容器，和它的的初始状态。原生方法：`getElementsByClassName()`和`getElementById()`都可以获取DOM文档中的元素，在HTML结构中没有使用`id`进行元素表示，则使用前者，但是要注意该方法获取得到的是一个元素集合`HTMLCollection`，须通过下标的方式获取第一个元素：

```javascript
var container = document.getElementsByClassName("container")[0];    //获取滚动容器
var oldLeftPos = parseInt(container.style.left);    //获取滚动容器的初始状态，并利用parseInt()方法取整
```

**注意：在上面的HTML结构中，你会注意到有一句行内样式`style="left: 0px;"`，这是为了防止`style.left`取不到值而报错。*可以通过`window.getComputedStyle(*)`来直接获取当前的样式值，不过这里就不再详述这个方法。***

然后获取图片的宽度，即为滚动容器每次移动的步长：

```javascript
var oneimgWidth = container.children[0].offsetWidth;    //这是一个整数
```

滚动容器左移：

```javascript
var newLeftPos = oldLeftPos - oneimgWidth;
container.style.left = newLeftPos + "px";   //记得加上单位px
```

这样就实现了一次移动效果，移动函数`movefunc()`：

```javascript
function movefunc() {
    var container = document.getElementsByClassName("container")[0];    //获取滚动容器
    var oneImgWidth = container.children[0].offsetWidth;    //获取一张图片的宽度，即每次左移的距离
    var oldLeft = parseInt(container.style.left);    //获取滚动容器的初始的左距离，利用parseInt()方法取整
    var newLeft = oldLeft - oneImgWidth;
    container.style.left = newLeft + "px";      //改变滚动容器的左距离
}
```

#### 自动滚动函数：`rollAuto(*)`

实现了移动函数，我们开始真正的轮播图实现的第一步动画效果——自动循环滚动，前面已经介绍过定时器的基本使用，现在开始利用定时器和移动函数，实现循环移动形成动画。

创建一个`rollAuto()`定时器函数：

```javascript
function rollAuto() {
  setInterval("movefunc()", 800);   //每隔800毫秒执行一次移动函数
}
rollAuto();     //需要先执行一次定时器函数，才可以开始定时器
```

在轮播图中不进行任何操作时，图片会自行滚动，但是当鼠标移动到展示区中时，滚动的图片则停止滚动，鼠标再移出展示区，则滚动继续，那么在这一进一出的两次操作中，就涉及了两次对于定时器的不同操作，一次清除，一次恢复，其中，移进为清除，移出为恢复。

根据上述定时器的介绍，清除定时器需要一个表示需要被清除定时器的编号，我们使用一个变量`timer`来保存该定时器。

```javascript
var timer = setInterval("movefunc()", 800);   //每隔800毫秒执行一次移动函数
clearInterval(timer);   //清除使移动函数循环执行的定时器
```

鼠标的移动会触发鼠标事件事件，例如此处，鼠标移进会触发`onmouseover`事件，鼠标移出会触发`onmouseout`事件，则我们可以为展示区元素绑定这两个事件：

```javascript
var showContainer = document.getElementsByClassName("showContainer")[0];     //获取展示区元素
//绑定鼠标事件
showContainer.onmouseover = function() {
    clearInterval(timer);
};
showContainer.onmouseout = function() {
    rollAuto();   //鼠标移出，继续执行移动函数
};
```

最后我们将这个自行滚动函数整理一下：

```javascript
function autoRollImg() {
    var showContainer = document.getElementsByClassName("showContainer")[0]; 
    var timer = null;
    function rollAuto() {
        timer =  setInterval("movefunc()", 800); 
    }
    rollAuto();
    showContainer.onmouseover = function() {
        clearInterval(timer);
    };
    showContainer.onmouseout = function() {
        rollAuto();
    };
}
```

最后还需要一些修改。我们希望的是，当最后一张图片滚出展示区后，应该出现第一张图，那么此处就需要做一个极限条件的判断。

```javascript
var container = document.getElementsByClassName("container")[0];    //获取滚动容器
var imgNum = container.children.length;     //获取图片数量
var oneImgWidth = container.children[0].offsetWidth;    //获取每张图的宽度（CSS样式设置中，每张图宽带一样）
//如果最后一张图移出展示区，初始化滚动容器的.style.left属性值
if (parseInt(container.style.left) <= -imgNum * oneImgWidth) {
    container.style.left = "0px";
}
```

最终整合后的函数如下：

```javascript
function autoRollImg() {
    var timer = null;
    var showContainer = document.getElementsByClassName("showContainer")[0]; 
    var container = document.getElementsByClassName("container")[0];  
    var imgNum = container.children.length;  
    var oneImgWidth = container.children[0].offsetWidth;  

    function rollAuto() {
        timer =  setInterval(function(){
            movefunc();
            if (parseInt(container.style.left) <= -imgNum * oneImgWidth) {
                container.style.left = "0px";
            }
        }, 800); 
    }
    rollAuto();
    
    showContainer.onmouseover = function() {
        clearInterval(timer);
    };
    showContainer.onmouseout = function() {
        rollAuto();
    };
}
```

## 点击左右箭头，实现图片翻页

点击向左箭头，滚动容器应该向右移动；点击向右箭头，滚动容器向左移动，这里就出现了一个问题，我们的移动函数，是将`container.style.left`减少一个图片的宽度，即使得图片向左移动，自行滚动函数和点击向右箭头效果与此相同。

但是点击向左箭头，则是朝着另一个方向了，因此，我们之前的移动函数`movefunc`就需要改动了，为了区分两个函数，我们现将改进的移动函数更名为`movement()`，不再仅仅是移动图片了，若是其他元素的移动也就可以重用该函数了。

### 可重用移动函数：`movement(*)`

为了满足两个方向的移动，我们就可以把移动的步长作为参数，传给该函数，函数就可以根据参数的正负值，进行不同方向的移动，如下：

```javascript
function movement(offset) {
    var container = document.getElementsByClassName("container")[0]; 
    var oldLeft = parseInt(container.style.left); 
    var newLeft = oldLeft + offset;
    container.style.left = newLeft + "px";
}
```

这样就完成了一个可重用的移动函数，移动步长可以通过参数控制。在此可以得出，需要滚动容器向左移动时，就传进一个负值，反之则为正值。

那么，`autoRollImg()`函数中就要把原本的移动函数替换为新的移动函数，并传一个负值进去：

```javascript
movement(-oneImgWidth);
```

### 给左右箭头绑定点击事件

同样在此之前，需要先获取元素：

```javascript
var leftTriangle = document.getElementsByClassName("left-triangle")[0];         //左箭头
var rightTriangle = document.getElementsByClassName("right-triangle")[0];       //右箭头
```

在这里点击向右箭头，`container`向左移动；点击向左箭头，`container`向右移动，给左右箭头绑定点击事件：

```javascript
leftTriangle.onclick = function() {
    movement(oneImgWidth);
};
rightTriangle.onclick = function() {
    movement(-oneImgWidth);
};
```

同样，在击左右箭头控制图片移动的时候，也需要进行极限条件的判断，在点击向右箭头的情况中，如果已经是第一张图再点击向右箭头，应该出现的事最后一张图；再点击向左箭头时，如果已经到了最后一张图，再点击向右箭头，应该出现的是第一张图。

```javascript
function clickTriangle() {
    var container = document.getElementsByClassName("container")[0]; 
    var imgNum = container.children.length;  
    var oneImgWidth = container.children[0].offsetWidth;
    leftTriangle.onclick = function() {
        if (parseInt(container.style.left) >= 0) {
            container.style.left = -(imgNum - 1) * oneImgWidth + "px";
        } else {
            movement(oneImgWidth);     
        }
    };
    rightTriangle.onclick = function() {
        if (parseInt(container.style.left) <= -(imgNum - 1) * oneImgWidth) {
            container.style.left = "0px";
        } else {
            movement(-oneImgWidth);
        }
    };
}
```

## 点击提示圆点，显示不同图片

### 给各提示圆点绑定点击事件

```javascript
function clickDots(){
    var container = document.getElementsByClassName("container")[0];  
    var oneImgWidth = container.children[0].offsetWidth;
    var dots = document.getElementsByClassName("dots")[0].children;     //获取提示圆点
    for(var i = 0; i < dots.length; i++){
        (function(n){
            dots[n].onclick = function(){
                container.style.left = -n*oneImgWidth + "px";
            }
        })(i);
    }
}
```

因为循环体中使用了循环变量`i`，需要使用创建匿名函数并立即执行的方式，解决闭包问题。

到此为止，已经实现基本需求，图片自行滚动、鼠标移动到展示区滚动停止、鼠标移出展示区滚动继续、点击向左箭头出现当前展示图的左边的图、点击向右箭头出现当前展示的图片的右边的图片、点击底部提示圆点出现相应图片。

但是还有一个问题，每次图片滚动，和点击左右箭头，底部的提示圆点并没有相应高亮显示。应该达到的效果是第一张图显示时，一个提示圆点应该高亮；第二张图显示时，第二个提示圆点要高亮...最后一张图显示时，最后一个提示圆点高亮。

因此应该有个记号，用来标记每一次展的示图片。因此我们声明这样一个记号叫做`index`，用来标识当前展示的图片，其初始状态为0，即展示区显示第一张图。

### 记号的变化分析

滚动容器`div.container`每向左滚动一次，`index`就应增加1，而当`index`增加到为`imgNum`时，即最后一张图恰好左移出展示区时，应该展示第一张图片，即`index`要重置为0；滚动容器`div.container`向右滚动时，则与此相反。

不论是自行滚动函数，还是点击事件函数，都会引起记号`index`的变化，所以我们需要将`index`的声明和初始化，放在这些所有函数的最外层，极限条件的判断放进每一个动画函数`autoRollImg()`、`clickTriangle()`、`clickDots()`里面：

```javascript
//点击向右箭头
rightTriangle.onclick = function() {
    if (parseInt(container.style.left) <= -(imgNum - 1) * oneImgWidth) {
        container.style.left = "0px";
    } else {
        movement(-oneImgWidth);
    }
    index = index + 1;
    if (index > imgNum - 1) {
        index = 0;
    }
};

//点击向左箭头
leftTriangle.onclick = function() {
    if (parseInt(container.style.left) >= 0) {
        container.style.left = -(imgNum - 1) * oneImgWidth + "px";
    } else {
        movement(oneImgWidth);     
    }
    index = index - 1;
    if (index < 0) {
        index = imgNum - 1;
    }
};
```

自行滚动函数修改如下：

```javascript
function autoRollImg() {
    var timer = null;
    var showContainer = document.getElementsByClassName("showContainer")[0]; 
    var container = document.getElementsByClassName("container")[0];  
    var imgNum = container.children.length;  
    var oneImgWidth = container.children[0].offsetWidth;  

    function rollAuto() {
        timer =  setInterval(function(){
            index++;
            if (index > imgNum - 1) {
                index = 0;
            }
            movement(-oneImgWidth);
            if (parseInt(container.style.left) <= -imgNum * oneImgWidth) {
                container.style.left = "0px";
            }
        }, 800); 
        if (parseInt(container.style.left) <= -imgNum * oneImgWidth) {
            container.style.left = "0px";
        }
    }
    rollAuto();
    
    showContainer.onmouseover = function() {
        clearInterval(timer);
    };
    showContainer.onmouseout = function() {
        rollAuto();
    };
}
```

### 高亮函数：`showDots(*)`

有了记号，则需要一个高亮显示的函数，根据记号`index`来让底部提示圆点高亮显示，我们先获取包含提示圆点的圆点容器，然后获取他的子元素，即`<span class="dot"></span>`这个小圆点，遍历这些元素，对比它们的下标，如果和记号`index`相同，则改变该提示圆点为高亮，即添加一个`active`类。

```javascript
function showDots(target) {
    var dots = document.getElementsByClassName("dots")[0].children;
    for (var i = 0; i < dots.length; i++) {
        if (i !== target) {
            dots[i].classList.remove("active");       //增加表示高亮的类名
        } else {
            dots[i].classList.add("active");      //去掉高亮
        }
    }
}
```

注意：由于点击底部提示圆点，会使得记号`index`有跳动，即不是`index`的变化的绝对值并不一定在1之内，不仅仅是+1和-1的变化。自行滚动函数是无限循环执行的，当点击过底部提示圆点后，记号值倍打乱，所以要将`index`值返回出去，以便自行滚动函数可以获取：

```javascript
function clickDots(){
    for(var i = 0; i < dots.length; i++){
        (function(n){
            dots[n].onclick = function(){
                container.style.left = -n*oneImgWidth + "px";
                index = n;
                showDots(index);
            }
        })(i);
    }
    return index;       //返回记号值
}
```

## 功能整合

现在终于大功告成了，每一部分的功能都已经实现，现在就只需要将所有的函数整合了，别掉以轻心，整合函数依然是一个不可忽视的步骤，这其中你可能会发现一些问题，这些问题将大大降低用户的体验。那么现在来开始整合函数。

为了避免出现全局变量，我们将所有的函数和变量全部放进一个函数中，声明此函数名为`slideShow()`，为实现HTML文档加载完就开始执行这个函数，需要使用`window.onload()`方法：

```javascript
window.onload = function() {
    slideShow();
}
```

### Javascript代码

我们在多个函数中都重复获取了DOM元素，现在我们把获取元素放在`slideShow()`这个函数的首部。

```javascript
function autoRollImg() {
    var timer = null; 
    var showContainer = document.getElementsByClassName("showContainer")[0]; 
    var container = document.getElementsByClassName("container")[0];  
    var imgNum = container.children.length;  
    var oneImgWidth = container.children[0].offsetWidth;  
    var leftTriangle = document.getElementsByClassName("left-triangle")[0];
    var rightTriangle = document.getElementsByClassName("right-triangle")[0];
    var dots = document.getElementsByClassName("dots")[0].children;     //获取提示圆点
    var index = 0;
    
    function showDots(target) {
        var dots = document.getElementsByClassName("dots")[0].children;
        for (var i = 0; i < dots.length; i++) {
            if (i !== target) {
                dots[i].classList.remove("active");       //增加表示高亮的类名
            } else {
                dots[i].classList.add("active");      //去掉高亮
            }
        }
    }

    function movement(offset) {
        var oldLeft = parseInt(container.style.left); 
        var newLeft = oldLeft + offset;
        container.style.left = newLeft + "px";
      }

    
    showContainer.onmouseover = function() {
        clearInterval(timer);
    };
    showContainer.onmouseout = function() {
        rollAuto();
    };

    function clickTriangle() {
        leftTriangle.onclick = function() {
            if (parseInt(container.style.left) >= 0) {
                container.style.left = -(imgNum - 1) * oneImgWidth + "px";
            } else {
                movement(oneImgWidth);     
            }
            index = index - 1;
            if( index < 0){ index = imgNum - 1;}
            showDots(index);
        };
        rightTriangle.onclick = function() {
            if (parseInt(container.style.left) <= -(imgNum - 1) * oneImgWidth) {
                container.style.left = "0px";
            } else {
                movement(-oneImgWidth);
            }
            index = index + 1;
            if( index > imgNum - 1){ index = 0;}
            showDots(index);
        };
    }
    clickTriangle() 

    function clickDots(){
        for(var i = 0; i < dots.length; i++){
            (function(n){
                dots[n].onclick = function(){
                    container.style.left = -n*oneImgWidth + "px";
                    index = n;
                    showDots(index);
                }
            })(i);
        }
        return index;
    }
    clickDots()

    function rollAuto() {
        timer =  setInterval(function(){
            movement(-oneImgWidth);
            index = index + 1;
            if( index > imgNum - 1){ index = 0;}
            showDots(index);
            if (parseInt(container.style.left) <= -imgNum * oneImgWidth) {
                container.style.left = "0px";
            }
        }, 2000); 
    }
    rollAuto();
}

function slideShow() {
    autoRollImg()
}

window.onload = function() {
    slideshow()
}
```

### HTML代码：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>img scroll</title>
    <link rel="stylesheet" href=".layout.css">
    <script src="./scrollImg.js"></script>
</head>

<body>
    <div class="imgScroll">
        <div class="showContainer">
            <div class="container">
                <img src="https://dimg02.c-ctrip.com/images/100b11000000qezw729A4_R_1600_10000_Mtg_7.jpg" alt="A cat">
                <img src="https://dimg05.c-ctrip.com/images/100u0x000000le38uA71D_R_1600_10000_Mtg_7.jpg" alt="A dog">
                <img src="https://dimg08.c-ctrip.com/images/100811000000qrlfxA3E0_R_1600_10000_Mtg_7.jpg" alt="dandelion">
            </div>
        </div>
        <div class="dots">
            <span class="dot active"></span>
            <span class="dot"></span>
            <span class="dot"></span>
        </div>
        <div class="left-triangle triangle">
            <span>&lt;</span>
        </div>
        <div class="right-triangle triangle">
            <span>&gt;</span>
        </div>
    </div>

</body>
</html>
```

### CSS代码

```css
.imgScroll {
    width: 800px;
    margin: 0 auto;
    position: relative;
}

.imgScroll .showContainer {
    width: 800px;
    height: 533px;
    margin: 0 auto;
    overflow: hidden;
    position: relative;
}

.imgScroll .showContainer .container {
    width: 9999px;
    position: absolute;
    left: 0px;
}

.imgScroll .showContainer .container img {
    display: block;
    float: left;
    width: 800px;
}

.imgScroll .triangle {
    position: absolute;
    top: 40%;
    cursor: pointer;
    font-size: 40px;
    color: lightgray;
}

.imgScroll .triangle:hover {
    color: gray;
}

.imgScroll .triangle.left-triangle {
    left: -50px;
}

.imgScroll .triangle.right-triangle {
    right: -50px;
}

.imgScroll .dots {
    width: 100%;
    text-align: center;
    height: 50px;
    line-height: 50px;
    cursor: pointer;
}

.imgScroll .dots .dot {
    display: inline-block;
    width: 10px;
    height: 10px;
    border-radius: 50%;
    background: lightgray;
     margin-right: 10px;
}

.imgScroll .dots .dot.active {
    background-color: gray;
}
```

## 参考

[原生JS实现轮播图--第二章动画实现](https://juejin.im/post/5b8d4171f265da436e74e5c1)