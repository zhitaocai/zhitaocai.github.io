title: 如何优雅正确地使用Log
tags:
  - Android 
categories:
  - Android Develop
date: 2016-05-17 08:15:45
---

![](/img/Momentum/201605170820.jpg)

本文详细描述了如何采用优雅的姿势并且正确地使用log
<!--more-->

# 原生的Android Log使用

像下面这样子的Log，大家都会用
```
Log.i("test", "hello world");
Log.i("test", "hello world " + 1);
Log.i("test", "hello world ", new Exception("test"));
```
## 原生Android Log的语句不够优雅

使用原生Android Log过程中，难免会发现上面的原生Log，用多了，会感觉用得很累，就比如这样子的Log:

```
long startTime = System.currentTimeMillis();
// ......
long endTime = System.currentTimeMillis();
Log.i("test", "Strart Time: " + startTime + "\nEnd Time: " + endTime + "\nTotal Time: " + (endTime - startTime));
```

这种log，先不说写的时候多麻烦，单纯看上去就觉得很累，必须要看完整句才大概知道这个Log的含义，**因为一句话被拆分了，中间穿插着不同的参数**，自然而然，为了优雅，大家都可能会想到用``String.format()``方法来进行优雅一点的输出：

```
long startTime = System.currentTimeMillis();
// ......
long endTime = System.currentTimeMillis();
Log.i("test", String.format("Strart Time: %d\nEndTime: %d\nTotalTime: %d", startTime, endTime, endTime - startTime));
```

## 原生的Android Log并不能很好地输出详细的信息

使用原生的Android Log，除非是输出异常的Log，否则是不会详细显示Log的调用信息的，简单来说，我要知道某句正常的Log是在哪里被调用的（比如你接手别人的项目的时候，输出一大堆不知道写在哪里的Log），那么原生的AndroidLog是不支持的，那么我们就要在自己动手一下

```
// 输出log的详细调用信息
try {
    StackTraceElement[] elements = Thread.currentThread().getStackTrace();
    if (elements.length > 5) {
        StackTraceElement element = elements[5];
        String txt = String.format("FileName:[ %s ] Method:( %s )  Line:( %d )", element.getFileName(), element.getMethodName(), element.getLineNumber());
        Log.i(tag, txt);
    }
} catch (Throwable e) {
    e.printStackTrace();
<F11><F11><F11>}
```

# 封装自己的Log

