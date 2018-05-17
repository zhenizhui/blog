---
title: 纪录react native开发过程遇到的一个坑
date: 2018-05-17 23:30:33
tag: [react-native]
categories: 技术
---

3月份到现在，接手了一个react-native的项目。

第一次接触移动端开发，感受挺深，虽然react-native的技术栈是javascript，但是这和以往在浏览器里面开发的体验是很不一样的，
如果用一句话来总结的话，我想表达的是，`一个纯写js的人，是根本不能很好的用react-native完成一个中大型的项目的`，这个以后再展开来讲，今天要说的是一个坑。

本来一直好好的项目，从昨天开始就出现问题了————运行`react-native run-android` 报错了。具体报错如下：

```
:react-native-audio:compileReleaseJavaWithJavac - is not incremental (e.g. outputs have changed, no previous execution, etc.).
D:\ZZH-WorkingSpace\deviceApp\node_modules\react-native-audio\android\src\main\java\com\rnim\rn\audio\ReactNativeAudioPackage.java:35: 错误: 方法不会覆盖或实现超类型的方法
    @Override
    ^
1 个错误
:react-native-audio:compileReleaseJavaWithJavac FAILED

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':react-native-audio:compileReleaseJavaWithJavac'.
> Compilation failed; see the compiler error output for details.

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output.

BUILD FAILED

Total time: 1 mins 0.629 secs
```

我打开对应目录的java代码，有点懵逼。:(
```java
/**
     * @return list of JS modules to register with the newly created catalyst instance.
     * <p/>
     * IMPORTANT: Note that only modules that needs to be accessible from the native code should be
     * listed here. Also listing a native module here doesn't imply that the JS implementation of it
     * will be automatically included in the JS bundle.
     */
    @Override
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();
    }
```

我当时第一反应是去react-native-audio上搜索issue。关键词：Override

很顺利，我搜到了相关内容，原来是react-native@0.47的breaking changes。它移除了createJSModule.

大家给的办法是注释@@Override，或者干脆删了这个方法。

我想了想，不对啊。我之前用的好好的，怎么突然出现了问题？还要我去改node_modules里面的代码？这是不正确的操作。

我又读了一下README.md。

```
For React Native >= 0.47.2, use v3.4.0 and up. 

For React Native >= 0.40, use v3.1.0 up til 3.2.2. 

For React Native <= 0.39, use v3.0.0 or lower.
```

我又对照了一下package.json，react-native-audio的版本也已经固定为3.2.2。rn的版本，明明是`0.41.2`，。怎么会出现`0.47+`才出现的错误？

```
$ npm list react-native
DeviceApp@0.0.1 D:\ZZH-WorkingSpace\deviceApp
`-- react-native@0.41.2

$ npm list react-native-audio
DeviceApp@0.0.1 D:\ZZH-WorkingSpace\deviceApp
`-- react-native-audio@3.2.2
```

=_= 真是奇怪。我昨天又是Google又是百度，还是解决不了，实在是没办法，开了一个[issue](https://github.com/jsierles/react-native-audio/issues/255)

然后一位热心的外国朋友给出了一个答案

```
@zhenizhui
in /android/build.gradle add

subprojects {
    configurations.all {
        resolutionStrategy {
            force 'com.facebook.react:react-native:0.43.1'
        }
    }
}
Change to whatever RN version you are using
```