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

# 注意事项

## 非public类的单元测试

比如一个包名负责一个业务逻辑，假设业务逻辑为请求一个在线参数，该包下有5个类，一个对外使用的公开类，以及4个不``public``的协作工具类，比如网络请求，解密等一些工具类，在这个例子下，如何进行单元测试，验证网络请求的写法是否正确，或者解密的工具类是否正确等等?

1. 反射？反射的话，势必要写一些字符串来调用对应的**类或者方法**，但是如果进行重构，改动了方法名或者类名，那么编译还是正常的，并不会在重构的时候，直接改动到这个单元测试类中的这个类名或者方法名，因为是字符串
2. 直接将这些类重新修饰为``public ``? 破坏了结构
3. 

## 测试方法不能设置返回值，必须设置设置为``void``类型


## 举例子可以用保存数据到文件中这个来举：为什么需要单元测试，因为能快速帮助我们进行边界测试

## AndroidJunitTest 测试不通过的时候，错误的描述详细程度比起Junit4差很多


# 参考资料

* http://www.vogella.com/tutorials/AndroidTesting/article.html 
* http://blog.bihe0832.com/Instrumentation_Android_test_3.html
