---
title: JavaScript模块化之CommonJS
tags: [JavaScript]
categories: 前端
date: 2019-01-01 21:34:19
---

> CommonJs 是由 JavaScript 社区于 2009 年提出的包含模块、文件、IO、控制台在内的一系列标准。

<!-- more -->

### 模块

CommonJs 中规定每个文件是一个模块。

将一个 JavaScript 文件直接通过 script 标签插入页面中与封装成 CommonJS 模块最大的不同在于，前者的作用域是全局作用域，在进行变量以及函数声明的时候会污染全局环境，而后者会形成一个属于模块自身的作用域，所有的变量以及函数只有自己能访问，对外是不可见的。

比如下面这个例子，a.js 和 index.js 里面都有相同名称的 a 变量，但是他们互不影响。

```js
// a.js

let a = 'a.js'
```

```js
// index.js

require('./a.js')

let a = 'index.js'

console.log(a) // index.js
```

### 怎么导出

在 CommonJS 中，通过`module.exports`可以导出模块中的内容

```js
module.exports = {
  name: '小明',
  age: 14
}
```

module.exports 用来指定该模块要对外暴露哪些内容。

为了书写方便，CommonJS 也支持另一种简化的导出方式，也就是直接使用 exports。

```js
exports.name = '小明'
exports.getFamilyName = function() {
  return '李'
}
```

在使用 exports 时要注意一个问题，即不要直接给 exports 赋值，否则会导致其失败。

```js
// 错误写法❌

exports = {
  name: '小明'
}
```

### 怎么导入

在 CommonJS 中，通过`require`进行模块导入

当我们 require 一个模块时会有两种情况

- require 的模块是第一次被加载过的，这时候辉收先执行该模块，然后导出内容。
- require 的模块曾经被加载过，这时候该模块的代码不会再次执行，而是直接导出上次执行后得到的结果

```js
// add.js

module.exports = {
  add: function(a, b) {
    return a + b
  }
}
```

```js
// index.js

const add = require('./add.js')

const result = add(1, 3)

console.log(result) // 4
```

require 函数可以接受表达式，借助这个特性我们可以动态地指定模块加载路径

```js
const moduleNames = ['a.js', 'b.js']

moduleNames.forEach(name => {
  require(`./${name}`)
})
```

CommonJS 用同步的方式加载模块。在服务端，模块文件都存在本地磁盘，读取非常快，所以这样做不会有问题。但是在浏览器端，限于网络原因，更合理的方案是使用异步加载。
