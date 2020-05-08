---
layout: post
title: Javascript面向对象与原型
categories: [Javascript, OOP]
description: Javascript面向对象编程相关的一些基础知识点笔记，关于对象和原型之间的关系等概念的速记。
keywords: Javascript, JS, OOP
---

对于Javascript而言对象其实就是键值对集合，而Javascript中几乎任何东西都可以看作是一个对象，所以Javascript同样也可以实现面向对象编程。但是在Javascript中并不区分类和实例，没有所谓类和类实例的概念。Javascript通过原型（prototype）和原型链来实现传统面向对象编程中类与类实例之间的关系。

## 类与类实例  

类和实例是大多数面向对象编程语言的基本概念。

- 类是对象的类型模板，例如，定义`Student`类来表示学生，类本身是一种类型，`Student`表示学生类型，但不表示任何具体的某个学生；

- 实例是根据类创建的对象，例如，根据`Student`类可以创建出`xiaoming`等多个实例，每个实例表示一个具体的学生，他们全都属于`Student`类型。

## 原型

比方说我们想要创建类似于上述概念中的`Student`和`xiaoming`这样的关系。那么原型是指当我们想要创建`xiaoming`这个具体的学生时，我们并没有一个`Student`类型可用，但是我们有个叫做`Student`的对象：

```javascript
var Student = {
    name: 'student',
    height: 1.7,
    run: function () {
        console.log(this.name + ' is running...');
    }
};
```

根据Javascript中原型和原型链的概念，我们可以使用`Student`对象来创建`xiaoming`这个对象：

```javascript
var xiaoming = {
    name: 'Xiaoming'
};

xiaoming.__proto__ = Student;
```

注意到上述代码的最后一行把`xiaoming`中键名是`__proto__`的属性（成员）指向了对象`Student`，这样一来`xiaoming`就可以使用`Student`对象中的方法，而相同键名的属性（成员）则似乎会被覆盖：

```javascript
xiaoming.name; // 'Xiaoming'
xiaoming.run(); // Xiaoming is running...
```

`xiaoming`有自己的`name`属性，但并没有定义`run()`方法。不过，由于小明是从`Student`继承而来或者说创建而来，只要`Student`有`run()`方法，`xiaoming`也可以调用。而所谓的相同键名的成员也并不是被覆盖，只是因为`xiaoming`本身有`name`属性，所以直接调用`xiaoming.name`得到的就是`xiaoming`这个对象自己的`name`。如果调用`xiaoming.__proto__.name`自然也能获取`Student`对象中的`name`属性。这样一看，Javascript中原型和原型链的概念，既可以说像类的实例化，也可以说像类的继承。同样都是对象，`xiaoming`由于继承了`Student`而获得了`Student`中的方法和属性。

```
xiaoming ---__proto__---> Student
- name                  - name
                        - height
                        - run()
```

那么在`xiaoming`中调用`run()`方法，由于`xiaoming`本身并没有这一属性（成员），于是Javascript会自动向它的原型中去查找，顺着原型链一直找到尽头（也就是null），如果存在这样一个方法则调用，不存在则会报错。

如果你把`xiaoming`的原型指向其他对象：

```javascript
var Fish = {
    swim: function () {
        console.log(this.name + ' is swimming...');
    }
};

xiaoming.__proto__ = Fish;
```

现在`xiaoming`已经无法`run()`了，它和它的原型链中都不存在`run()`，它只能调用`swim()`

```javascript
xiaoming.swim(); // xiaoming is swimming...
```

在JavaScrip代码运行时期，你可以把`xiaoming`从`Student`变成`Fish`，或者变成任何对象。

## Hint

在编写JavaScript代码时，最好不要直接用`obj.__proto__`去改变一个对象的原型，而且低版本的IE也无法使用`__proto__`。`Object.create()`方法可以传入一个原型对象，并创建一个基于该原型的新对象，但是新对象什么属性都没有，因此，我们可以编写一个函数来创建`xiaoming`：

```javascript
// Prototype
var Student = {
    name: 'student',
    height: 1.7,
    run: function () {
        console.log(this.name + ' is running...');
    }
};

function createStudent(name) {
    // Create a new object inherited from Student
    var student = Object.create(Student);
    // Initialize
    student.name = name;
    return student;
}

var xiaoming = createStudent('Xiaoming');
xiaoming.run(); // Xiaoming is running...
xiaoming.__proto__ === Student; // true
```

这是创建原型继承的一种方法，JavaScript也还有其他方法来创建对象.