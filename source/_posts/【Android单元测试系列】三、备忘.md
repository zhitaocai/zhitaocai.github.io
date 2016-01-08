title: 【Android单元测试系列】三、备忘
tags:
  - Android
categories:
  - Android开发
  - Android测试

description: 【Android单元测试系列】三、备忘
date: 2016-01-07 11:18:12

---
Android SDK 测试类是基于Junit3来进行扩展的，因此我们有必要了解Junit3，本文将详细介绍Junit3的相关知识。
<!--more-->

## Android 主流单元测试框架介绍

### Junit 

| 面对类、对象、函数等等，在测试工程中创建一个对象出来，然后执行测试用例对该函数进行测试，但是在Android中，面对的还有组件，控件，生命周期，异步任务，消息传递等，虽然本质是SDK主动执行了一些实例的函数，但创建一个Activity并不能让它执行到resume的状态，因此需要JUnit之外的框架支持。

### AndroidTest

默认创建的Android工程就是采用AndroidTest框架的，需要运行在Android设备上

### Robolectric

* 可以直接运行在JVM上，无须准备Android环境，速度更快
* 可以由Jenkins周期性执行

因此，Roboletric十分适用于Android的测试驱动开发


### 参考资料

* [官方文档](http://robolectric.org/)
* [介绍使用入门](http://segmentfault.com/a/1190000002904944)



### Shadow

| Robolectric的本质是在Java运行环境下，采用Shadow的方式对Android中的组件进行模拟测试，从而实现Android单元测试。对于一些Robolectirc暂不支持的组件，可以采用自定义Shadow的方式扩展Robolectric的功能。

### Mock框架

| 如果要测试的目标对象依赖关系较多，需要解除依赖关系，以免测试用例过于复杂，用Robolectric的Shadow是个办法，但是推荐更加简单的Mock框架，比如Mockito，该框架可以模拟出对象来，而且本身提供了一些验证函数执行的功能。


```seq
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
```

