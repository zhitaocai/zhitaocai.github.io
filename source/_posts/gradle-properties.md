title: Gradle Property 使用详解
tags:
  - Gradle 
categories:
  - Gradle 
date: 2016-02-20 13:31:09
---

![](/img/Momentum/201602201404.jpg)

Gradle支持很多种方式去设置Property，方式太多了，我们有必要了解一下每种方式的使用

<!-- more -->

# Property 分类

Property 可以分为3类：

* Gradle Property 
* System Property 
* 自定义Property 

# Gradle Property

## 通过命令行定义

通过命令行定义的Gradle Property，格式必须为 ``-Pxxx``，其中``xxx``为对应的Property名字

```gradle
   gradle -q showGradleProperty -PmyProperty=myPropertyFromGradleCMD
```

``build.gradle``:

```gradle
    task showGradleProperty << {
        println myProperty 
    }
```

## 通过gradle.properties定义

``gradle.properties``:

```
    myProperty=myPropertyFromGradleProperties
```

``build.gradle``:

```gradle
    task showGradleProperty << {
        println myProperty 
    }
```

# System Property

## 通过命令行定义

我们可以通过-D参数定义JVM的系统参数，格式必须为 ``-Dorg.gradle.project.xxx``，其中``xxx``为对应的Property名字

```gradle
   gradle -q showSystemProperty -Dorg.gradle.project.myProperty=myPropertyFromSystemPropertyCMD
```

``build.gradle``:

```gradle
    task showSystemProperty << {
        println System.properties['myProperty']
    }
```

## 通过gradle.properties定义

通过gradle.properties定义的SystemProperty，格式必须为 ``systemProp.xxx``，其中``xxx``为对应的Property名字

``gradle.properties``：

```
    systemProp.myPropert=myPropertyFromGradlePropertiesSystemProp
```

``build.gradle``:

```gradle
    task showSystemProperty << {
        println System.properties['myProperty']
    }
```
## 通过系统环境变量设置

在环境变量中定义的SystemProperty，格式必须为 ``ORG_GRADLE_PROJECT_xxx``，其中``xx``为对应的Property名字

```bash
   export ORG_GRADLE_PROJECT_myProperty=myPropertyFromEnv 
```

``build.gradle``:

```gradle
    task showSystemPropertyFromEnv << {
        println myProperty 
    }
```

# 自定义Property

## 通过ext定义

``build.gradle``:

```gradle

    ext.myProperty = "myProperty from ext"

    // or

    // ext {
    //     myProperty = "myProperty from ext"
    // }

    task showProperty << {
        println myProperty 
    }
```

# 优先级

从Property的传入方式，我们大致分为

* ``命令行传入Property``
* ``在gradle.properties传入Property``
* ``在系统环境变量中传入Property``

如果在多个地方设置了同一个Property，那么优先采用次序依次为：

``命令行传入Property`` > ``在gradle.properties传入Property`` > ``在系统环境变量中传入Property``

# 参考资料

* https://docs.gradle.org/current/userguide/build_environment.html
* http://www.cnblogs.com/davenkin/p/gradle-learning-5.html

