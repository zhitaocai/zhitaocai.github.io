title: 【Android设备信息】IMEI、MEID、PESN、ESN
tags:
  - Android UIDS
categories:
  - Android Develop
date: 2015-11-27 15:29:55
---
你应该需要知道的Android设备标识。
<!--more-->

# IMEI

## 定义

IMEI(International Mobile Equipment Identity)：国际移动通讯设备识别号的缩写，由15位数字组成，主要用于**GSM**系统，因此与每台移动/联通手机一一对应，而且该码是全世界唯一的。每一部手机在组装完成后都将被赋予一个全球唯一的一组号码，这个号码从生产到交付使用都将被制造生产的厂商所记录。**移动/联通上可以通过在拨号键盘上输入*#06#进行查询imei**。


## 组成

IMEI由15位数字组成，其组成为：``IMEI = TAC + FAC + SNR + SP``

* ``TAC``(TYPE APPROVAL CODE)：IMEI前6位，**标识型号核准号码，一般代表机型**。由欧洲型号认证中心分配。TAC码前三位在不同的时期会发生变化，过去的TAC码前三位在现在的手机上不会出现
* ``FAC``(FAC-Final Assembly Code)：IMEI第7位到第8位，**标识最后装配号，一般代表产地**，由厂家编码，通常表示生产厂家及其装配地。也就是我们说的行货认证 
* ``SNR``(Serial Number)：IMEI第9位到第14位，**标识串号，一般代表生产顺序号**，也由厂家分配。识别每个TAC和FAC中的某个设备的。每一部手机的SNR都不会一样．简单的说该号码可以说明手机出产日期的先后，通常数值越大说明该机型出厂时间越晚，所以如果一部刚上市不久的手机的IMEI上出现了6位的数字你就得小心了，因为刚上市不久的手机其SNR最多不会超过四位，大家可以在购机时留意一下。也许这可以作为鉴别手机是否被JS修改IMEI的好办法之一。
* ``SP``：IMEI第15位，**检验码**，过去通常为0，现在市面上的手机基本都会由厂家进行配置这个校验码

## 获取方法

```java
    /**
     * 获取IMEI
     */
    public static String getIMEI(Context context) {
        try {
            TelephonyManager telephonyManager = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
            if (TelephonyManager.PHONE_TYPE_GSM == telephonyManager.getPhoneType()) {
                return telephonyManager.getDeviceId();
            }
        } catch (Throwable e) {
        }
        return null;
    }
```

## 扩展

### 17位IMEI

**有些手机在IMEI上做了自己的定义，如：爱立信手机，三星V200在待机时输入*#06#就会出现17位的IMEI，而最后两位的主要作用是用来识别软件版本，一般来说数值越低版本也越低**

```java
    /**
     * 获取IMEI的版本号
     * 部分GSM手机上拨号键盘输入*#06#之后会出现的IMEI/SV(software version)
     * 一般在三星的手机上输入*#06#最后会出现15位IMEI + "/01"的内容，后面的就是描述软件版本号的
     */
    public static String getIMEISV(Context context) {
        try {
            TelephonyManager telephonyManager = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
            if (TelephonyManager.PHONE_TYPE_GSM == telephonyManager.getPhoneType()) {
                return telephonyManager.getDeviceSoftwareVersion();
            }
        } catch (Throwable e) {
        }
        return null;
    }
```

### 两个IMEI

**双卡双待的手机有两个通讯芯片，相当于2个手机装在一个手机壳里面，每个通讯芯片都需要有IMEI标识，于是输入*#06#之后**

1. 可能会显示2个IMEI
2. 可能会显示1个IMEI
3. 可能会显示1个MEID
4. 可能会显示2个MEID
5. 可能会显示1个IMEI+1个MEID

但是上面的获取方法只能获取1个，如果是2个以上的话，剩下那个需要其他方法才能获取到


### IMEI校验


* 尽管imei定义可以理解为每台手机唯一，但是实际上很容易被改，安装了Xposted(目前好像在Android 5.x以上系统不好使)框架，然后安装Xprivacy就可以轻松替换imei
* 可以通过校验算法进行一次校验，过滤一些小白随便起的imei
* 最好不要通过长度进行判断imei是否有效

```java
    IMEI 校验算法(SP计算方法):

    1. 分别取偶数位*2，得出7组结果
    2. 针对这7组结果，每组进行（十位数数字 + 个位数数字)，得出新的7组结果，将这7组结果相加得出和1
    3. 取imei中的所有奇数位数字（7个），全部相加得出和2
    4. 和1 + 和2 得出结果，如果结果的个位数数字是0，那么校验码就为0，否则用10减去个位数数字即可以得出校验码

    e.g.

    imei: 35 89 01 80 69 72 41 7(最后这个7为我们要计算出来的)

    1. 偶数位乘以2得到5*2=10 9*2=18 1*2=02 0*2=00 9*2=18 2*2=04 1*2=02
    2. 计算和1：(1+0)+(1+8)+(0+2)+(0+0)+(1+8)+(0+4)+(0+2)=27
    3. 计算和2：3+8+0+8+6+7+4 = 36
    4. 36+27 = 63 校验码为 10-3 = 7.
    
    加上校验码的最终imei：35 89 01 80 69 72 41 7
```


# MEID

## 定义

