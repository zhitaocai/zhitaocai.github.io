title: 【Android单元测试系列】二、Junit4使用
tags:
  - Junit4
categories:
  - Android Test
description: getting start with Junit4
date: 2015-07-20 21:25:01
---
在阅读本文之前，强烈建议仔细阅读上文了解Junit3的一些相关内容，那么你更需要了解Junit4。
<!--more-->

# Junit3 -> Junit4
在将Junit4之前，我们需要先回顾一下Junit3的一些特性及一些评价：

1. **测试类需要继承TestCase**，但是java只能单继承
2. **测试类的测试方法需要以**``test``**方法开头，不够美观，不够优雅**
3. **初始化和回收代码在每个测试方法之前调用，一些方法其实可以统一初始化的，而无需每次测试前都初始化一遍，也即不支持类初始化**
4. **如果一个方法的测试，需要模拟很多参数的时候，可能需要写很多重复的代码，不够优雅**
5. **要写的东西总感觉有点多**
6. ...

于是，Junit4出来了，Junit4 主要使用自Java 1.5起支持的注解技术。But，Andorid SDK默认是使用Junit3的，因此，如果需要在``Android Studio``中使用Junit4，那么我们需要引入Junit4的类库才能使用，在`app/build.gradle`中添加下面依赖库：

```gradle
    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        compile 'com.android.support:appcompat-v7:22.1.1'
        
        // 注意是testCompile与androidTestCompile是有区别的，后面会说到
        testCompile 'junit:junit:4.12'
}
```
# @Test
Junit3中我们测试一个方法需要继承`TestCase`并且需要测试的方法都需要固定前缀命名为`test`

e.g

```java
    public class MathUtil {
    
        public static int add(int a, int b) {
            return a + b;
        }
    }
```


```java
    // Junit3 写法
    public class MathUtilJunit3TestCase extends TestCase {
    
        public void test_add(){
            assertEquals(2, MathUtil.add(1, 1));
        }
        
    }  
```

但是同样的测试用例，在Junit4中仅需要用`@Test`注解就可以完成了:

```java
    // Junit4 写法
    public class MathUtilJunit4TestCase {
    
        @Test
        public void add() {
            Assert.assertEquals(2, MathUtil.add(1, 1));
        }
    
    }
```

Junit3 -> Junit4测试方法使用比较:

1. 不用继承`TestCase`
2. 不用固定方法命名前缀一定加`test`，可以实现测试类和源码类，类名方法名完全保存一致，高雅直观
3. 仅需要在被测试的方法前面加上`@Test`注解就可以将该方法标注为测试方法

# @Before @After
Junit3中，我们重写`TestCase`的`setUp`和`tearDown`方法就可以实现每个测试方法测试前后的初始化和回收，Junit4中，仅需要在需要的方法前面加上`@Before`、`@After`即可快速实现相同的功能:

```java
    // Junit3 写法
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
    
        public void test_add() {
            System.out.println("---Calling test_add---");
        }
    
        public void test_sub() {
            System.out.println("---Calling test_sub---");
        }
        
    }
```

```java

    // Junit4 写法
    public class MathUtilJunit4TestCase {
    
        @Before
        public void beforeMethod() {
            System.out.println("---BeforeMethod---");
        }
    
        @After
        public void afterMethod() {
            System.out.println("---AfterMethod---");
        }
    
        @Test
        public void add() {
            System.out.println("---add---");
        }
    
        @Test
        public void sub() {
            System.out.println("---sub---");
        }
        
    }
```

![](/img/【Android单元测试系列】二、Junit4使用/1.png)

# @BeforeClass @AfterClass
Junit4新增了`@BeforeClass`和`@AfterClass`两个注解

+ `@BeforeClass`：在该类的所有测试方法运行之前，执行一次
+ `@AfterClass`:在该类的所有测试方法运行完之后，执行一次

```java
    public class MathUtilJunit4TestCase {
    
        @BeforeClass
        public static void beforeClass() {
            System.out.println("---BeforeClass---");
        }
    
        @AfterClass
        public static void afterClass() {
            System.out.println("---AfterClass---");
        }
    
        @Before
        public void beforeMethod() {
            System.out.println("---BeforeMethod---");
        }
    
        @After
        public void afterMethod() {
            System.out.println("---AfterMethod---");
        }
    
        @Test
        public void add() {
            System.out.println("---add---");
        }
    
        @Test
        public void sub() {
            System.out.println("---sub---");
        }
    
    }
```

![](/img/【Android单元测试系列】二、Junit4使用/2.png)


# @Ignore
Junit4新增了`@Ignore`注解：被`@Ignore`注解修饰的测试方法，不会纳入测试结果中，即便错误，也不会影响到整体测试结果

