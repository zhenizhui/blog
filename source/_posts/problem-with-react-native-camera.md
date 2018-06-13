---
title: react-native-camera扫码的局限性
date: 2018-06-12 12:30:33
tag: [react-native]
categories: 技术
---

这篇文章主要讲讲我使用react-native-camera实现扫描二维码过程中发现的一个问题。

<!--more-->

### 开源社区的二维码组件

react-native-camera是rn官方的相机组件，提供了扫码的功能，但奇怪的是，这个扫二维码的功能却连个框都没有，这就有点尴尬了😅。所以，社区出现了其他的二维码组件，例如，

+ [AC-QRCode-RN](https://github.com/MarnoDev/AC-QRCode-RN)，这个组件设计的十分美观。

+ [react-native-qr-scanner](https://github.com/shifeng1993/react-native-qr-scanner)，虽然界面设计比不上前面的那个，但是这个组件却实现了官方组件没有的功能，就是只扫描框内的二维码。

以上组件大多数仅仅是做了一些样式的配置，底层都是依赖于react-native-camerm。

要是写代码一帆风顺那该多好啊。。。下面问题来了。

### 问题

我在使用[react-native-qr-scanner](https://github.com/shifeng1993/react-native-qr-scanner)发现了这么一个问题。当屏幕范围内有多个二维码的时候，会无法识别二维码，很长时间都没有解析出二维码的内容。

我在确认代码使用正确的情况下，给作者[@shifeng1993](https://github.com/shifeng1993)写了一封邮件咨询这个问题。

原来是这样的，如果屏幕范围内只有一个二维码，那么这个库能完美解决问题。但是，存在多个二维码的时候，就有问题了。

react-native-camera识别二维码的顺序是先识别第一个二维码，在识别第二个二维码，再限制了区域，所以你对准第二个二维码在框内的时候，实际react-native-camera得到是第一个二维码的信息，又因为你第一个二维码不在区域内。所以相应的解析函数没有被调用。有兴趣的同学可以查看调用解析二维码函数的[部分代码](https://github.com/shifeng1993/react-native-qr-scanner/blob/master/src/QRScanner.js#L144)

### 一个暂时可行的解决方案

```jsx
<View style={{ width: 200, height: 200, marginTop: 100, marginLeft: 100}}>
  <Camera />
</View>
```

通过包多一层View，并对这个View做偏移和大小的限制。这样相机组件就不会在整个屏幕范围内识别二维码了，这样，扫码的功能就没问题了。但是，UI样式还要下功夫去调试，毕竟目前这个实现方式和原生的扫码组件有交互上的差别。

在写下这边文章的时候，我也同时给react-native-qr-scanner提了个[issue](https://github.com/shifeng1993/react-native-qr-scanner/issues/8)
