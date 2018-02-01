---
title: 栈
tags: [数据结构]
categories: 数据结构与算法
date: 2018-02-01 11:19:14
---

> 堆栈（英语：stack）又称为栈或堆叠，是计算机科学中的一种抽象数据类型，只允许在有序的线性数据集合的一端（称为堆栈顶端，英语：top）进行加入数据（英语：push）和移除数据（英语：pop）的运算。

<!-- more -->

### 概念

栈是一种遵从后进先出(LIFO)原则的有序集合。新添加的或待删除的元素都保存在栈的末尾，称作`栈顶`，另一端就叫`栈底`。在栈里，新元素都靠近栈顶，旧元素都接近栈底。

### 实现

下面就来看看如何用 JavaScript 实现栈这种数据结构。

```js
/**
 * 新建一个Stack类来表示栈
 */
function Stack() {
  // 1. 用数据来保存数据
  this.data = [];
}

// 2. 添加一个新元素到栈顶
Stack.prototype.push = function (element) {
  this.data.push(element);
};

// 3. 移除栈顶的元素，同时返回被移除的元素
Stack.prototype.pop = function () {
  return this.data.pop();
};

/**
 * 4. 获取栈里最后添加的元素，返回栈顶的元素，不对栈做任何修改(这个方法不会移除栈顶的元素，仅仅返回它)。
 */
Stack.prototype.peek = function () {
  return this.data[this.data.length - 1];
};

// 5. 检查是否为空
Stack.prototype.isEmpty = function () {
  return this.data.length == 0;
};

// 6. 获取栈的大小
Stack.prototype.size = function () {
  return this.data.length;
};

// 7. 清空栈
Stack.prototype.clear = function () {
  this.data = [];
};

// 8. 输出栈的数据
Stack.prototype.print = function () {
  console.log(this.data.toString());
};

const stack = new Stack();

stack.push(0);
stack.push(1);
stack.push(2);

console.log(stack.size()); // 3
```

### 应用

+ 十进制转化为任意进制

+ 判断一个字符串是不是回文