```java
    public class MathUtilJunit4TestCase {
    
        @Test
        public void add() throws Exception {
            System.out.println("---add---");
        }
    
        @Test
        public void sub() throws Exception {
            System.out.println("---sub---");
        }
    
        @Test
        @Ignore
        public void test() throws Exception {
            Assert.assertEquals(true, false);
        }
    }
```

![](/img/【Android单元测试系列】二、Junit4使用/3.png)


# 异常测试
Junit3 中测试可能会抛出预期异常的方法时，我们需要针对这部分预期的异常进行处理，标识该异常为**"正确的"**：


```java
    // 待测试类
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
         * @throws NumberFormatException
         */
        public static int getDecimalFromString(String str) throws NumberFormatException {
            String[] numbers = str.split("\\.");
            return Integer.valueOf(numbers[1]);
        }
    }

```

```java
    // Junit3 中针对预期异常的写法
    public class MathUtilJunit3TestCase extends TestCase {
    
        public void test_getDecimalFromString() throws Exception {
            try {
                assertEquals(456, MathUtil.getDecimalFromString("123.456d"));
            } catch (NumberFormatException e) {
                assertNotNull(e.getMessage());
            }
        }
    
    }

```
Junit4中我们仅需要在`@Test`注解中写上`(expected = xxxException.class)`

```java
    public class MathUtilJunit4TestCase {
    
        @Test(expected = NumberFormatException.class)
        public void getDecimalFromString() {
            Assert.assertEquals(456, MathUtil.getDecimalFromString("123.456d"));
        }
    
    }
```

# 限时测试

Junit4新增限时测试，只需要在`@Test`注解中加上`(timeout = 1000)`即可标识这个方法必须在1000ms内跑完，否则测试失败

```java
    // 待测试类
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
    }
```


```java
    public class AsyncTaskJunit4TestCase {
    
        @Test(timeout = 1000)
        public void sync_getOnlineConfig() {
            Assert.assertEquals("value1", new AsyncTask().sync_getOnlineConfig("key1"));
        }
    }
```

![](/img/【Android单元测试系列】二、Junit4使用/4.png)

# @RunWith

在上文Junit3使用中，我们说到，Junit的运作模式： **TestCase** -> **TestSuite** -> **TestRunner** ==> **TestResult**，但是上文并没有怎么讲到`TestRunner`，这是因为在Junit4中讲解比较容易理解。

## 参数化测试 @Parameterized
回顾Junit3中的参数化测试，不断copy同一个方法:

```java
    // 待测试类
    public class StringUtil {
    
        public static boolean isStringNull(String... args) {
            if (args == null) {
                return true;
            }
            for (String arg : args) {
                if (arg == null) {
                    return true;
                }
                if (arg.trim().length() == 0) {
                    return true;
                }
            }
            return false;
        }
    
    }

```
```java
    public class StringUtilJunit3TestCase extends TestCase {
    
        /**
         * 测试字符串是否为空的方法,因为要模拟各种边界情况，这里就详细列出各个情况
         *
         * @throws Exception
         */
        public void test_isStringNull() throws Exception {
            assertEquals(true, StringUtil.isStringNull(null));
            assertEquals(true, StringUtil.isStringNull(null, null));
            assertEquals(true, StringUtil.isStringNull(""));
            assertEquals(true, StringUtil.isStringNull("", ""));
            assertEquals(false, StringUtil.isStringNull("123"));
            assertEquals(true, StringUtil.isStringNull("123", ""));
            assertEquals(false, StringUtil.isStringNull("123", "345"));
        }
    }
```

在Junit4中，我们可以通过

+ `@RunWith`:指定我们的测试环境为参数化测试(`Parameterized.class`)

同时，将所有的参数列为一个数组，并修饰为`@Parameterized.Parameters()`，然后，通过**自定义构造函数**，为每组测试参数赋值，然后调用测试方法进行测试:

```java
    @RunWith(Parameterized.class)
    public class StringUtilJunit4TestCase {
    
        @Parameterized.Parameters()
        public static Iterable<Object[]> data() {
            return Arrays.asList(new Object[][] {
                    {true, null}, {true, new String[] {null, null}}, {true, new String[] {""}}, {true, new String[] {"", ""}},
                    {false, new String[] {"123"}}, {true, new String[] {"123", ""}}, {false, new String[] {"123", "456"}}
            });
        }
    
        private boolean mExpected;
    
        private String[] mArgs;
    
        public StringUtilJunit4TestCase(boolean expected, String... args) {
            mExpected = expected;
            mArgs = args;
    
        }
    
        @Test
        public void isStringNull() throws Exception {
            Assert.assertEquals(mExpected, StringUtil.isStringNull(mArgs));
        }
    
    }
```

