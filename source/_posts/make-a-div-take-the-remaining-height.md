---
title: 如何让div自动占满剩下的宽度
date: 2018-12-14 19:30:33
tag: [CSS]
categories: 前端
---

场景：一个div里面有两个div，其中一个定高，需要另外一个div自动占据剩下的空间，怎么做？有多少种方法？

<!-- more -->

### 第一种方法：Flex

```html
<div id="outer">
  <div id="inner">
    固定高度150px
  </div>

  <div id="inner_remaining">
    自动占据剩下的高度
  </div>
</div>
```

```css
#outer {
  display: flex;
  flex-flow: column;
  height: 100%;
}

#inner {
  height: 100px;
  background-color: grey;
}

#inner_remaining {
  background-color: #dddddd;
  flex-grow: 1;
}
```

#### 优点

- 很容易实现
- 这是一个现代化的解决方案

#### 缺点

- 需要浏览器支持 Flex

### 第二种方法：absolute;

这种方法使用绝对定位来让一个 div 在两个高度（一个是 height:100px，一个是 top:100px）之间自动铺满剩下的空间。

```html
<div id="inner">
  固定高度100px
</div>

<div id="inner_remaining">
  自动占据剩下的高度
</div>
```

```css
html,
body {
  height: 100%;
  width: 100%;
  margin: 0;
}

#inner {
  height: 100px;
  background-color: grey;
}

#inner_remaining {
  background-color: #dddddd;
  position: absolute;
  top: 100px;
  bottom: 0;
  width: 100%;
}
```

#### 优点

- 这也是很容易实现
- 效果很直观

#### 缺点

- 维护起来很麻烦（对 height 硬编码）

### 第三种：display: table

By utilizing the property of tables to distribute the given space between the rows and assigning fixed heights to some element, the other elements end up using the remaining height.

因为表格中的行有个特点：如果一个元素在

通过利用表格中行的特点：在行之间分配给定的空间，并将固定的高度分配给某个元素，其他元素最终使用剩余的高度。

```html
<div id="outer">
  <div id="inner">
    固定高度100px
  </div>

  <div id="inner_remaining">
    自动占据剩下的高度
  </div>
</div>
```

```css
html,
body,
#outer {
  height: 100%;
  width: 100%;
  margin: 0;
}

#outer {
  display: table;
}

#inner {
  height: 100px;
  background-color: grey;
  display: table-row;
}

#inner_remaining {
  background-color: #dddddd;
  display: table-row;
}
```

#### 优点

- 简单直接
- 没有硬编码，别的元素可以更改他们的高度

#### 缺点

- 可能对布局有影响

### 第四种方法：CSS3 calc

这个方法利用来新的 CSS3 函数 calc 来计算出一个剩余的高度，这个剩余高度是由总的高度减去其他元素的高度来得到。

```html
<div id="inner">
  固定高度100px
</div>

<div id="inner_remaining">
  自动占据剩下的高度
</div>
```

```css
html,
body {
  height: 100%;
  width: 100%;
  margin: 0;
}

#inner {
  height: 100px;
  background-color: grey;
}

#inner_remaining {
  background-color: #dddddd;
  height: calc(100% - 100px);
}
```

#### 优点

- 很容易实现
- 比其他方法的代码量更少

#### 缺点

- calc 这个 css3 函数是个比较新的实现(老浏览器不支持)
- 维护起来很麻烦（对 height 硬编码）

### 总结

现在我倾向于根据需要使用 flex 和其他上述解决方案。
