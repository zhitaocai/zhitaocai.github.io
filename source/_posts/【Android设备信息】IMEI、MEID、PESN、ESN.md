title: 【Android设备信息】IMEI、MEID、PESN、ESN
tags:
  - Android设备标识
categories:
  - Android开发
date: 2015-11-27 15:29:55
---

你应该需要知道的Android设备标识。

<!--more-->


## IMEI

### 基本定义

IMEI(International Mobile Equipment Identity)是国际移动通讯设备识别号的缩写，由15位数字组成，主要用于**GSM**系统，因此与每台移动/联通手机一一对应，而且该码是全世界唯一的。每一部手机在组装完成后都将被赋予一个全球唯一的一组号码，这个号码从生产到交付使用都将被制造生产的厂商所记录。**移动/联通上可以通过在拨号键盘上输入*#06#进行查询imei**。

IMEI由15位数字组成，其组成为：``IMEI = TAC + FAC + SNR + SP``

* ``TAC``(TYPE APPROVAL CODE)：IMEI前6位，**标识型号核准号码，一般代表机型**。由欧洲型号认证中心分配。TAC码前三位在不同的时期会发生变化，过去的TAC码前三位在现在的手机上不会出现
* ``FAC``(FAC-Final Assembly Code)：IMEI第7位到第8位，**标识最后装配号，一般代表产地**，由厂家编码，通常表示生产厂家及其装配地。也就是我们说的行货认证 
* ``SNR``(Serial Number)：IMEI第9位到第14位，**标识串号，一般代表生产顺序号**，也由厂家分配。识别每个TAC和FAC中的某个设备的。每一部手机的SNR都不会一样．简单的说该号码可以说明手机出产日期的先后，通常数值越大说明该机型出厂时间越晚，所以如果一部刚上市不久的手机的IMEI上出现了6位的数字你就得小心了，因为刚上市不久的手机其SNR最多不会超过四位，大家可以在购机时留意一下。也许这可以作为鉴别手机是否被JS修改IMEI的好办法之一。
* ``SP``：IMEI第15位，**检验码**，过去通常为0，现在市面上的手机基本都会由厂家进行配置这个校验码



### 获取方法

```
    TelephonyManager telephonyManager = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
    String imei = telephonyManager.getDeviceId();
```

### 扩展

* 有些手机在IMEI上做了自己的定义，如：爱立信手机，三星V200在待机时输入*#06#就会出现17位的IMEI，而**最后两位的主要作用是用来识别软件版本，一般来说数值越低版本也越低**
* IMEI 校验算法(SP计算方法):

    1. 分别取偶数位*2，得出7组结果
    2. 针对这7组结果，每组进行（十位数数字 + 个位数数字)，得出新的7组结果，将这7组结果相加得出和1
    3. 取imei中的所有奇数位数字（7个），全部相加得出和2
    4. 和1 + 和2 得出结果，如果结果的个位数数字是0，那么校验码就为0，否则用10减去个位数数字即可以得出校验码
    `e.g:`
    ```
        imei: 35 89 01 80 69 72 41 7(最后这个7为我们要计算出来的)

        1. 偶数位乘以2得到5*2=10 9*2=18 1*2=02 0*2=00 9*2=18 2*2=04 1*2=02
        2. 计算和1：(1+0)+(1+8)+(0+2)+(0+0)+(1+8)+(0+4)+(0+2)=27
        3. 计算和2：3+8+0+8+6+7+4 = 36
        4. 36+27 = 63 校验码为 10-3 = 7.
        
        加上校验码的最终imei：35 89 01 80 69 72 41 7
    ```


### 总结

* 尽管imei定义可以理解为每台手机唯一，但是实际上很容易被改，安装了Xposted(目前好像在Android 5.x以上系统不好使)框架，然后安装Xprivacy就可以轻松替换imei
* imei的校验:
    * 不能通过长度进行判断imei是否有效
    * 可以通过校验算法进行一次校验，过滤一些小白随便起的imei


## MEID/ESN/PESN

### 定义
ESN （Electronic Serial Numbers）
电子序列号。在CDMA 系统中，是鉴别一个物理硬件设备唯一的标识。也就是说每个手机都用这个唯一的ID来鉴别自己, 就跟人的身份证一样。一个ESN有32 bits, 随着CDMA移动设别的增多，ESN已经不够用了，所以推出了位数更多的MEID。ESN用8位的16进制来表示，如0x801EA066。


MEID (Mobile Equipment Identifier)
移动设备识别码是CDMA手机的身份识别码，也是每台CDMA手机或通讯平板唯一的识别码。通过这个识别码，网络端可以对该手机进行跟踪和监管。用于CDMA制式的手机。MEID的数字范围是十六进制的，和IMEI的格式类似。一般为电信的手机
 
MEID的申请，是需要付费的。价格是每1M范围的MEID的费用是8000美元，每增加1M范围的MEID号码需要额外付费8000美元。
MEID号码是由Telecommunications Industry Association（TIA）进行分配管理的。
MEID号码的查看，没有一个通用的方法，由各手机制造商自己设置。可以通过查看手机说明书得到查看MEID号码的方法。
MEID由14个十六进制字符标识，第15位为校验位，不参与空中传输。
 
RR：范围A0-FF，由官方分配
XXXXXX：范围 000000-FFFFFF，由官方分配
ZZZZZZ：范围 000000-FFFFFF，厂商分配给每台终端的流水号
C/CD：0-F，校验码

### 获取方法



# 参考资料

## IMEI/MEID

* http://www.miui.com/article-336-1.html
* http://www.cnblogs.com/flyme/p/3317013.html
* http://my.oschina.net/adairs/blog/418954?fromerr=9LQoQQpv
* http://www.miui.com/article-336-1.html
* http://www.lainzy.net/post/157.html

