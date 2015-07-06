title: Android单元测试详细介绍
tags:
  - Android
  - Junit
categories:
  - Android开发
  - Android测试

description: Android单元测试详细介绍
date: 2015-07-06 19:49:51

---
本门主要从`什么是Junit`->`Junit4大功能`->`为什么需要单元测`->`什么时候写单元测试`->`junit3使用`->`junit4使用`->`Android单元测试使用`方面进行介绍如何在Android上进行测试
<!--more-->

# 什么是 JUnit
JUnit 是采用测试驱动开发的方式，也就是说在开发前先写好测试代码，主要用来说明被测试的代码会被如何使用，错误处理等；然后开始写代码，并在测试代码中逐步测试这些代码，直到最后在测试代码中完全通过。


# JUnit 的 4 大功能
1. **管理测试用例**：修改了哪些代码，这些代码的修改会对哪些部分有影响，通过 JUnit 将这次的修改做个完整测试。这也就 JUnit 中所谓的 TestSuite。
2. **定义测试代码**：这也就是 JUnit 中所谓的 TestCase，根据源代码的测试需要定义每个TestCase，并将 TestCase 添加到相应的 TestSuite 方便管理。
3. **定义测试环境**：在 TestCase 测试前会先调用“环境”配置，在测试中使用，当然也可以在测试用例中直接定义测试环境。
4. **检测测试结果**：对于每种正常、异常情况下的测试，运行结果是什么、结果是否是我们预期的等都需要有个明确的定义，JUnit 在这方面提供了强大的功能。

# 为什么需要单元测试
1. Android应用程序运行在各种设备上，有限的内存、CPU功率、电源、网络连接、系统版本号等等，都需要一个合理的测试覆盖率来帮助您增强和维护Android应用程序。
2. 目前移动开发app生命周期短，迭代速度快，如果缺乏一个系统的测试，稍有不慎可能会因为新增了新功能而导致低版本现有的功能出现bug等等。
3. **对于程序员来说：学会编写单元测试对代码设计有十分大的帮助**
4. ...

**等等，为什么上面说的是JUnit，而这里又说Android呢？那是因为Android的测试类是基于Junit3来进行扩展编写的，因此我们有必要了解Junit3 、Junit4的相关知识。**


# 什么时候写单元测试
> JUnit 是采用测试驱动开发的方式，也就是说在开发前先写好测试代码，主要用来说明被测试的代码会被如何使用，错误处理等；然后开始写代码，并在测试代码中逐步测试这些代码，直到最后在测试代码中完全通过。

> 看了是否感觉有些不符合程序员的思维习惯（先写代码然后在调试），的确这也是JUnit 是对程序员思维习惯的“颠覆”。在这里我自己也感觉，好像很 难做到，为什么？在一匹“马”没有完全设计好前，怎么规定这匹“马”将来会如何跑？而且即使把“马”将来会如何“跑”定义好了，在实现的时候“马”被改变 了怎么办？

> 说到这里，我就说明下，我自己对 JUnit “错误”的使用方法，这也许与 JUnit 测试驱动开发的目的相矛盾，但是的确可以有效地减少 bug。JUnit 从核心来说就是将源代码与测试代码完全分开，将测试代码作为一个单独的程序。前面介绍的方法，都将源代码与 测试代码合为一体，由于源代码的重要性大于测试代码的重要性，所以测试代码经常有不完整、结构不清晰等问题，这样程序员的单元测试也就不完整。JUnit 就是被我用来做完整的单元测试，对当前的部分代码，测试其在每种“环境”下的运行结果。

总结下来：**单元测试需要在开发前写好测试代码**，听起来已经够怪异了，但是经过实际使用后，发现确实需要做到这点才能真正的用好测试，另外估计你已经从这句话里面猜到究竟是谁写单元测试了，没错，就是**写代码的你**~~

# Junit3

## Junit 运作模式

**TestCase** -> **TestSuite** -> **TestRunner** ==> **TestResult**
1. 我们需要为每个类写测试用例(**TestCase**)，比如：各种食物的保质期测试用例
2. 然后通过不同的容器（**TestSuite**）将这些类分别组织起来，如：蔬菜一个容器，鲜肉一个容器
3. 不同的容器运行在不同的环境中(**TestRunner**，如：参数化测试``Parameterized.class``，套件测试``Suite.class``)，然后**TestRunner**就会将容器中的所有测试用例自动执行
4. 最后得出结果

## 如何在AndroidStudio上搭建Junit测试环境
在进行Junit3的运用之前，我们需要先搭建一下测试环境

1. 在项目的`src`目录下依次新建文件夹`test`->`java`->`你的包名路径`，如下图（我这里的包名为`com.czt.saisam.unittest`）
 ![](/img/Android单元测试详细介绍/0.png)