MEID(Mobile Equipment Identifier)：移动设备识别码是CDMA手机的身份识别码，也是每台CDMA手机或通讯平板唯一的识别码。通过这个识别码，网络端可以对该手机进行跟踪和监管。**主要用于CDMA制式的手机**。MEID的数字范围是十六进制的，和IMEI的格式类似。**一般为电信的手机，同样可以通过在拨号键盘上输入*#06#进行查询**

## 组成


MEID，56bits，由14个十六进制字符标识，``MEID = 区域码（前2位）+生厂商编号（接着6位） + 串号（接着6位） + 校验位（最后一位）``，其中，校验位不参与空中传输。


|Regional code|Manufacturer code|Serial number|CD|
|--|--|--|--|
|RR|XXXXXX|ZZZZZZ|C|

|||
|--|--|
|RR|开头的0xA表示CDMA手机，如果是0x9, 就表示多模手机，一般特指双卡双待。|
|XXXXXX|范围 000000-FFFFFF，由官方分配，厂商生产编号|
|ZZZZZZ|范围 000000-FFFFFF，厂商分配给每台终端的流水号|
|CD|0-F，校验码|


## 获取方法

```java
    public static String getMEID(Context context) {
        try {
            TelephonyManager telephonyManager = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
            if (TelephonyManager.PHONE_TYPE_CDMA == telephonyManager.getPhoneType()) {
                return telephonyManager.getDeviceId();  
            }
        } catch (Throwable e) {
        }
        return null;
    }
```

## 扩展 

* **MEID理论上是14位（不包括校验位）的长度，但是有些机子会是16位（不包括校验位）的长度，``典型的例子：14位的MEID：0xA0，16位的MEID：0x00A0``，为了统一，可以直接截取后面14位作为最后的MEID**
* MEID的申请，是需要付费的。价格是每1M范围的MEID的费用是8000美元，每增加1M范围的MEID号码需要额外付费8000美元
* MEID号码是由Telecommunications Industry Association（TIA）进行分配管理的
* MEID号码的查看，没有一个通用的方法，由各手机制造商自己设置。可以通过查看手机说明书得到查看MEID号码的方法
* MEID校验算法：
    1. 将MEID偶数位数字分别乘以2，得到的7组结果
    2. 针对这7组结果，每组进行（十位数数字 + 个位数数字)，得出新的7组结果，将这7组结果相加得出和1
    2. 将MEID奇数位数字相加得到和2
    3. 和1 +和2，如果得出的数个位是0，则校验位为0，否则为10(这里的10是16进制)减去个位数
    ```
        MEID: AF 01 23 45 0A BC DE C(最后这个C为我们要计算出来的)
        
        1. 偶数位乘以2得到各组结果:F*2=1E,1*2=02,3*2=06,5*2=0A,A*2=14,C*2=18,E*2=1C
        2. 计算上面各组结果的个位数和十位数之和得出和1:(1+E)+(0+2)+(0+6)+(0+A)+(1+4)+(1+8)+(1+C)=3C(16进制下)
        3. 将原始MEID的奇数位相加得出和2:A+0+2+4+0+B+D=28(16进制下)
        4. 和1+和2:3C+28=64(16进制下)，于是最终检验位10-4=C
        
        加上校验码的最终MEID：AF 01 23 45 0A BC DE C
    ```



# ESN/pESN

## 定义

ESN(Electronic Serial Numbers)：电子序列号。在CDMA 系统中，是鉴别一个物理硬件设备唯一的标识。也就是说每个手机都用这个唯一的ID来鉴别自己, 就跟人的身份证一样。**一个ESN有32 bits**, 随着CDMA移动设别的增多，ESN已经不够用了，所以推出了位数更多的MEID。**ESN用8位的16进制来表示，如0x801EA066。**

pESN(pseudo ESN):伪ESN。pESN的推出是为了解决前向兼容的问题，**pESN的格式与ESN是完全一样的，唯一的区别是pESN是采用0x80开头的**。将MEID转为pESN，就可以在支持ESN的C网内正常使用。

## 扩展

*  MEID转化为pESN具体的方法是，56 bits的MEID通过SHA-1 hash算法，挑出后6位，然后在开头加上0x80。**pESN并不是唯一的，是可能会重复的，但pESN不会跟ESN重复，因为开头强制加了0x80**；java中提供了MessageDigest这个类来实现了一系列的hash算法，可以通过调用该类来进行运算，计算过程中注意byte和hexstring之间的转换即可。
* 一个手机只能有一个ESN或一个MEID。如6800、6900均是ESN码；但6950开始就采用MEID码了。
* 带ESN或MEID码的手机都可以支持ESN的CDMA网络内正常使用，而ESN码的手机不能在只支持MEID的CDMA网络内使用。
* 目前中国电信的C网应该已经开始采用MEID鉴权了，从2005年开始MEID开始替换ESN。


# 总结

* CDMA系统上，我们叫MEID，GSM系统上，我们叫IMEI
* **正常情况下**，手机上我们获取到``14位MEID``，或者``15位IMEI``

# 参考资料

* http://www.cnblogs.com/flyme/p/3317013.html
* http://my.oschina.net/adairs/blog/418954?fromerr=9LQoQQpv
* http://www.miui.com/article-336-1.html
* http://www.lainzy.net/post/157.html
* http://baike.baidu.com/view/2823315.htm
* [扩展]http://wenku.baidu.com/view/9aed75f37c1cfad6195fa70e.html 
* [扩展]http://www.docin.com/p-555856641.html
