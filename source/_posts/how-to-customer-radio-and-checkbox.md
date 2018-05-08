---
title: 自定义radio和checkbox
date: 2017-04-23 22:00:33
tag: [前端]
categories: 技术
---

原生的radio控件和checkbox实在是太普通了(~~其实是太丑了~~)，有些时候甚至和页面的其他漂亮的元素格格不入，那么如何对他们进行美化呢？
<!--more-->

先上最终的结果，请看

+ [例子](https://zhenizhui.github.io/customRadioAndCheckbox/)
+ [Github源代码](https://github.com/zhenizhui/customRadioAndCheckbox)

### 原理

当原生radio，checkbox被选中的时候，结合CSS的`相邻兄弟选择器(+)`应用一些动画效果。

+ 对于radio来说，则是缩放
+ 对于checkbox来说，除了缩放，还有旋转

### HTML部分
最终设计出来的html骨架是这样的，相比较而言，多了一些dom结构。

```html

<!-- radio -->

<label for="radio-1" class="radio-wrapper">
  <span class="radio">
    <input type="radio" name="sex" id="radio-1" class="radio-input">
    <span class="radio-inner"></span>
  </span>
  <span class="label-text">男</span>
</label>
```

```html

<!-- checkbox -->

<label for="checkbox-1" class="checkbox-wrapper">
  <span class="checkbox">
    <input type="checkbox" name="evaluate" id="checkbox-1" class="checkbox-input">
    <span class="checkbox-inner"></span>
  </span>
  <span class="label-text">优</span>
</label>
```

### CSS部分

样式部分最关键的是：选中的时候改变元素的状态，

以radio为例，先设置.radio-inner:after的scale属性为0，选中的时候发生改变。

由于.radio-inner:after本身已经有transition的设置。所以这里会有个缓动的效果

```css
.radio-input:checked + .radio-inner:after {
  background-color: #39f;
  transform: scale(1);
}
```
checkbox也是类似的道理

### JavaScrit部分

这里需要js吗？不存在的，这里不需要js。

因为用css就可以实现的东西，为什么还要用js？如果想获取状态的话，那可以给个id，通过

```
let isCheckedRadio = document.querySelector('#radio-1').checked;

// true 表示选中
// false 表示没有选中

```


### 后记

如果要彻底贯彻封装的概念的话，以Vue为例，我们可以将上面的内容封装到一个.vue文件中即可，这里就不详细来介绍细节了，因为都差不多的。