---
title: var a = {} && []; a = ?
tags: JavaScript
categories: 技术
date: 2018-1-31 20:34:14
---

> 逻辑运算符通常用于Boolean型（逻辑）值。这种情况，它们返回一个布尔型值。然而，&&和||运算符实际上返回一个指定操作数的值，因此这些运算符也用于非布尔型，它们返回一个非布尔型值。

<!-- more -->

今天在看GitHub上看别人的源代码的时候，发现有这么一行:

```
// 精简后，类似如下
var a = {} && []
```

当时我是有点懵逼的。

答案是：a = [];

后来我去翻了[MDN: 逻辑与（&&）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_AND)，才知道js还能这么玩。

### 逻辑与（&&）

| 运算符 | 示例 | 说明 |
|:-----|:-----|:-----|
| && | A && B  | 如果 A 能转为 false 则返回 A , 否则返回 B .|

如果A能转换成false则返回A，否则返回B。

因此，与布尔值一起使用时，如果两个操作数都为true时&&返回true，否则返回false。

```
0 && 3             // f && t 结果为 0
```

看下面的例子会更清晰
```
true && true       // t && t 结果为 true
true && false      // t && f 结果为 false
false && true      // f && t 结果为 false
false && (3 == 4)  // f && f 结果为 false
"Cat" && "Dog"     // t && t 结果为 Dog
false && "Cat"     // f && t 结果为 false
"Cat" && false     // t && f 结果为 false
```

### 逻辑或（||）
| 运算符 | 示例 | 说明 |
|:-----|:-----|:-----|
| &#124;&#124; | A &#124;&#124; B  | 如果 A 能转为 true 则返回 A , 否则返回 B .|

如果A能转换成true则返回A，否则返回B。

因此，与布尔值一起使用时，如果任意一个操作数为true时，结果就返回true。

```
console.log(1 || true); // 1
```

看下面的例子会更清晰

```
true || true       // t || t 结果为 true
false || true      // f || t 结果为 true
true || false      // t || f 结果为 true
false || (3 == 4)  // f || f 结果为 false
"Cat" || "Dog"     // t || t 结果为 Cat
false || "Cat"     // f || t 结果为 Cat
"Cat" || false     // t || f 结果为 Cat
```

### 小结
JavaScript中的逻辑运算符的结果并不是只有`true`或者`false`