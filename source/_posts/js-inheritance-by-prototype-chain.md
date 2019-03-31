---
title: 通过原型链来实现JavaScript继承
tags: [JavaScript]
categories: 前端
date: 2019-03-31 11:24:19
---

> 今天这篇文章将介绍如何使用原型链来实现 JavaScript 的继承。

<!-- more -->

## 原型链

JavaScript 中的每个函数中都有一个指向某一对象的 prototype 属性。该函数被 new 操作符调用时会创建并返回一个对象，

比如我们有个 Person（人） 的构造函数

```js
function Person() {
  this.name = "person"
  this.toString = function () {
    return this.name
  }
}
```

有个 Student（学生）的构造函数

```js
function Student() {
  this.className = "一年级一班"
  this.name = "student"
}
```

还有个 DiligentStudent（聪明学生）的构造函数

```js
function DiligentStudent() {
  this.name = "diligent student"
  this.say = function () {
    console.log(`i am a ${this.name}`)
  }
}
```

学生首先是个人，聪明的学生也是学生。那如何使他们有继承关系？

```js
// 关键代码

// 使用Person的实例覆盖Student的prototype
Student.prototype = new Person()

// 使用Student的实例覆盖DiligentStudent的prototype
DiligentStudent.prototype = new Student()

const student = new Student()
const dStudent = new DiligentStudent()

// 这样，student对象也能使用toString方法
student.toString()

// dStudent对象也能访问到className这个变量
console.log(dStudent.className) // 一年级一班
```

这样一番操作之后，student 能访问 toString 函数了。dStudent 也有自己的 className 了。

以上实现继承的原理就是`原型链`。因为在原型链中，我们可以访问某个属性不在 A 对象而在 B 对象的时候，仍然当作是访问 A 的属性。这样每个对象都能访问它原型链上的任何一个属性。

### 缺点

#### 1. constructor 属性

用一个新的实例完全替换对象的 prototype 属性会对对象的 constructor 属性有副作用。

所以在完成相关的继承关系设定之后，一个好的习惯是重置对象的 constructor 属性。

```js
// 重置Student对象的 constructor 属性
Student.prototype.constructor = Student

// 重置DiligentStudent对象的 constructor 属性
DiligentStudent.prototype.constructor = DiligentStudent
```

#### 2. 引用类型的属性被所有实例共享

假设现在学生的构造函数内部多了一个课程的属性，是一个数组。

```js
function Student() {
  this.className = "一年级一班"
  this.name = "student"
  // 默认的课程有语文、数学、英语
  this.course = ["Chinese", "Math", "English"]
}

const student = new Student()
const dStudent = new DiligentStudent()

// 添加多一个Music
dStudent.course.push("Music")

// ↓ ["Chinese", "Math", "English", "Music"]
console.log("更新后", dStudent.course)

const dStudent2 = new DiligentStudent()

// ↓ ["Chinese", "Math", "English", "Music"]
console.log(dStudent2.course)

// 然后你就会发现之后所有DiligentStudent的实例的course数组都被修改了。
```

https://mp.weixin.qq.com/s/hTO3Z0Tm1v1O4ar2zpsBpQ
