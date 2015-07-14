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

# Junit3
Android SDK 中的单元测试代码是基于Junit3扩展的，因此我们有必要学些Junit3的相关内容

# Junit4
Junit4 相对于 Junit3 有了很大的改进，并且采用了自Java 1.5后十分流行的注解技术，从此单元测试编写变得更加简单，而Android单元测试框架中就可以采用Junit4的注解技术了

# Android SDK 自带测试类使用
Android SDK 自带的测试类是我们重点介绍的内容，基本可以实现Android各种情况下的单元测试

# Robolectric 单元测试框架使用
Android 自带的测试，是需要连接模拟器或者真机才能进行测试，而Robolectric最大的特性是摆脱模拟器，直接在开发应用的过程中测试，可以简单理解为在java虚拟机上载入AndroidSdk从而实现脱机测试，极大地提高效率。
