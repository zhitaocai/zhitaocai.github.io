title: 【Android设备信息】深入理解Build
tags:
  - Android UIDS
categories:
  - Android Develop
date: 2016-04-20 16:35:53
---

相信很多同学都用到过``Build.MODEL``, ``Build.SERIAL``之类的参数，但是这个类下还有其他一些参数，这次我们来深入认识一下这些参数
<!--more-->

## Build.DEVICE, Build.BOARD, Build.BRAND, Build.MODEL, Build.PRODUCT, Build.MANUFACTURER

这几个属性比较密切，所以合在一起来讲，先看栗子：

```
    Build.DEVICE = mako
    BUild.BOARD = MAKO 
    BUild.BRAND = google
    BUild.MODEL = Nexus 4
    BUild.PRODUCT = occam
    Build.MANUFACTURER = LGE
```

栗子看完了，我们来分析一下：

* ``Build.DEVICE``: 设备驱动描述
* ``BUild.BOARD``: 设备基板名称描述
* ``BUild.BRAND``: 商标，描述这台设备的商标
* ``BUild.MODEL``: 设备型号
* ``BUild.PRODUCT``: 产品
* ``Build.MANUFACTURER``: 厂商

最后，我们用人类的语言解读一下上面的栗子：**这是一台occam的设备产品，商标为google，设备名称叫Nexus4，由LGE代工生产，设备基板为MAKO, 驱动为mako**


实际上，在很多国产手机上，这几个参数并没有那么严格的区分，举个栗子：

```
    Build.DEVICE = mx4 
    BUild.BOARD = mx4
    BUild.BRAND = Meizu
    BUild.MODEL = MX4
    BUild.PRODUCT = meizu_mx4
    Build.MANUFACTURER = Meizu
```


简单的介绍说明到这里，在看源码的时候，你可能发现其实他们(整个Build类对外的公开静态成员变量)的值很大一部分是读取通过SystemProperties类进行获取的

```
    // ---------------------------------------------------------
    // Build.java

    /** The name of the industrial design. */
    public static final String DEVICE = getString("ro.product.device");

    private static String getString(String property) {
        return SystemProperties.get(property, UNKNOWN);
    }
    ...
    

    // ---------------------------------------------------------
    // SystemProperties.java

    /**
     * Get the value for the given key.
     * @return if the key isn't found, return def if it isn't null, or an empty string otherwise
     * @throws IllegalArgumentException if the key exceeds 32 characters
     */
    public static String get(String key, String def) {
        if (key.length() > PROP_NAME_MAX) {
            throw new IllegalArgumentException("key.length > " + PROP_NAME_MAX);
        }
        return native_get(key, def);
    }
    ...

```

铺垫好了，关键的Point：

1. **这些几个参数基本可以算为一个组，组内的参数是有一定的关联约束关系的，简单来说不可能``Build.MODEL=Nexus 4``，但是，``Build.BRAND=Meizu``，这是一点可以应用在你校验设备是否存在篡改参数的情况校验**
2. **在防刷上，不能仅仅看``Build.DEVICE``这个值，还需要反射调用下``SystemProperties.get("ro.product.device", "unknown")``，获得其值，判断两者的值是否相等，不等的话，那么就有可能被篡改的可能

## Build.SERIAL

设备序列号，估计很多同学都是用这个来参与到一台唯一设备的标识中，暂时没发现这个值有什么生成规则，但是因为这个值本身就定义为设备序列号，所以，会刷的人，肯定会刷这个值，最后可能会出现奇奇怪怪的值，也有可能直接是**12344567890**之类的。


## Build.FINGERPRINT

设备指纹(描述)

## Build.HOST

设备主机地址，暂时没有发现有什么用，栗子:

* Nexus 4 : Build.HOST = kpfj2.cbf.corp.google.com
* MX 4 : Build.HOST = mz-builder-4

## Build.TAGS

一般为``release-keys``，标识当前为发布版，也有可能为另外一个常用值``test-keys``，模拟器上一般就是``test-keys``了，但是也不能说``test-keys``就是模拟器，只能说大部分就是模拟器，在魅族的新春特别版的Flyme OS上，这个值居然是``test-keys``，哎，令我想起FlyOs有多坑

## Build.TIME

本版本的构建时间，如果大量发现不同版本(Build.VERSION.SDK_INIT)下，存在大量相同的time，那么也是有篡改参数的可能

## Build.TYPE

设备版本类型，主要为**user**或**eng**

## Build.USER

设备用户名，基本上都为**android-build**，国内的OS可能会改动一下，如Meizu的为 **flyme** 之类的，无伤大雅

暂时没有发现其可用性

## Build.getRadionVersion()

无线电固件版本名，可能为空，暂时没有发现其用途

## Build.HARDWARE

设备硬件名称,一般和基板名称（``Build.BOARD``）一样，国产手机上，也可能是写当前所用的cpu型号，如魅族MX4上，``Build.HARDWARE = mt6595``，用的就是联发科MTK平台下的6595芯片

不要太较真这个参数，如果你一定要较真的话，找各个手机的规格参数，看看这个值是否真的如规格参数中描述的那样

## Build.BOOTLOADER

设备引导程序版本号，栗子:

```
    Build.BOOTLOADER = MAKOZ30d
```

这个值不一定有，暂时没发现其作用性

## Build.ID

这个属性保存这当前Android版本名所对应的发布日期，是的，你没看错，是发布日期！返回值格式形如 ``LMY471`` ,这是一个google自定义的日期格式，解读如下:


附: [各个版本所对应的ID值](http://www.cookqq.com/blog/8a10a5f35167c83a01519e5f5d8616a8)

解读完毕，那么你就知道这个值其实不是看上去那么**乱**，或者 **很难理解**了

**But**，我们关键的Point是：**这个值不是乱写的，是有一个约束规则的，因此，我们可以收集这个值，在根据其他参数判断是否合法（e.g 通过收集``Build.VERSION.RELEASE（一般返回如4.4.4）``，然后在根据4.4.4版本下对应的``Build.ID``是否匹配，进行判断这个参数是否合法，是否有被模拟篡改的可能）**

## Build.DISPLAY

这个属性可以理解为``Build.ID`` 的别名，``Build.ID``一般返回用户"很难理解"的版本，为了让用户可理解，一般就会用这个``Build.DISPLAY``来进行版本描述。恩，这样子说吧，**ID和DISPLAY的关系就是VersionCode和VersionName的关系**。

举个栗子：

```
    Build.ID = LMY47I
    BUild.DISPLAY = Flyme OS 5.1.3.0A 新春特别版

```


## 参考资料

* http://www.cookqq.com/blog/8a10a5f35167c83a01519e5f5d8616a8
* http://blog.csdn.net/zy987654zy/article/details/8637435
