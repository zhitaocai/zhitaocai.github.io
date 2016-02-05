title: 【Android设备信息】IMSI、ICCID、KI
tags:
  - Android UIDS
categories:
  - Android Develop
date: 2015-12-01 10:41:56
---
你应该需要知道的Android设备标识。
<!--more-->


# IMSI

## 定义

IMSI(International Mobile Subscriber Identification Number)：国际移动用户标识码，储存在SIM卡中，可用于区别移动用户的有效信息。其总长度不超过15位，使用0~9的数字

## 组成

``IMSI = MCC(前3位)+ MNC(接下来2~3位) + MSIN(接下来10位)``

|||
|-|-|
|**MCC(Mobile Country Code)**|移动国家码，由国际电信联盟（ITU）在全世界范围内统一分配和管理，唯一识别移动用户所属的国家，共3位，**中国为460**|
|**MNC(Mobile Network Code)**|移动网络号码，用于识别用户所归属的通讯网|
|**MSIN(Mobile Subscriber Identification Number)**|移动用户识别号码，用以识别某一个通讯网中的用户|

## 获取方法

```java
    public static String getIMSI(Context context) {
        TelephonyManager telephonyManager = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
        return telephonyManager.getSubscriberId();
    }
```

## 扩展

### MCC&&MNC

根据MCC和MNC的定义，我们可以在[这里](http://mcclist.com/mobile-network-codes-country-codes.asp)查询到对应的移动网络说对应的MNC

|中国移动|中国联通|中国电信|中国铁通|
|--|--|--|--|
|46000|46001|46003(CDMA)|46020(GSM-R)|
|46002|46006|46005(CDMA)||
|46007||46011(4G)||


### 处理两个IMSI

在大天朝，你不能仅仅看上面的定义，很早之前就有双卡双待的手机了，尽管IMSI的定义差不多，但是我们需要获取的不仅仅是一个IMSI，而是两个IMSI

1. 获取当前使用的IMSI可以直接通过上面的获取方法进行获取
2. 获取第二个IMSI则需要根据不同平台，不同厂商进行处理，网上有些牛人已经总结了不错的方法，点击[这里](http://blog.csdn.net/zc0727/article/details/15541289)

## 总结

* IMEI是与手机绑定的。IMSI是与SIM（Subscriber Identity Module，用户识别模块）或者USIM（Universal Subscriber Identity Module，全球用户身份模块）
* IMSI不等同于手机号码，更多是一个映射，该IMSI对应一个指定的手机号码
* 代码上获取手机号码据传是有一些方法的，但是不靠谱，我们可以自己猜想一下，tips：补卡，换卡

# ICCID

## 定义

ICCID(Integrate circuit card identity)：集成电路卡识别码（固化在手机SIM卡中） ICCID为IC卡的唯一识别号码，共有20位数字组成，**存储在SIM卡中**

## 组成

``ICCID=XXXXXX 0MFSS YYGXX XXXXX``

|||
|-|-|
|中国移动编码格式|89860 0MFSS YYGXX XXXXP|
|中国联通编码格式|89860 1YYMH AAAXX XXXXP|
|中国电信编码格式|89860 3MYYH HHXXX XXXXX|

具体的编码格式说明详见[这里](http://baike.baidu.com/item/iccid)

## 获取方法

```java
    /**
     * 获取sim卡序列号iccid 不同于misi
     *
     * @param context
     *
     * @return
     */
    public static String getICCID(Context context) {
        TelephonyManager telephonyManager = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
        return telephonyManager.getSimSerialNumber();
    }
```

# KI

## 定义

KI(Key identifier)：是SIM卡与运营商之间加密数据传递的密钥，**存储在SIM卡中**。GSM的加密方式是一种称为comp-128的数字加密运算，当系统进行验证时会同时使用Ki及IMSI，经过一连串系统安全认证讯息后产生随机变量，并以A3算法进行加密运算与手机内存资料进行比对，当身份确认无误后始可入网。目前GSM使用的KI长度是16 bytes，相当于128bits，若非经过特殊译码程序，使用者无法读取Ki，安全性极高，使用者无须担心有被盗打电话的顾虑

## 扩展
只要知道SIM卡的KI、IMSI值，我们就可以通过软件仿真出SIM卡的功能，甚至可以利用多组KI、IMSI值，用一张微处理器卡片来同时仿真本来需要多张SIM所完成的功能，这就是“一卡多号”技术。

# 总结

* 一张SIM卡，里面有ICCID，也有IMSI。 ICCID是卡的标识，IMSI是用户的标识。
* ICCID只是用来区别SIM卡，不作接入网络的鉴权认证。而IMSI在接入网络的时候，会到运营商的服务器中进行验证。
* ICCID可以伪造，可以用一张空白多号卡，写入IMSI和KI，只要是经过破解的IMSI和KI，就可以接入网络，而ICCID可以任意20位数字。
* iPhone手机在激活的时候，会把ICCID和IMSI一起发送到苹果服务器端进行验证。特别是有锁的手机，就使用IMSI来判断是否合法运营商，如果不合法，就无法激活。ICCID作为SIM卡标识，在激活的时候被记录下来，直到下次刷机，在服务端的记录都不会被改变。

# 参考资料

* http://baike.baidu.com/link?url=daVUmf1YoGhS_RPd1tiScA9ycDVhINEVJLeinTReLM0eoKbXtmzQ41SOZzcUdH5pCRXlgj_m17yk0Cdzfz0wT_
* http://mcclist.com/mobile-network-codes-country-codes.asp
* http://blog.csdn.net/zc0727/article/details/15541289
* http://www.chinasnow.net/index.php/archives/256
* http://bbs.zol.com.cn/sjbbs/d297_291682.html
* http://baike.baidu.com/item/iccid
