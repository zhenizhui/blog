---
title: Promise细节问题
date: 2019-04-7 23:00:33
tag: [异步编程, 前端]
categories: 技术
---

重新梳理一下 Promise 的细节问题，巩固基础

<!-- more -->

#### 1. 在 Promise 中 return Error 和 throw Error 的区别

```js
Promise.resolve()
  .then(
    value => {
      throw new Error('error')
    },
    reason => {
      console.error('fail1:', reason)
    }
  )
  .catch(reason => {
    console.error('fail2:', reason)
  })
```

<details>
<summary>答案</summary>

.then 可以接收两个参数：[.then(onResolved, onRejected)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/then#%E5%8F%82%E6%95%B0)

.catch 是.then 的语法糖：.then(onRejected)相当于.then(null, onRejected)

**return 一个 Error 不会被 catch 捕获到**

**但是 throw 一个 Error 会被 catch 到**

所以输出结果是：

```js
fail2: Error: error at ....
```

</details>

#### 2. 执行顺序的问题

```js
new Promise(resolve => {
  // p1
  resolve(1)
  // p2
  Promise.resolve().then(() => {
    console.log(2) // t1
  })
  console.log(4)
}).then(t => {
  console.log(t) // t2
})
console.log(3)
```

<details>
<summary>答案</summary>

解析：

1. 在构造函数内部，执行到 p1 的 resolve(1)，这时候 p1 的状态更改为 resolve
2. 遇到 p2 的 resolve()，这时候 p2 的状态更改为 resolve，然后遇到了 p2 的 then 函数，推入到微任务的队列
3. new Promise(fn)，fn 立即执行，所以先输出 4；
4. 这时候执行到了 p1 的 then 函数，推入到微任务的队列
5. 完成执行 console.log(3)后，宏任务结束，然后微任务队列中的任务 t1、t2 依次执行，所以输出 3、2、1；

所以输出结果是：

```js
// 4 3 2 1
```

</details>

#### 3. Promise 的.finally()

```js
Promise.resolve('1')
  .then(res => {
    console.log(res)
  })
  .finally(() => {
    console.log('finally 1')
  })
Promise.resolve('2')
  .finally(() => {
    console.log('finally 2')
    return '我是finally2返回的值'
  })
  .then(res => {
    console.log('p2 then', res)
  })
```

<details>
<summary>答案</summary>

```js
1
finally 2
finally 1
p2 then 2
```

</details>

#### 4. Promise 结合 setTimeout，判断输出内容

```js
console.log('start')
setTimeout(() => {
  console.log('time')
})
Promise.resolve().then(() => {
  console.log('resolve')
})
console.log('end')
```

<details>
<summary>答案</summary>

这种其实还是很好得出正确答案的，要明确宏任务和微任务执行时机即可

1. 刚开始整个脚本作为一个宏任务来执行，对于同步代码直接压入执行栈进行执行，因此先打印出 start 和 end。
2. setTimout 作为一个宏任务被放入宏任务队列(下一个)
3. Promise.then 作为一个微任务被放入微任务队列
4. 本次宏任务执行完，检查微任务，发现 Promise.then，执行它
5. 接下来进入下一个宏任务，发现 setTimeout，执行。

```js
'start'
'end'
'resolve'
'time'
```

</details>
