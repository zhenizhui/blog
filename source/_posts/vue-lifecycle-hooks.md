---
title: Vue的生命周期
tags: [Vue]
categories: 前端
date: 2019-04-11 19:53:19
---

今天抽了空看了一下 Vue 的生命周期。

<!-- more -->

### Vue 渲染元素的过程

Vue 中要渲染一块页面内容的时候，会有这么几个过程：

1. 把`<template>`模板解析语法生成 AST 语法树
2. 根据 AST 结果，完成 data 数据初始化。
3. 根据 AST 结果和 data 数据绑定情况，生成虚拟 DOM。
4. 将虚拟 DOM 生成真正的 DOM 插入到页面中，此时页面会被渲染。

当我们绑定的数据进行更新的时候，又会产生以下这些过程：

5. 框架接收到数据变更的事件，根据数据生成新的虚拟 DOM 树。比较新旧两棵虚拟 DOM 树，得到差异。
6. 把差异应用到真正的 DOM 树上，即根据差异来更新页面内容。

**注意**，如果 data 里面的数据没有在模板中使用，即使更新了数据，也不会触发`updated`的钩子函数

当我们移除页面的元素的时候，还有：

7. 注销 Vue 实例，清空页面内容，移除绑定事件、监听器等，如果这时候有使用`addEventListener`来绑定事件的，也要手动一并移除。

### 代码示例

```html
<div id="app"></div>
```

```js
new Vue({
  el: '#app',
  template: '<div>{{ message }}</div>',
  data() {
    return {
      message: 'hello, vue',
    }
  },
  methods: {
    test() {},
  },
  beforeCreate: function () {
    // 在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用
    console.log('beforeCreate', this.message, this.test, this.$el)
  },
  created: function () {
    // 在实例创建完成后被立即调用
    // 在这一步，实例已完成以下的配置：数据观测 (data observer)，属性和方法的运算，watch/event 事件回调
    // 挂载阶段还没开始，$el 属性目前不可见
    console.log('created', this.message, this.test, this.$el)
  },
  beforeMount: function () {
    // 在挂载开始之前被调用：相关的 render 函数首次被调用
    console.log('beforeMount', this.message, this.test, this.$el)
  },
  mounted: function () {
    // el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子
    // 如果 root 实例挂载了一个文档内元素，当 mounted 被调用时 vm.$el 也在文档内
    // mounted 不会承诺所有的子组件也都一起被挂载
    // 如果希望等到整个视图都渲染完毕，可以用 vm.$nextTick 替换掉 mounted
    console.log('mounted', this.message, this.test, this.$el)
    this.$nextTick(function () {
      // 此处整个视图已渲染完毕
    })
  },
  beforeUpdate: function () {
    // 数据更新时调用，发生在生成虚拟 DOM 之前
    // 这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器
  },
  updated: function () {
    // 由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子
    // 当这个钩子被调用时，组件 DOM 已经更新，因此可以做一些操作DOM的事情
  },
  beforeDestroy: function () {
    // 实例销毁之前调用。
  },
  destroyed: function () {
    // Vue 实例销毁后调用
    // 调用后，Vue 实例自己的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁
  },
})
```

### 总结

| 生命周期钩子  | 说明                                                                |
| :------------ | :------------------------------------------------------------------ |
| beforeCreate  | 初始化实例前，data、methods 等选项无法访问                          |
| created       | 实例初始化完成，此时可访问 data 里数据和 methods 事件，无法访问 DOM |
| beforeMount   | 虚拟 DOM 创建完成，此时未挂载到页面中，vm.\$el 可访问未挂载模板     |
| mounted       | 数据绑定完成，真实 DOM 已挂载到页面，vm.\$el 可访问真实 DOM         |
| beforeUpdate  | 数据更新，DOM Diff 得到差异，未更新到页面                           |
| updated       | 数据更新，页面也已更新                                              |
| beforeDestroy | 实例销毁前                                                          |
| destroyed     | 实例销毁完成                                                        |
