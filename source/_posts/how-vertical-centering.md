---
title: 水平垂直居中的实现方式
tags: [CSS]
categories: 前端
date: 2017-11-01 12:34:19
---

总结三种常用的水平垂直居中的实现方式

<!-- more -->

### absolute + margin偏移

```CSS
.wrapper {
    position: relative;
    width: 400px;
    height: 400px;
    background: #000;
    }
.inner {
    width: 200px;
    height: 200px;
    position: absolute;
    top: 50%;
    left: 50%;
    margin-top: -100px;
    margin-left: -100px;
    background: #fff;
}
```

这种是很常见的实现方式，通常适用于绝对定位元素的宽高为固定值的时候。

### flex + margin: auto

```CSS
.wrapper {
    width: 400px;
    height: 400px;
    display: flex;
    background: #000;
      }
.inner {
    width: 200px;
    height: 200px;
    margin: auto;
    background: #fff;
}
```

如果不考虑浏览器的兼容性，那用Flexbox就肯定是最方便的了，因为它的出现就是为了解决这样的问题。

完成这个需求只需要两个样式，在父元素上设置display:flex和当前元素上设置margin:auto。

### flex

```CSS
.wrapper {
    width: 400px;
    height: 400px;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #000;
    }
.inner {
    width: 200px;
    height: 200px;
    background: #fff;
}
```
