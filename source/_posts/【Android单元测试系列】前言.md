title: 【Android单元测试系列】前言
tags:
  - Android
  - Junit
categories:
  - Android开发
  - Android测试

description: 【Android单元测试系列】前言
date: 2015-07-14 22:27:35

---
本系列将从下面几个方面介绍Android单元测试的相关内容，下面是目前了解到的一些内容，可能会随时变动
<!--more-->

# Junit

| 面对类、对象、函数等等，在测试工程中创建一个对象出来，然后执行测试用例对该函数进行测试，但是在Android中，面对的还有组件，控件，生命周期，异步任务，消息传递等，虽然本质是SDK主动执行了一些实例的函数，但创建一个Activity并不能让它执行到resume的状态，因此需要JUnit之外的框架支持。

## Junit3

Android SDK 中的单元测试代码是基于Junit3扩展的，因此我们有必要学些Junit3的相关内容

## Junit4

Junit4 相对于 Junit3 有了很大的改进，并且采用了自Java 1.5后十分流行的注解技术，从此单元测试编写变得更加简单，而Android单元测试框架中就可以采用Junit4的注解技术了

# Android SDK 自带框架 Instrumentation
Android SDK 自带的测试类是我们重点介绍的内容，基本可以实现Android各种情况下的单元测试

# Robolectric
Android 自带的测试，是需要连接模拟器或者真机才能进行测试，而Robolectric最大的特性是摆脱模拟器，直接在开发应用的过程中测试，可以简单理解为在java虚拟机上载入AndroidSdk从而实现脱机测试，极大地提高效率。

* 可以直接运行在JVM上，无须准备Android环境，速度更快
* 可以由Jenkins周期性执行

因此，Roboletric十分适用于Android的测试驱动开发


## 参考资料

* [官方文档](http://robolectric.org/)
* [介绍使用入门](http://segmentfault.com/a/1190000002904944)

## Shadow

| Robolectric的本质是在Java运行环境下，采用Shadow的方式对Android中的组件进行模拟测试，从而实现Android单元测试。对于一些Robolectirc暂不支持的组件，可以采用自定义Shadow的方式扩展Robolectric的功能。

# Mock框架

| 如果要测试的目标对象依赖关系较多，需要解除依赖关系，以免测试用例过于复杂，用Robolectric的Shadow是个办法，但是推荐更加简单的Mock框架，比如Mockito，该框架可以模拟出对象来，而且本身提供了一些验证函数执行的功能。


# Robotium

| Robotium 是一个基于模拟点击时间的用于进行黑河测试的Android测试开源项目：https://github.com/robotiumtech/robotium
| 官网：http://robotium.com/pages/user-guidem
| 完整API：http://robotium.googlecode.com/svn/doc/index.html

