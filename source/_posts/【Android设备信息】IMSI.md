title: 【Android设备信息】IMSI
tags:
  - Android设备标识
categories:
  - Android开发
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

# 参考资料

* http://baike.baidu.com/link?url=daVUmf1YoGhS_RPd1tiScA9ycDVhINEVJLeinTReLM0eoKbXtmzQ41SOZzcUdH5pCRXlgj_m17yk0Cdzfz0wT_
* http://mcclist.com/mobile-network-codes-country-codes.asp
* http://blog.csdn.net/zc0727/article/details/15541289
