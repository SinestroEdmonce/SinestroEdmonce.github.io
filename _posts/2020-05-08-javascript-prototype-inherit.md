---
layout: post
title: Javascript原型继承
categories: [Javascript, OOP]
description: Javascript面向对象编程相关的一些基础知识点笔记，关于对象和原型之间的关系等概念的速记。
keywords: Javascript, JS, OOP
---

在传统的基于Class的语言如Java、C++中，继承的本质是扩展一个已有的Class，并生成新的Subclass。
由于这类语言严格区分类和实例，继承实际上是类型的扩展。但是，Javascript由于采用原型继承，我们无法直接扩展一个Class，因为根本不存在Class这种类型。

## 继承

但是办法还是有的。我们先回顾Student构造函数：

```javascript
function Student(props) {
    this.name = props.name || 'Unnamed';
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
}
```

以及``Student``的原型链：

```
Student                                             Object
- prototype --->|                                   - prototype
        |       |-----> Prototype Object1                |
        |<------|<----- - constructor          |-------->|---> Prototype Object2        |----> null
                        - __proto__ ---------->|               - __proto__ ------------>|
```

现在，我们要基于`Student`扩展出`PrimaryStudent`，可以先定义出`PrimaryStudent`：

```javascript
function PrimaryStudent(props) {
    // 调用Student构造函数，绑定this变量:
    Student.call(this, props);
    this.grade = props.grade || 1;
}
```

但是，调用了`Student`构造函数不等于继承了`Student`，`PrimaryStudent`创建的对象的原型是：

```
new PrimaryStudent().__proto__ ----> PrimaryStudent.prototype ----> Object.prototype ----> null
```

必须想办法把原型链修改为：

```
new PrimaryStudent().__proto__ ----> PrimaryStudent.prototype ----> Student.prototype ----> Object.prototype ----> null
```

这样，原型链对了，继承关系就对了。新的基于`PrimaryStudent`创建的对象不但能调用`PrimaryStudent.prototype`定义的方法，也可以调用`Student.prototype`定义的方法。如果你想用最简单粗暴的方法这么干：

```javascript
PrimaryStudent.prototype = Student.prototype;
```

是不行的！如果这样的话，`PrimaryStudent`和`Student`共享一个原型对象，那定义`PrimaryStudent`就没有意义了？

我们必须借助一个中间对象来实现正确的原型链，这个中间对象的原型要指向Student.prototype。为了实现这一点，中间对象可以用一个空函数F来实现，并且把“继承”这个动作用一个inherits()函数封装起来，还可以隐藏F的定义，并简化代码：

```javascript
function inherits(Child, Parent) {
    // 空函数F
    var F = function () {};
    F.prototype = Parent.prototype;
    Child.prototype = new F();
    Child.prototype.constructor = Child;
}

function Student(props) {
    this.name = props.name || 'Unnamed';
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
}

function PrimaryStudent(props) {
    Student.call(this, props);
    this.grade = props.grade || 1;
}

// 实现原型继承链:
inherits(PrimaryStudent, Student);

// 绑定其他方法到PrimaryStudent原型:
PrimaryStudent.prototype.getGrade = function () {
    return this.grade;
};

// 验证原型:
xiaoming.__proto__ === PrimaryStudent.prototype; // true
xiaoming.__proto__.__proto__ === Student.prototype; // true

// 验证继承关系:
xiaoming instanceof PrimaryStudent; // true
xiaoming instanceof Student; // true
```

注意，函数F仅用于桥接，我们仅创建了一个`new F()`实例，而且，没有改变原有的Student定义的原型链。

## 小结

Javascript的原型继承实现方式就是：

- 定义新的构造函数，并在内部用`call()`调用希望“继承”的构造函数，并绑定this；
- 借助中间函数F实现原型链继承，最好通过封装的`inherits`函数完成；
- 继续在新的构造函数的原型上定义新方法。