基于上面点，我这边自己动手封装一个DLog，概览如下(具体代码请点击[这里](https://github.com/zhitaocai/DLog))：

![](/img/android/log/DLogPreview.png)


# 优雅地控制Log输出

实际项目开发中，我们当然是希望调试的Log在我们开发测试的时候才进行输出，在发布的版本中不进行输出

为什么?

1. 如果你的调试Log在发布版本中也存在，那么apk的体积肯定是变大的
2. 如果你的调试Log在发布版本中也存在，那么apk被心怀恶意的人反编译破解的时候，Log的输出就是很好的切入点，能极大地帮助他们理解你的代码

**总结下来，我们需要的就是一个开关，在我们开发测试的时候，开启Log，在我们发布的时候，关闭Log**

## 使用BuildConfig来控制Log输出

Setp 1: 配置项目Module的build.gradle

```gradle
    buildTypes {
        debug {
            buildConfigField "boolean", "ISDEBUG", "true"
        }
        release {
            buildConfigField "boolean", "ISDEBUG", "false"
        }
    }

```

Setp 2: 在java代码中如下使用即可

```java
    if (BuildConfig.ISDEBUG) {
        DLog.i("test", "test");
    }
    
```

**构建release版本，BuildConfig中的ISDEBUG参数就会是false，那么if就永远跑不进去，那么就不会将Log的代码打包到apk中，同理，构建debug版本时，则会输出log**

## 使用Proguard来控制Log输出 

Setp 1: 将自己使用的封装Log类写入到混淆配置(我这里为proguard-rules.pro)：

```java
# 删除代码中DLog相关的代码
-assumenosideeffects class io.github.zhitaocai.dlog.DLog {
    public static void i(...);
    public static void e(...);
    public static void d(...);
    public static void w(...);
    public static void v(...);

    public static void ti(...);
    public static void te(...);
    public static void td(...);
    public static void tw(...);
    public static void tv(...);
}
```

Setp 2: 开启混淆优化：

```gradle
    buildTypes {
        release {
            minifyEnabled true      // 开启混淆
            signingConfig signingConfigs.release

            // 注意是用proguard-android-optimize.txt而不是proguard-android.txt
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'

            // proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
```

Setp 3: java代码中使用的Log如下：

```java
    DLog.i("test", "test");
```


**解释说明:**

> ``-assumenosideeffects class_specification:`` Specifies methods that don't have any side effects (other than maybe returning a value). In the optimization step, ProGuard will then remove calls to such methods, if it can determine that the return values aren't used. ProGuard will analyze your program code to find such methods automatically. It will not analyze library code, for which this option can therefore be useful. For example, you could specify the method System.currentTimeMillis(), so that any idle calls to it will be removed. With some care, you can also use the option to remove logging code. Note that ProGuard applies the option to the entire hierarchy of the specified methods. Only applicable when optimizing. In general, making assumptions can be dangerous; you can easily break the processed code. Only use this option if you know what you're doing!

1. ``assumenosideeffects`` ： 可以理解为假设某个东西无效，那么混淆（具体为优化阶段）之后就会将这里面假定无效的类移除，从而达到不输出Log的效果
2. 正如说明，要令assumenosideeffects生效，就需要开启混淆 
3. 正如说明，要令assumenosideeffects生效，就需要开启混淆中的优化选项，而默认的proguard-android.txt是不会开启优化选项的 ![](/img/android/log/proguard-android-txt-part.png) 并且，从上面 proguard-android.txt 部分内容截图中，我们可以知道，如果我们需要开启混淆的话，那么是建议我们采用**proguard-android-optimize.txt**，那么就可以解释为什么我们Setp 2需要采用 ``proguard-android-optimize.txt`` 作为Android的默认混淆配置了

## 比较

### 测试代码

```java

public class MainActivity extends AppCompatActivity {

	private final static String DOWNLOAD_TAG = "download_";

	private int i = 10;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);

		// 正常的Android Log 封装使用
		Log.i("aaaaa", "=======华丽分割线1=======");
		DLog.i("test", "下载测试: 1");
		DLog.d("test", "下载测试: " + 2);
		DLog.w("test", "下载测试: %d", 3);
		DLog.e("test", new Exception("测试的Exception"), "下载测试: 4");
		DLog.v("test", new Exception("测试的Exception"), "下载测试: %d", 5);

		Log.i("aaaaa", "=======华丽分割线2=======");
		// 支持指定前缀tag（方便给tag分类）以及可以指定具体调用类的Log方式
		DLog.ti(DOWNLOAD_TAG, this, "下载测试 1");
		DLog.td(DOWNLOAD_TAG, this, "下载测试 " + 2);
		DLog.tw(DOWNLOAD_TAG, this, "下载测试 %d", 3);
		DLog.te(DOWNLOAD_TAG, this, new Exception("测试的Exception"), "下载测试 4");
		DLog.tv(DOWNLOAD_TAG, this, new Exception("测试的Exception"), "下载测试 %d", 5);

		Log.i("aaaaa", "=======采用Proguard控制Log输出=======");
		proguardLogTest();

		Log.i("aaaaa", "=======采用BuildConfig控制Log输出=======");
		BuildConfigLogTest();

	}

	private void proguardLogTest() {
		// 测试1: log输出中使用方法
		Log.i("aaaaa", "=======proguard 1=======");
		byte[] input = new byte[] { 1, 2 };
		DLog.i("network", "加密byte数组: %s", formatByte2String(input));

		// 测试2: log输出中使用方法，并且该方法中和其他存在关联
		Log.i("aaaaa", "=======proguard 2=======");
		DLog.ti(DOWNLOAD_TAG, this, "下载测试 %d", incrementI());
		DLog.ti(DOWNLOAD_TAG, this, "下载测试 %d", incrementI());
		DLog.ti(DOWNLOAD_TAG, this, "下载测试 %d", incrementI());

		// 测试3: 外部先构建好一个对象，然后输出该对象的toString
		Log.i("aaaaa", "=======proguard 3=======");
		TestModel testModel = new TestModel("下载测试 1000");
		DLog.ti(DOWNLOAD_TAG, this, testModel.toString());
	}

	private void BuildConfigLogTest() {
		// 测试1: log输出中使用方法
		Log.i("aaaaa", "=======proguard 1=======");
		byte[] input = new byte[] { 1, 2 };
		if (BuildConfig.DEBUG) {
			DLog.i("network", "加密byte数组: %s", formatByte2String(input));
		}

		// 测试2: log输出中使用方法，并且该方法中和其他存在关联
		Log.i("aaaaa", "=======proguard 2=======");
		if (BuildConfig.DEBUG) {
			DLog.ti(DOWNLOAD_TAG, this, "下载测试 %d", incrementI());
			DLog.ti(DOWNLOAD_TAG, this, "下载测试 %d", incrementI());
			DLog.ti(DOWNLOAD_TAG, this, "下载测试 %d", incrementI());
		}

		// 测试3: 外部先构建好一个对象，然后输出该对象的toString
		Log.i("aaaaa", "=======proguard 3=======");
		TestModel testModel = new TestModel("下载测试 1000");
		if (BuildConfig.DEBUG) {
			DLog.ti(DOWNLOAD_TAG, this, testModel.toString());
		}
	}

	/**
	 * 假设这个方法为将byte数组加密成Base64(对应于实际场合中的，和服务器联调测试时，解密网络请求中返回的数据时用)
	 *
	 * @return encodeString
	 */
	private String formatByte2String(byte[] input) {
		return Base64.encodeToString(input, Base64.NO_WRAP);
	}

	private int incrementI() {
		return ++i;
	}

	static class TestModel {

		protected String mTest;

		public TestModel(String test) {
			mTest = test;
		}

		@Override
		public String toString() {
			final StringBuilder sb = new StringBuilder("TestModel {\n");
			sb.append("  mTest=\"").append(mTest).append('\"').append("\n");
			sb.append('}');
			return sb.toString();
		}
	}
}
```

### 反编译结果


![](/img/android/log/BuildConfig_vs_Proguard.png)


### 结论

* BuildConfig
    * Log输出时，如果DLog.i中的传入参数是一些方法的返回值或者自定义对象的某些方法诸如此类的依赖值，那么用BuildConfig这种方法时，是连这个方法或者自定义对象的方法都不会执行，完成优化掉，减少apk体积同时也优化运行速度（因为不用在调用这些方法）
    * 缺陷就是使用的时候比较麻烦，不断写``if(BuildConfig.ISDEBUG) {...}``，比较麻烦
* Proguard
    * Log输出时，如果DLog.i中的传入参数是一些方法的返回值或者自定义对象的某些方法诸如此类的依赖值，那么用Proguard这种方法时，仅仅是会不输出Log，但是，Log传入参数所依赖的方法或者自定义对象的方法是会执行的，不能很好优化掉，并且你还要确定，这部分还继续被执行的代码是否可以真的继续执行
    * 其实采用这种方法还是有一点弊端的样子，在 ``proguard-android-optimize.txt`` 文件中存在一段话: ![](/img/android/log/proguard-android-optimize-txt-part.png) **混淆时，开启优化选项存在风险**
    * 优点：可以直接调用DLog.i()进行打log，而不用像BuildConfig那样子，每次打Log之前都需要if一下

**Any Way, 最终结论是建议大家使用BuildConfig的方法控制Log开关，使用Proguard的方法来控制Log的话需要慎用**

# 参考文献

* http://proguard.sourceforge.net/
* http://www.jianshu.com/p/60e82aafcfd0
