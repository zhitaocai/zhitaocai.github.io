title: 【Android单元测试系列】三、Junit4使用
tags:
  - Android
  - Junit
categories:
  - Android开发
  - Android测试

description: Junit4使用
date: 2015-07-06 19:49:51

---

<!--more-->

# Junit4

到了Junit4，我们看一下前面的Junit3使用过后的一些感想:

1. **测试类需要继承TestCase（java只能单继承，扼杀了这个属性）**
2. **测试类的测试方法需要以**``test``**方法开头，不够美观，不够优雅**
3. **初始化和回收代码在每个测试方法之前调用，一些方法其实可以统一初始化的，而无需每次测试前都初始化一遍，也即不支持类初始化**
4. **如果一个方法的测试，需要模拟很多参数的是偶，需要写很多重复的代码，不够优雅**
5. **要写的东西总感觉有点多**
6. ...

于是，就有了我们的Junit4，完美解决上面的问题，But，Andorid SDK默认是使用Junit3的，因此需要我们引入`Junit4`的类库，才能在单元测试中使用Junit4的api，在`app/build.gradle`中添加下面依赖库：

```gradle
    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        compile 'com.android.support:appcompat-v7:22.1.1'
        
        // 注意是testCompile与androidTestCompile是有区别的，下文会说到
        testCompile 'junit:junit:4.12'
}
```

关于Junit4的更多使用，下面的文章已经描述得十分清晰了，建议大家直接查看原文。同时，我也在[我的项目](https://github.com/zhitaocai/UnitTest)中写了一些例子。

+ [Junit3、4相关使用](http://www.blogjava.net/jnbzwm/archive/2010/12/15/340801.html)
+ [Junit4注解使用](http://blog.csdn.net/wangpeng047/article/details/9628449)
+ [Junit4参数化测试、打包测试、异常测试、限时测试](http://blog.csdn.net/wangpeng047/article/details/9630203)
+ 
# Android单元测试


# 参考资料

1. **【强推】**[Android、JUnit深入浅出.pdf](/pdf/Android、JUnit深入浅出.pdf)
2. **【强推】**[Android应用测试指导（英文版）](http://www.vogella.com/tutorials/AndroidTesting/article.html#androidtesting)
3. [JUnit4用法详解](http://www.blogjava.net/jnbzwm/archive/2010/12/15/340801.html)
4. [Java魔法堂：JUnit4使用详解](http://www.cnblogs.com/fsjohnhuang/p/4061902.html)
5. [Junit使用教程（二）](http://blog.csdn.net/wangpeng047/article/details/9628449)
6. [Junit使用教程（三）](http://blog.csdn.net/wangpeng047/article/details/9630203)
7. [Gradle Unit Test](http://ask.android-studio.org/?/article/44)