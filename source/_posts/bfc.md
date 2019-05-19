---
title: CSS中的BFC
tags: [CSS]
categories: 前端
date: 2019-05-19 21:49:19
---

> CSS 中有个重要的概念 BFC，它会在你书写样式的时候不知不觉的影响你的 CSS 代码。了解 BFC 有助于我们写出更好的 CSS 代码

<!-- more -->

### 什么是 BFC

BFC（Block Formatting Context） 中文的意思就是，块级格式化上下文。

它是页面中的一块渲染区域，有一套渲染规则，决定了其子元素如何布局，以及和其他元素之间的关系和作用。而且 BFC 是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面元素。

### 如何创建 BFC

下列方式会创建块格式化上下文：

常见的方式

- 根元素(html)
- 浮动元素（元素的 float 不是 none）
- 绝对定位元素（元素的 position 为 absolute 或 fixed）
- 行内块元素（元素的 display 为 inline-block）
- overflow 值不为 visible 的块元素
- 弹性元素（display 为 flex 或 inline-flex 元素的直接子元素）

不常见的方式

- display 值为 flow-root 的元素
- 表格单元格（元素的 display 为 table-cell，HTML 表格单元格默认为该值）
- 表格标题（元素的 display 为 table-caption，HTML 表格标题默认为该值）
- 匿名表格单元格元素（元素的 display 为 table、table-row、 table-row-group、table-header-group、table-footer-group（分别是 HTML table、row、tbody、thead、tfoot 的默认属性）或 inline-table）
- contain 值为 layout、content 或 paint 的元素
- 网格元素（display 为 grid 或 inline-grid 元素的直接子元素）
- 多列容器（元素的 column-count 或 column-width 不为 auto，包括 column-count 为 1）
- column-span 为 all 的元素始终会创建一个新的 BFC，即使该元素没有包裹在一个多列容器中

### BFC的应用

#### 阻止相邻元素的 margin 合并

在常规的流布局中，两盒子的距离由外边距 margin 决定，一般遵循以下规则：

- 两个相邻的外边距都是正数的时候，外边距取二者较大的；
- 两个相邻的外边距都是负数的时候，外边距取二者绝对值较大的；
- 两个相邻的外边距一正一负的时候，取二值之和。

而如果两个盒子在不同的 BFC 中他们的外边距就不会折叠

### 参考资料

- [MDN:块格式化上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)
- [W3C:块格式化上下文](https://www.w3.org/TR/CSS2/visuren.html#block-formatting)