title: 【Android单元测试系列】三、Instrumentation
tags:
  - Android
categories:
  - Android开发
  - Android测试

description: 【Android单元测试系列】三、备忘
date: 2016-01-07 11:18:12

---
 Android自带单元测试框架，怎么样都要了解一下
<!--more-->

# Instrumnetation

Android 官方sdk本身就带有的AndroidTest框架，默认创建的Android工程就是采用[Instrumentation]http://developer.android.com/intl/zh-cn/reference/android/app/Instrumentation.html 
框架的

* **采用这个框架写的测试用例运行时需要运行在Android设备上**
* 框架基于Junit3，因此有必要先了解Junit3的的语法
* 为Android应用每个组件都提供了测试基类

## 原理

当你运行一个测试程序时，首先会运行一个系统工具叫做Activity Manager。Activity Manager使用Instrumentation框架来启动和控制TestRunner，这个TestRunner反过来又使用Intrumentation来关闭任何主程序的实例，然后启动测试程序及主程序（同一个进程中）。这就能确保测试程序与主程序间的直接交互。

**Instrumentation框架通过将主程序和测试程序运行在同一个进程来实现这些功能**。Instrumentation和Activity有点类似，只不过Activity是需要一个界面的，而Instrumentation不需要，我们可以将它理解为一种没有图形界面的，具有启动能力的，用于监控其他类的工具类。

## Instrumentation TestCase类






## Instrumentation TestRunner

| Android提供了自定义的运行测试用例的类，叫做InstrumentationTestRunner。这个类控制应用程序处于测试环境中，在同一个进程中运行测试程序和主程序，并且将测试结果输出到合适的地方。IntrumentationTestRunner在运行时对整个测试环境的控制能力的关键是使用Instrumentation。而且即使你的测试类不使用Instrumentation，你也可以使用这个TestRunner。

**也就是说说你可以将测试进度和结果通过runner，反馈主程序中，然后可以界面动态更新结果和进度**

## 入门使用

1. 在 ``AndroidManifest.xml`` 中的 ``manifest`` 标签中添加 **instrumentation** 描述：指定要测试的应用程序

```xml
    <!-- targetPackage：指定要测试的应用程序包名 -->
    <instrumentation
        android:name="android.test.InstrumentationTestRunner"
        android:targetPackage="com.android.example" >
    </instrumentation>
```

2. 在 ``AndroidManifest.xml`` 中的 ``application`` 标签中添加使用的 **library** 描述

```xml
    <uses-library android:name="android.test.runner">
```



# 参考资料

* http://www.vogella.com/tutorials/AndroidTesting/article.html 
* http://blog.bihe0832.com/Instrumentation_Android_test_3.html