2. 打开`Build Variants`窗口，修改为单元测试`Unit Tests`
 默认快捷键 `Ctrl + E`  
 ![](/img/Android单元测试详细介绍/1.png)
 ![](/img/Android单元测试详细介绍/2.png)
 到这里，你可以看到你的目录结构已经发生变化了  
 ![](/img/Android单元测试详细介绍/3.png)
   
3. 如果您还不了解或者对上面步骤产生疑问，可以详细参考[这里](http://ask.android-studio.org/?/article/44)


## 编写一个简单的Junit3测试用例

1. 待测试java源代码
```java
    package com.czt.saisam.unittest.util;
    
    /**
     * @author zhitao
     * @since 2015-07-06 21:30
     */
    public class MathUtil {
    
        public static int add(int a, int b) {
            return a + b;
        }
    
        public static int sub(int a, int b) {
            return a - b;
        }
    }
```

2. 测试代码
```java
    package com.czt.saisam.unittest.util;
    
    import junit.framework.TestCase;
    
    /**
     * @author zhitao
     * @since 2015-07-06 21:31
     */
    public class MathUtilJunit3TestCase extends TestCase {
    
        public void test_add() throws Exception {
            assertEquals(2, MathUtil.add(1, 1));
        }
    }  
```

3. 解析

  1. 测试类存放&命名规则
    *  在Junit3，为了更好的管理项目，建议将测试类放在与源代码同包名下的目录下  
    ![](/img/Android单元测试详细介绍/4.png)
    *  命名的话建议`原类名 + TestCase`之类的，我这里起`MathUtilJunit3testCase`是因为后面会讲到Junit4，为了区分就这样起了。
  2. Junit3中规定具体运行的测试方法必须要以`test`开头，后面的自己另起。于是就有了测试方法`test_add()`*(为了清晰,我个人习惯用下划线命名规则来命名测试方法)*
  3. 测试的时候遇到异常可以通过在方法后面`throws Exception`处理，尽量都抛出
  4. 测试的方法有了，那么我们根据什么来判断这个测试方法是否通过呢，没错，就是通过`assertEquals(期望值，实际值)`方法来进行断言，由于我们知道1+1肯定等于2，于是我们就断言如果方法`MathUtil.add(1, 1)`的结果肯定为2，于是就有了`assertEquals(2, MathUtil.add(1, 1));`

4. 运行
右击方法就方法选择``Run``->``test_add``
![](/img/Android单元测试详细介绍/5.png)

5. 运行结果，因为实际值与期望值相同所以就通过了
![](/img/Android单元测试详细介绍/6.png)

## 继续编写详细Junit3测试类

```java
    package com.czt.saisam.unittest.util;
    
    import junit.framework.TestCase;
    
    /**
     * @author zhitao
     * @since 2015-07-06 21:31
     */
    public class MathUtilJunit3TestCase extends TestCase {
    
        private int a;
    
        private int b;
    
        @Override
        protected void setUp() throws Exception {
            super.setUp();
            a = 5;
            b = 2;
        }
    
        @Override
        protected void tearDown() throws Exception {
            super.tearDown();
        }
    
        /**
         * 测试加法——同时测试是否正确与失败
         *
         * @throws Exception
         */
        public void test_add() throws Exception {
            assertSame(2, MathUtil.add(1, 1));
            assertNotSame(1, MathUtil.add(1, 1));
        }
    
    
        /**
         * 测试减法——应该失败
         *
         * @throws Exception
         */
        public void test_sub_ShouldBeRight() throws Exception {
            /**
             * 这里的a，b的值会在{@link #setUp()} 方法中进行初始化
             */
            assertEquals(3, MathUtil.sub(a, b));
            assertEquals(2, MathUtil.sub(a, b));
        }
    
    }
```

1. 解析

  1. 可以在同一个测试方法中断言多次，比如`test_add`方法中我就测试了正确的和错误的参数
  2. 这里重载了两个方法`setUp()`、`tearDown()`
    1. `setUp()`:在每个测试方法之前调用，一般用于初始化
    2. `tearDown()`:在每个测试方法执行完毕之后调用，一般用于资源回收
    3. 比如`test_sub_ShouldBeRight()`的执行，会按照这样的顺序
        1. `setUp`->
        2. `testtest_sub_ShouldBeRight`->
        3. `tearDown`->
        4. 测试下一个方法
        5. `setUp`->
        6. `test_xxx`->
        7. `tearDown`->...
    
2. 右击类，`Run`->`MathUtilJunit3TestCase`进行测试，结果如下
![](/img/Android单元测试详细介绍/7.png)
可以发现这次测试不通过了，而且输出窗口也有详细写明错误地方，期望值，实际值，错误的位置等，一目了然 

## 编写TestSuite

```java

/**
 * @author zhitao
 * @since 2015-07-06 22:31
 */
public class Junit3TestSuite extends TestSuite {

    public static Test suite() {
        TestSuite suite = new TestSuite(Junit3TestSuite.class);
        
        // 将测试的类加入进来
        suite.addTestSuite(MathUtilJunit3TestCase.class);
        return suite;
    }
}

```


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