## 打包/套件测试 @Site.SuiteClasses
在Junit3中，我们定义一个TestSuite，在运行时，默认是运行在TestSuite中

```java
    public class Junit3TestSuite extends TestSuite {
    
        public static Test suite() {
            TestSuite suite = new TestSuite(Junit3TestSuite.class);
    
            suite.addTestSuite(MathUtilJunit3TestCase.class);
            suite.addTestSuite(StringUtilJunit3TestCase.class);

            return suite;
        }
    }
```

在Junit4中，我们可以通过

+ `@RunWith`：指定测试环境为`Suite.class`
+ `@Site.SuiteClasses({xxx.class, xxx.class})`:指定待测试的类

来实现同样的操作

```java
    @RunWith(Suite.class)
    @Suite.SuiteClasses({MathUtilJunit4TestCase.class, StringUtilJunit4TestCase.class})
    public class Junit4TestSuite {
    
    }

```

# 指定测试方法执行顺序 @FixMethodOrder

从**Junit4.11**版本开始，Junit支持指定测试执行顺序，只需要在测试类添加下面3个注解之一即可:

|参数|说明|
|--|--|
|``@FixMethodOrder(MethodSorters.DEFAULT)``|**默认值**，使用一个确定的但是不可预测的排序|
|``@FixMethodOrder(MethodSorters.NAME_ASCENDING)``|根据测试方法的方法名排序,按照词典排序规则(ASC,从小到大,递增)。|
|``@FixMethodOrder(MethodSorters.JVM)``|保留测试方法的执行顺序为JVM返回的顺序。每次测试的执行顺序有可能会所不同。|


```java
   
    //默认值：使用一个确定的但是不可预测的排序
    @FixMethodOrder(MethodSorters.DEFAULT)
    
    //根据测试方法的方法名排序,按照词典排序规则(ASC,从小到大,递增)。
    //@FixMethodOrder(MethodSorters.NAME_ASCENDING)
    
    //保留测试方法的执行顺序为JVM返回的顺序。每次测试的执行顺序有可能会所不同。
    //@FixMethodOrder(MethodSorters.JVM)
    
    public class OrderTest {
    
        @Test
        public void a() {
            System.out.println("======a======");
        }
    
        @Test
        public void d() {
            System.out.println("======d======");
        }
    
        @Test
        public void c() {
            System.out.println("======c======");
        }
    
        @Test
        public void b() {
            System.out.println("======b======");
        }
    }
```
![](/img/【Android单元测试系列】二、Junit4使用/5.png)

实际测试，``MethodSorters.DEFAULT``和``MethodSorters.NAME_ASCENDING``基本一样的执行顺序，这里还没搞懂不同的地方，而且仅有的这3个参数其实并不能满足我们的实际需求，如果确实需要制定测试执行顺序，感觉下面两个方案可行：

  1. 采用Junit4的这种指定测试执行顺序的参数，但是需要将你的测试方法名都要修改为能够按照字母由小到大排序的命名；
  2. 将需要按照顺序指定的测试方法集中到一个方法中执行，以模拟**按指定顺序**执行。

# 命令行运行测试

通过运行 ``./gradlew test`` 即可运行项目的单元测试用例

# 测试报告

Junit的单元测试报告位置默认在 ``build/reports/tests/`` 目录下，里面有 ``index.html`` 文件，打开就可以看到测试报告（通过率，失败原因等等等）

也可以在 ``build.gradle`` 文件中重新制定测试报告的位置

```gradle
android {
	testOptions {
		// 默认测试报告路径build/reports/androidTests/
		// 可以通过下面代码自定义测试路径
		resultsDir = "${project.buildDir}/testReport"
	}
}


```

# 参考资料
在写本文的时候，我参考了很多相关的资料，但是在编写过程中，忘记了一些参考链接，或者该参考资料仅在某一点上有参考价值，因此总结下来，下面参考资料在我编写本文的时候提供了极大的帮助：

+ **【强推】**[Junit4使用详解](http://www.cnblogs.com/fsjohnhuang/p/4061902.html)
+ [Junit3、4相关使用](http://www.blogjava.net/jnbzwm/archive/2010/12/15/340801.html)
+ [Junit4基本注解使用](http://blog.csdn.net/wangpeng047/article/details/9628449)
+ [Junit4参数化测试、打包测试、异常测试、限时测试](http://blog.csdn.net/wangpeng047/article/details/9630203)
+ [JUnit中按照顺序执行测试方式](http://www.cnblogs.com/nexiyi/p/junit_test_in_order.html)
+ [Junit指定测试执行顺序](http://blog.csdn.net/renfufei/article/details/36421087)
 

# 项目源码地址
本系列的源码我都会统一放在这个项目上[UnitTest](https://github.com/zhitaocai/UnitTest/tree/master)。
