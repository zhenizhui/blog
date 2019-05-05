---
title: Vue复杂表单代码优化之道
tags: [Vue]
categories: 前端
date: 2019-05-01 19:13:19
---

随着需求的不断的增加，写的代码越来越多，单个`.vue`文件的行数越来越多，这时候如何清晰的维护代码成为了我当即不得不思考的问题。

<!-- more -->

### 需求场景

在一些中后台应用中，很常见的一些需求是，通过**表单**的一些交互组件，比如下拉框，输入框，单选按钮等来筛选列表。而且，有时候这些交互组件数量甚至多达七八个，在不同的界面，逻辑却是一样的。

怎么办？

一个很直接且常见的思路就是，封装成**组件**。但是这篇文章不打算分享这种常见的 Vue 组件代码封装，我们看看另外一种。

我们先假设这样的一个场景，我们要通过表单的学生编号来筛选学生列表（为了不让篇幅太冗余，我们假设这里的交互组件就只有一个学生编号的输入框），其中，

- 学生编号有位数限制，只能是 8 位数阿拉伯数字。

这里我们使用`Element-ui`作为基础框架。

### 核心：高阶函数

我们首先介绍一下本次业务需求的一个高阶函数

> 如果一个函数操作其他函数，即将其他函数作为参数或将函数作为返回值，将其称为**高阶函数**。**高阶组件**让代码更具有复用性、逻辑性与抽象特征。

写这个函数的目的是将 `Element-ui` 的 `FormItem` 组件和我们的业务组件揉在一块，方便我们在 `Element-ui` 的 `Form` 表单使用

```js
// FormItemHoc/index.js 的实现

import { FormItem } from "element-ui"

// 拦截分发props 到对应组件
export default function (WrappedComponent) {
  return {
    inheritAttrs: false,
    props: {
      // 这里接受的props是传给Element-ui的FormItem组件的
      hocProps: {
        type: Object,
        default: () => ({}),
      },
      // 这里接受的props是传给被包裹组件的
      // 比如el-input, el-select, el-button等需要与业务相结合的特定组件
      selfProps: {
        type: Object,
        default: () => ({}),
      },
    },
    render(h) {
      return h(
        FormItem,
        {
          props: this.hocProps,
        },
        [
          h(WrappedComponent, {
            on: this.$listeners,
            props: this.selfProps,
            // 把父组件的$attrs和selfProps一并往下传递
            attrs: { ...this.$attrs, ...this.selfProps },
          }),
        ]
      )
    },
  }
}
```

### 业务代码的封装

接下来是我们的输入框组件的封装

使用`v-bind`绑定父组件`$attrs`的，使用 v-on 绑定父组件的所有事件监听器`$listeners`。

代码里还提供了对内置事件的处理。比如说当输入框获取到焦点的时候，还想处理一些别的逻辑。同时也支持`$emit`传递数据

```html
<!-- @/components/studentInput/index.vue -->
<template>
  <el-input
    v-bind="$attrs"
    clearable
    v-on="$listeners"
    @focus="customerFocus"
  />
</template>

<script>
  export default {
    methods: {
      customerFocus() {
        console.log("自定义业务逻辑")
        this.$emit("customerFocus", {
          focus: true,
        })
      },
    },
  }
</script>

<style></style>
```

然后我们把 Hoc 组件和输入框组件结合在一起并导出。

```js
// @/components/studentInput/formItem.js

import StudentInput from "./index.vue"
import FormItemHoc from "../FormItemHoc/index"

export default FormItemHoc(StudentInput)
```

然后就是我们的一个校验输入框的业务，学号不能超过 8 位，这里的逻辑涉及到 `Element-ui` `Form` 表单的[校验](https://element.eleme.cn/#/zh-CN/component/form#zi-ding-yi-xiao-yan-gui-ze)。

```js
// @/components/studentInput/StudentNumberValidate.js

// 非法学号表单提示信息语
export const INVALID_STUDENT_NUM_MSG = "请输入8位数的学号"

// 检查学号输入的情况 必须是8位纯数字
export function checkStudentNumberInput(rule, value, callback) {
  if (value !== "") {
    check(value, callback)
  } else {
    callback(new Error(INVALID_STUDENT_NUM_MSG))
  }
}

function check(value, callback) {
  const isAllNumberString = /^[0-9]+$/.test(value)
  const checkLength = value.length !== 8
  if (!isAllNumberString || checkLength) {
    callback(new Error(INVALID_STUDENT_NUM_MSG))
  } else {
    callback()
  }
}
```

接下来就是如何使用。多的不说 🤭，先上代码

```html
// App.vue

<template>
  <el-form ref="form" :model="form" label-width="100px" size="mini" inline>
    <template v-for="(item, index) in formItemList">
      <component
        :is="item.component"
        :key="index"
        v-model="form[item.prop]"
        v-bind="item"
        v-on="item.on"
      />
    </template>
  </el-form>
</template>

<script>
  // 把Hoc包裹好的组件导入进来
  import StudentInput from "@/components/StudentInput/formItem.js"
  import { checkStudentNumberInput } from "@/components/StudentInput/StudentNumberValidate.js"

  export default {
    data() {
      return {
        form: {
          number: "",
        },
        formItemList: [
          {
            prop: "number",
            component: StudentInput,
            selfProps: {
              // 在这里可以补充Element-ui对应组件的props
              placeholder: "请输入学号",
              clearable: true,
            },
            hocProps: {
              prop: "number",
              label: "学号",
              rules: [
                {
                  required: true,
                  validator: checkStudentNumberInput,
                  trigger: "change",
                },
              ],
            },
            // 处理$emit的事件,比如这里负责接收一个自定义的foucus事件
            on: {
              customerFocus: this.handleCustomerFocus,
            },
          },
        ],
      }
    },
    methods: {
      handleCustomerFocus(data) {
        // 处理输入框组件的focus事件
        console.log(data)
      },
    },
  }
</script>

<style></style>
```

我们这里和以往封装组件不同的点在于，我们把组件也放到了 Vue 的 data 函数里面去维护。

`<component>`里面的`v-on="item.on"`负责接受`$emit`的事件

`formItemList`数组可以存储了多个业务组件的结构，这样免去书写了重复而且冗余的 html 模板，我们使用 v-for 循环动态组件来生成我们的业务代码

### 优点

这样写的代码有什么好处？

- 表单里的值是双向绑定的，无需书写额外的事件处理代码设置表单内的值。

- 组件可复用，颗粒度更细。

- 减少了大量重复的 html 模板代码

### 缺点

- 如果`data`函数里面的数据结构太复杂，Vue 初始化数据进行双向绑定的时候消耗的时间会增多
