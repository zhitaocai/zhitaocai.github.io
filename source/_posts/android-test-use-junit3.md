title: 【Android单元测试系列】一、Junit3使用
tags:
  - Junit3
categories:
  - Android Test
date: 2015-07-14 22:31:35

---
Android SDK 测试类是基于Junit3来进行扩展的，因此我们有必要了解Junit3，本文将详细介绍Junit3的相关知识。
<!--more-->

# 本文更新日志

## 2015-07-14 
第一版

## 2015-07-15
+ [新增断言方法集合说明](#chap-assert)
+ [新增异步方法测试](#chap-junit-async-test)
+ [新增全局初始化和全局解决方案](#global_setUp_tearDown)
+ 修改上个版本的图片贴错问题

**本文将不再更新，后续有内容将会另起文章进行分享~~**

# 什么是 JUnit
> JUnit 是采用测试驱动开发的方式，也就是说在开发前先写好测试代码，主要用来说明被测试的代码会被如何使用，错误处理等；然后开始写代码，并在测试代码中逐步测试这些代码，直到最后在测试代码中完全通过。

> 看了是否感觉有些不符合程序员的思维习惯（先写代码然后在调试），的确这也是JUnit 是对程序员思维习惯的“颠覆”。在这里我自己也感觉，好像很 难做到，为什么？在一匹“马”没有完全设计好前，怎么规定这匹“马”将来会如何跑？而且即使把“马”将来会如何“跑”定义好了，在实现的时候“马”被改变 了怎么办？

> 说到这里，我就说明下，我自己对 JUnit “错误”的使用方法，这也许与 JUnit 测试驱动开发的目的相矛盾，但是的确可以有效地减少 bug。JUnit 从核心来说就是将源代码与测试代码完全分开，将测试代码作为一个单独的程序。前面介绍的方法，都将源代码与 测试代码合为一体，由于源代码的重要性大于测试代码的重要性，所以测试代码经常有不完整、结构不清晰等问题，这样程序员的单元测试也就不完整。JUnit 就是被我用来做完整的单元测试，对当前的部分代码，测试其在每种“环境”下的运行结果。

总结下来：**JUnit单元测试需要在开发前写好测试代码**，听起来已经够怪异了，但是经过实际使用后，发现确实需要做到这点才能真正的用好测试，另外估计你已经从这句话里面猜到究竟是谁写单元测试了，没错，就是**写代码的你**~~

<!-- # 为什么需要单元测试 -->
<!-- 1. Android应用程序运行在各种设备上，有限的内存、CPU功率、电源、网络连接、系统版本号等等，都需要一个合理的测试覆盖率来帮助您增强和维护Android应用程序。-->
<!-- 2. 目前移动开发app生命周期短，迭代速度快，如果缺乏一个系统的测试，稍有不慎可能会因为新增了新功能而导致低版本现有的功能出现bug等等。-->
<!-- 3. **对于程序员来说：学会编写单元测试对代码设计有十分大的帮助**-->
<!-- 4. ...-->
<!-- **等等，为什么上面说的是JUnit，而这里又说Android呢？那是因为Android的测试类是基于Junit3来进行扩展编写的，因此我们有必要了解Junit3 、Junit4的相关知识。**-->

<a name="Junit运作模式" id="chap-junit-model"></a>
# JUnit 的运作模式 

1. **定义测试代码**：这也就是 JUnit 中所谓的 TestCase，根据源代码的测试需要定义每个TestCase，并将 TestCase 添加到相应的 TestSuite 方便管理。
2. **管理测试用例**：修改了哪些代码，这些代码的修改会对哪些部分有影响，通过 JUnit 将这次的修改做个完整测试。这也就 JUnit 中所谓的 TestSuite。
3. **定义测试环境**：在 TestCase 测试前会先调“环境”（如：参数化测试环境，套件测试环境）配置，在测试中使用，当然也可以在测试用例中直接定义测试环境。
4. **检测测试结果**：对于每种正常、异常情况下的测试，运行结果是什么、结果是否是我们预期的等都需要有个明确的定义，JUnit 在这方面提供了强大的功能。

简单来说就是： **TestCase** -> **TestSuite** -> **TestRunner** ==> **TestResult**


# 如何在AndroidStudio上搭建Junit测试环境
在一切的一切之前，我们需要先搭建一个Junit的运行环境，由于我们是基于Android的单元测试系列，所以我们将介绍如何在Android Studio中搭建Junit运行环境：

1. 在项目的`src`目录下依次新建文件夹`test`->`java`->`你的包名路径`，如下图（我这里的包名为`com.czt.saisam.unittest`）
![](/img/【Android单元测试系列】一、Junit3使用/0.png)
2. 打开`Build Variants`窗口（默认快捷键 `Ctrl + E`），修改为单元测试`Unit Tests`
![](/img/【Android单元测试系列】一、Junit3使用/1.png)![](/img/【Android单元测试系列】一、Junit3使用/2.png)
3. 到这里，你可以看到你的目录结构已经有背景颜色了，表示你可以在这个目录下创建测试类了  
![](/img/【Android单元测试系列】一、Junit3使用/3.png)
   
ps：如果您还不了解或者对上面步骤产生疑问，可以详细参考[这里](http://ask.android-studio.org/?/article/44)


# TestCase

## 编写一个简单的Junit3测试用例

1. 在项目源码路径（``src/main/java/com/czt/saisam/unittest/util``）中创建一个简单的待测试类
```java
    public class MathUtil {
    
        public static int add(int a, int b) {
            return a + b;
        }
    }
```

2. 为了直观，我们一般在项目的Junit测试代码目录中，在对应于待测试类的位置中创建单元测试类（如：``src/main/java/com/czt/saisam/unittest/util``对应于``src/test/java/com/czt/saisam/unittest/util``）
```java
    public class MathUtilJunit3TestCase extends TestCase {
    
        public void test_add(){
            assertEquals(2, MathUtil.add(1, 1));
        }
        
    }  
```

3. 分析

  1. 在Junit3，为了更好的管理项目，建议将测试类放在与源代码同包名下的目录下  
    ![](/img/Android单元测试详细介绍/4.png)
  2. 命名的话建议`原类名 + TestCase`之类的，我这里起`MathUtilJunit3testCase`是因为后面会讲到Junit4，为了区分就这样起了。
  3. Junit3中规定具体运行的测试方法必须要以`test`开头，后面的自己另起。于是就有了测试方法`test_add()`*(为了清晰,我个人习惯用下划线命名规则来命名测试方法)*
  4. 测试的方法有了，那么我们根据什么来判断这个测试方法是否通过呢，没错，就是通过`assertEquals(期望值，实际值)`方法来进行断言，由于我们知道1+1肯定等于2，于是我们就断言如果方法`MathUtil.add(1, 1)`的结果肯定为2，于是就有了`assertEquals(2, MathUtil.add(1, 1));`

4. 运行
右击方法就方法选择``Run``->``test_add``
![](/img/【Android单元测试系列】一、Junit3使用/5.png)

5. 运行结果，因为实际值与期望值相同所以就通过了
![](/img/【Android单元测试系列】一、Junit3使用/6.png)


<a name="assert" id="chap-assert"></a>
## 断言
``TestCase``类是继承自``Assert``，因此默认拥有下面的断言方法，具体也可以参考源码(``junit.framework.Assert``)中支持的断言方法

|断言方法|简单说明|
|--|--|
|``assertTrue(boolean condition)``|断言condiction为true|
|``assertTrue(String message, boolean condition)``|断言condiction为true，如果断言失败，那么就显示message的内容|
|``assertFalse(boolean condition)``|断言condiction为false|
|``assertFalse(String message, boolean condition)``|断言condiction为false，如果断言失败，那么就显示message的内容|
|``fail()``|断言不通过|
|``fail(String message)``|断言不通过，并且显示message的内容|
|``assertEquals(String expected, String actual)``|断言期望字符串expected的内容等于实际值字符串actual的内容|
|``assertEquals(String message, String expected, String actual)``|断言期望字符串expected的内容等于实际值字符串actual的内容，如果断言失败就显示message的内容|
|``assertEquals(...)``|基本支持各大基本数据类型的断言，这里就不一一再列了|
|``assertNotNull(Object object)``|断言object对象不为空|
|``assertNotNull(String message, Object object)``|断言object对象不为空，如果断言失败就显示message的内容|
|``assertNull(Object object)``|断言object对象为空|
|``assertNull(String message, Object object)``|断言object对象为空，如果断言失败就显示message的内容|
|``assertSame(Object expected, Object actual)``|断言期望对象expected等于实际对象actual|
|``assertSame(String message, Object expected, Object actual)``|断言期望对象expected等于实际对象actual，如果断言失败就显示message的内容|
|``assertNotSame(Object expected, Object actual)``|断言期望对象expected不等于实际对象actual|
|``assertNotSame(String message, Object expected, Object actual)``|断言期望对象expected不等于实际对象actual，如果断言失败就显示message的内容|
|``failSame(String message)``|暂时不明|
|``failNotSame(String message, Object expected, Object actual)``|暂时不明|
|``failNotEquals(String message, Object expected, Object actual)``|暂时不明|
|``format(String message, Object expected, Object actual)``|暂时不明|

PS：
如果需要自定义断言方法，可以自己继承``TestCase``类，新起一个如``DiyTestCase``类，在该类中自行定义断言方法，然后后续的测试用例类继承``DiyTestCase``即可采用自己的断言方法了。
But，Junit3提供的断言方法已经足够正常使用了，因此这里就不细讲这块。

<a name="setUp&&tearDown" id="chap-setUp&&tearDown"></a>
## setUp&&tearDown

Junit3中TestCase提供了 ``setUp`` 和 ``tearDown``方法：

* `setUp()`:在每个测试方法之前调用，一般用于初始化
* `tearDown()`:在每个测试方法执行完毕之后调用，一般用于资源回收
* 如果类中存在多个测试方法，那么测试执行顺序为
    1. `setUp`->
    2. `test_测试方法1`->
    3. `tearDown`->
    5. `setUp`->
    6. `test_测试方法2`->
    7. `tearDown`->
    8. ...

```java

    public class MathUtilJunit3TestCase extends TestCase {

        @Override
        protected void setUp() throws Exception {
            super.setUp();
            System.out.println("---Calling setUp---");
        }
    
        @Override
        protected void tearDown() throws Exception {
            super.tearDown();
            System.out.println("---Calling tearDown---");
        }
    
        public void test_add() throws Exception {
            System.out.println("---Calling test_add---");
        }
    
        public void test_sub() throws Exception {
            System.out.println("---Calling test_sub---");
        }
    }

```
右击类运行测试，结果如下：
![](/img/【Android单元测试系列】一、Junit3使用/7.png)


## 异常测试

测试的时候遇到异常可以通过在方法后面`throws Exception`处理，将异常抛出，在最终的测试结果中可以查看到。

那么什么时候需要抛出异常呢？按照普遍惯例是都抛出，下面我举一个场景：

实际开发过程中，我们难免会设计一些方法，这些方法可能会抛出某些指定的异常，需要给上层使用者进行处理，那么在测试的时候，我们可以将这些指定的异常进行捕捉，然后针对其他异常进行捕捉，作为测试结果，测试这些方法是否会有额外的异常出现。

例如下面的例子：

```java

    public class MathUtil {
    
        /**
         * 从一个浮点型字符串中获取小数部分的整数
         * <p/>
         * 实际上如果是一个整数或者其他不是数字的字符串，我们除了抛出无法转型的异常之外，还可能会抛出数组越界的异常
         * 假设是"123"，那么是没有numbers[1]的
         *
         * @param str
         *
         * @return
         *
         * @throws NumberFormatException 交由上层调用者自己处理
         */
        public static int getDecimalFromString(String str) throws NumberFormatException {
            String[] numbers = str.split("\\.");
            return Integer.valueOf(numbers[1]);
        }
    }
```


```java
    public class MathUtilJunit3TestCase extends TestCase {
    
        /**
         * 这里我们捕捉测试方法可能存在的额外异常
         */
        public void test_getDecimalFromString() throws Exception {
            try {
                assertEquals(456, MathUtil.getDecimalFromString("123.456"));
                assertEquals(0, MathUtil.getDecimalFromString("abc"));
                assertEquals(0, MathUtil.getDecimalFromString("123"));
            } 
            
            // 这里我们捕捉这个方法会抛出的异常，因为这些都是设计的时候考虑到的，一定情况下是正常的
            catch (NumberFormatException e){
    
            }
        }
    }  
```

运行测试时就可以发现出现一个上面所说的数据越界的异常
![](/img/【Android单元测试系列】一、Junit3使用/8.png)

**经过这个测试，我们就可以根据提示找到我们的源代码中设计时所忽略的问题了，那么修复就不再是问题了，是不是觉得测试开始有意思呢~~**

## 测试单个TestCase
经过上面的一些说明，我们的``MathUtil``和``MathUtilJunit3TestCase``已经发生了比较多变化了，这里我再贴一下最后的代码样子（@since 2015-07-15）。

```java
    public class MathUtil {
    
        public static int add(int a, int b) {
            return a + b;
        }
    
        public static int sub(int a, int b) {
            return a - b;
        }
    
        /**
         * 从一个浮点型字符串中获取小数部分的整数
         * <p/>
         * 实际上如果是一个整数或者其他不是数字的字符串，我们除了抛出无法转型的异常之外，还可能会抛出数组越界的异常
         * 假设是"123"，那么是没有numbers[1]的
         *
         * @param str
         *
         * @return
         *
         * @throws NumberFormatException
         */
        public static int getDecimalFromString(String str) throws NumberFormatException {
            String[] numbers = str.split("\\.");
            return Integer.valueOf(numbers[1]);
        }
    
    }

```

```java
    public class MathUtilJunit3TestCase extends TestCase {
    
        private int a;
    
        private int b;
    
        @Override
        protected void setUp() throws Exception {
            super.setUp();
            System.out.println("---Calling setUp---");
            a = 5;
            b = 2;
        }
    
        @Override
        protected void tearDown() throws Exception {
            super.tearDown();
            System.out.println("---Calling tearDown---");
        }
    
        /**
         * 测试加法——同时测试是否正确与失败
         *
         * @throws Exception
         */
        public void test_add() throws Exception {
            System.out.println("---Calling test_add---");
    
            assertSame(2, MathUtil.add(1, 1));
            assertNotSame(1, MathUtil.add(1, 1));
        }
    
        /**
         * 测试减法——应该失败
         *
         * @throws Exception
         */
        public void test_sub() throws Exception {
            System.out.println("---Calling test_sub---");
    
            // 这里的a，b的值会在 setUp() 方法中进行初始化
            assertEquals(3, MathUtil.sub(a, b));
            assertEquals(2, MathUtil.sub(a, b));
        }
    
        public void test_getDecimalFromString() throws Exception {
            System.out.println("---Calling test_getDecimalFromString---");
            try {
                assertEquals(456, MathUtil.getDecimalFromString("123.456"));
                assertEquals(0, MathUtil.getDecimalFromString("abc"));
                assertEquals(0, MathUtil.getDecimalFromString("123"));
            } catch (NumberFormatException e) {
    
            }
        }
    
    }

```


这个时候，我们可以右击类，`Run`->`MathUtilJunit3TestCase`进行测试这个类的所有测试方法的测试，结果如下
![](/img/【Android单元测试系列】一、Junit3使用/9.png)

可以发现这次测试有**测试成功的例子(绿色图标)**，也有**测试失败的例子（黄色图标）**，也有出现**测试出现异常的例子（红色图标）**，而且输出窗口也有详细写明错误地方，期望值，实际值，错误的位置等，一目了然。 


<a name="异步方法测试" id="chap-junit-async-test"></a>
## 异步方法测试
实际场景下，我们很多时候会进行一些耗时操作，比如网络请求等，为了处理这些耗时操作，我们可能会根据实际情况设计出**同步**或者**异步**方法。

下面我们举一个比较经典——获取在线参数例子，为了方便，我这里直接采用线程睡眠来模拟耗时操作。

```java

    public class AsyncTask {
    
        public AsyncTask() {
            super();
        }
    
        public String sync_getOnlineConfig(String key) {
            // 采用线程睡眠模拟耗时操作
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            if ("key1".equals(key)) {
                return "value1";
            }
            return null;
        }
    
        public void async_getOnlineConfig(String key, onFinishListener listener) {
            String value = sync_getOnlineConfig(key);
            if (listener != null) {
                listener.onFinish(key, value);
            }
        }
    
        public interface onFinishListener {
    
            void onFinish(String key, String value);
            
        }
    }
```

测试类：

```java

    public class AsyncTaskJunit3TestCase extends TestCase {
    
        public void test_sync_getOnlineConfig() throws Exception {
            assertEquals("value1", new AsyncTask().sync_getOnlineConfig("key1"));
        }
    
        public void test_async_getOnlineConfig() throws Exception {
            final CountDownLatch signal = new CountDownLatch(1);
            new AsyncTask().async_getOnlineConfig("key1", new AsyncTask.onFinishListener() {
    
                @Override
                public void onFinish(String key, String value) {
                    if ("key1".equals(key)) {
                        assertEquals("value1", value);
                    }
                    signal.countDown();
                }
            });
            signal.await();
        }
    }
```

1. 在测试同步耗时方法的时候，可以像以前那样子直接断言结果值是否正确。
2. 在测试异步耗时方法时，因为是异步的，所以该方法不会阻塞后面的代码逻辑，会直接跳过异步方法的回调结果，而我们的断言是在回调函数中进行的，最终结果就是导致没法执行断言。于是，我们需要在进行异步方法的运行时，采用同步加锁机制，来将这个异步操作在一定程度上变为**“同步阻塞”**，待回调结果时，进行解锁并断言结果的正确性。这里我们采用了 ``CountDownLatch``同步辅助类来协作完成这个**“同步阻塞”**。



# TestSuite

## 基本使用
如果你是重头看下来，那么会把焦点都关注在``TestCase``上，然后现在弹出个``TestSuite``，What's this？ Please review [Junit运作模式](#chap-junit-model)

```java

public class Junit3TestSuite extends TestSuite {

    public static Test suite() {
        TestSuite suite = new TestSuite(Junit3TestSuite.class);
        
        // 将测试的类加入进来
        suite.addTestSuite(MathUtilJunit3TestCase.class);
        return suite;
    }
}

```

实际开发过程中，我们大多数会遵循**高类聚低耦合**的设计思想进行开发，那么可能就会出现多个单独的功能模块，每个功能模块的测试可以通过TestSuite来组合该功能模块所用到的测试用例。

那么如果在某个时刻，我们的A功能模块发生变动，仅需要重新跑一遍这个功能模块的TestSuite即可快速检验这次的变动是否通过。

<a name="global_setUp_tearDown" id="global_setUp_tearDown"></a>
## 配置全局初始化和结束

TestSuite可以理解为一个功能单元集合，有时我们希望在测试这个单元功能时，全局初始化一些资源，以及全局回收一些资源（比如：初始化时建立数据库连接，结束时候统一结束连接），那么这个时候，采用TestCase的[``setUp``和``tearDown``](#chap-setUp&&tearDown)明显不够用了

Junit3提供``TestSetup``来帮助我们处理这个事情

```java
    public class Junit3TestSuite extends TestSuite {
    
        public static Test suite() {
            TestSuite suite = new TestSuite(Junit3TestSuite.class);
    
            suite.addTestSuite(MathUtilJunit3TestCase.class);
            suite.addTestSuite(AsyncTaskJunit3TestCase.class);
            suite.addTestSuite(StringUtilJunit3TestCase.class);
    
    
            TestSetup wrapper = new TestSetup(suite) {
                protected void setUp() {
                    System.out.println("---Calling Global_setUp---");
                }
    
                protected void tearDown() {
                    System.out.println("---Calling Global_tearDown---");
                }
            };
            return wrapper;
        }
    }
```

运行结果：
![](/img/【Android单元测试系列】一、Junit3使用/10.png)

PS: 
如果你进行过实际操作的话，就会发现默认的AndroidSDK中没有``TestSetup``类，这是因为Android SDK中仅保留极少部分的Junit3代码（主要是``junit.framework的代码``），其中并没有我们所用到的``TestSetup``类
![](/img/【Android单元测试系列】一、Junit3使用/11.png)

那么TestSetup类又是从哪里来的呢，其实在使用的时候，我已经引用了**Junit4.12**的类库了

```gradle

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:22.1.1'
    
    testCompile 'junit:junit:4.12'
}
```

而这个类就是在Junit4的类库中
![](/img/【Android单元测试系列】一、Junit3使用/12.png)

看到这里你应该明白了一件事了，我们准备讲Junit4了~~

# TestRunner 
默认情况下是不需要自定义TestRunner的，我们将在后面的内容中讲到。

# 参考资料
在写本文的时候，我参考了很多相关的资料，但是在编写过程中，忘记了一些参考链接，或者该参考资料仅在某一点上有参考价值，因此总结下来，下面参考资料在我编写本文的时候提供了极大的帮助：

+  **【强推】**[Android、JUnit深入浅出.pdf](/pdf/Android、JUnit深入浅出.pdf)
+  **【强推】**[Junit源码分析](http://sns.testin.cn/thread-1529-1-1.html)
+  [JUnit3的使用](http://android.blog.51cto.com/268543/49994)
+  [Android Studio上开启Junit测试环境](http://ask.android-studio.org/?/article/44)

# 项目源码地址
本系列的源码我都会统一放在这个项目上[UnitTest](https://github.com/zhitaocai/UnitTest/tree/master)。
