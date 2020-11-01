---
title: 如何在 Markdown 文档显示 diff 效果
tags: [Markdown]
categories: 小技巧
date: 2020-11-01 09:19:39
---

> 今天分享一个 Markdown 的小技巧

<!-- more -->

在写博客的过程中，我们常常想向人们展示一段代码和另一段代码的不同。比如下面类似的例子

![git diff 例子](git-diff-demo.png)

那我们在 Markdown 里面怎么做才有这样的效果呢？只要在反引号的后面加上`diff`就可以实现这种效果了

![如何在 Markdown 文档显示 diff 效果](code.png)

上面的代码运行起来就是下面这个样子啦

```diff
function hello () {
-  return 'hello, world';
+  return 'hello, Tom';
}
```

大家赶紧去试一试吧~
