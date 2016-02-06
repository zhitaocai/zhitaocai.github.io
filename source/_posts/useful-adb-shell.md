title: ADB常用命令手册
tags:
  - adb
categories:
  - Android Tools
date: 2016-01-20 15:07:05
---
记住一些常用的adb命令，会为我们的工作提供极大的帮助 
<!--more-->

## 基本 

```
    adb help            // 查看adb帮助
    adb start-server    // 开启adb服务
    adb kill-server     // 关闭adb服务 
    adb devices         // 显示连接到计算机的设备

    adb [-d|-e|-s <serialNumber>] <command>

        -d 发送命令给usb连接的设备
        -e 发送命令到模拟器设备
        -s <serialNumber> 发送命令到指定设备
```

## 常用

### 清除应用数据

```
    adb shell pm clear yourpackagename
```

### 安装APK

```
    adb install <apkfile> //比如：adb install baidu.apk
```

### 保重新安装apk，留数据和缓存文件

```
    adb install -r <apkfile> //比如：adb install -r baidu.apk
```

### 安装apk到sd卡

```
    adb install -s <apkfile> // 比如：adb install -s baidu.apk
```

### 卸载APK

```
    adb uninstall <package> //比如：adb uninstall com.baidu.search
```
   
### 卸载app但保留数据和缓存文件

```
    adb uninstall -k <package> //比如：adb uninstall -k com.baidu.search
```

### 启动应用

```
    adb shell am start -n <package_name>/.<activity_class_name>
```

### 查看log

```
    adb logcat
```

### 清除log缓存

```
    adb logcat -c
```

### 查看bug报告

```
    adb bugreport
```

### 重启机器

```
    adb reboot
```

### 重启到bootloader，即刷机模式

```
    adb reboot bootloader
```

### 重启到recovery，即恢复模式

```
    adb reboot recovery
```


## 查看运行状态

### 查看设备cpu和内存占用情况

```
    adb shell top
```

### 查看占用内存前6的app

```
    adb shell top -m 6
```

### 刷新一次内存信息，然后返回

```
    adb shell top -n 1
```

### 查看当前内存占用

```
    adb shell cat /proc/meminfo
```

### 查看IO内存分区

```
    adb shell cat /proc/iomem
```

### 查看后台services信息

```
    adb shell service list
```

### 查询各进程内存使用情况

```
    adb shell procrank
```

### 查看进程列表

```
    adb shell ps
```
### 杀死一个进程

```
    adb shell kill [pid]
```

### 查看指定进程状态

```
    adb shell ps -x [PID]
```

## 文件操作

### 将system分区重新挂载为可读写分区

```
    adb remount
    // e.g.
    // 安装系统应用
    // adb remount
    // adb push xxx.apk /system/app
```

### 从本地复制文件到设备

```
    adb push <local> <remote>
```

### 从设备复制文件到本地

```
    adb pull <remote>  <local>
```

### 重命名文件

```
    adb shell rename path/oldfilename path/newfilename
```

### 删除system/avi.apk

```
    adb shell rm /system/avi.apk
```

### 删除文件夹及其下面所有文件

```
    adb shell rm -r <folder>
```

### 移动文件

```
    adb shell mv path/file newpath/file
```

### 设置文件权限

```
    adb shell chmod 777 /system/fonts/DroidSansFallback.ttf
```

### 新建文件夹

```
    adb shell mkdir path/foldelname
```

### 查看文件内容


```
    adb shell cat <file>
```

## 获取设备信息

### 查看Build文件

```
    adb shell cat /system/build.prop
```
### 查看wifi密码

```
    adb shell cat /data/misc/wifi/*.conf
```
### 获取序列号

```
    adb get-serialno
```

### 获取机器MAC地址

```
    adb shell  cat /sys/class/net/wlan0/address
```

### 获取CPU序列号

```
    adb shell cat /proc/cpuinfo
```

## 测试

### 跑monkey

```
    adb shell monkey -v -p your.package.name 500
```

## 推荐

* [详细一点的介绍，包括使用logcat](http://www.iteye.com/topic/260042)
* [Ubuntu/Mac 解决手机ADB识别问题](http://www.cnblogs.com/benhero/p/4287252.html)

### adb shell dumpsys 

* [通过adb dumpsys获取信息](https://testerhome.com/topics/1462)
* [Android 性能分析工具dumpsys的使用](http://www.open-open.com/lib/view/open1405061994872.html)
* [使用adb shell dumpsys检测Android的Activity任务栈](http://blog.iderzheng.com/debug-activity-task-stack-with-adb-shell-dumpsys/)


## 参考资料

* [官方文档](http://developer.android.com/tools/help/adb.html)
* http://www.cnblogs.com/jinsdu/archive/2013/02/21/2919874.html
* http://blog.csdn.net/shuaihj/article/details/8889465
