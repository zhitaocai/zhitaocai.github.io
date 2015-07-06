title: 【Android单元测试系列】二、Junit3使用
tags:
  - Android
  - Junit
categories:
  - Android开发
  - Android测试

description: Junit3使用
date: 2015-07-06 19:49:51

---
本文将详细介绍Junit3的相关知识，建议阅读之前先阅读[【Android单元测试系列】一、单元测试&&Junit](/【Android单元测试系列】一、单元测试&&Junit.html)。

<!--more-->

# 如何在AndroidStudio上搭建Junit测试环境
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


# 编写一个简单的Junit3测试用例

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

# 继续编写详细Junit3测试类

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

