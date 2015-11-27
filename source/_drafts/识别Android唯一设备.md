title: 识别Android唯一设备
tags:
  - Android
  - Android设备标识 
categories:
  - Android开发 
date: 2015-11-12 09:35:43
---
1. 如果你知道**imei**，**imsi**这些东西，那么恭喜你，你入坑了
2. 如果你知道**GT-I9500**，那么很高兴认识你，哥们～
3. 如果你知道**Xprivacy**，恭喜你，你进阶了
<!--more-->


AndWay，下面我介绍一些启发性的东西

|路径|内容描述|
|-|-|
|/data/misc/wifi/softap.conf|热点ssid+密码|
|/data/misc/wifi/wpa_supplicant.conf|主要是历史上该手机曾经链接过的wifi的所有信息，包括最后一次链接成功时所用的密码|
|/data/misc/wifi/WCNSS_qcom_cfg.ini|wifi的一些配置，如wifi接受信号功率等|
|/sys/class/net/wlan0/address|本机mac地址|
