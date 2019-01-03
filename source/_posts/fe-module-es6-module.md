---
title: JavaScript模块化之ES6Module
tags: [JavaScript]
categories: 前端
date: 2019-01-03 22:49:09
---

> 一直到 2015 年 6 月，TC39 标准委员会正式发布了 ES6（ECMAScript 6.0）。从此 JavaScript 语言才正式具备了模块这以特性

<!-- more -->

### 模块

ES6 Module 中规定每个文件是一个模块（这一点和 CommonJS 一致），不同的是，`import` 和 `export` 作为`保留字`在 ES6 版本中加入进来，而 CommonJS 中的 `module` 并不是保留字

ES6 Module 会自动采用严格模式，这在 ES5 中是一个可选项。

### 怎么导出

在 ES6 Module 中，通过`export`可以导出模块中的内容，export 有两种形式

- 命名导出
- 默认导出

```js
// 命名导出

export const a = 'a'
export const add = function(a, b) {
  return a + b
}
```

```js
// 默认导出

const a = 'a'
const add = function(a, b) {
  return a + b
}

export { a, add }
```

在使用命名导出时，可以通过`as`关键字对变量重命名

```js
// 命名导出

const a = 'a'
const add = function(a, b) {
  return a + b
}

// 在导入的时候就是a和addNumber
export { a, add as addNumber }
```

### 怎么导入

在 ES6 Module 中，通过`import`语法可以导入模块中的内容。

```js
// add.js

export default {
  a: 'a',
  add: function(a, b) {
    return a + b
  }
}
```

```js
// index.js

// 使用关键字as
import { add, a } from './add.js'

const result = add(1, 3)

console.log(result) // 4
console.log(a) // a
```

加载带有命名导出的模块时，import后面要跟一对大括号来将导入的变量名包裹起来，并且这些变量名需要与导出的变量名`完全一致`，并且导入的变量都是只读的，不允许被修改。

与命名导出类似，可以通过`as`关键字对变量重命名

```js
// index.js

import { add as addNumber, a } from './add.js'

const result = addNumber(1, 3)

console.log(result) // 4
console.log(a) // a
```

如果要导入的变量太多，我们还可以采用整体导入的方式

```js
// index.js

import * as AddUtil from './add.js'

const result = AddUtil.add(1, 3)

console.log(result) // 4
console.log(AddUtil.a) // a
```

还有一种混合导入的情况

```js
import React, { Component } from 'react'
```

这里的`React`对应的是该模块的默认导出，而`Component`则是其命名导出中的一个变量，

而且这里的`React`必须写在花括号的前面，顺序不能颠倒，否则会提示语法错误。
