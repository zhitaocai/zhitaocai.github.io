title: 在多个Gradle脚本中传递变量
tags:
  - Gradle
categories:
  - Gradle
date: 2016-02-22 16:07:44
---

![](/img/Momentum/201602221815.jpg)

实际项目中，我们可能在发布的时候，需要调用到很多个项目的gradle脚本，用于分别执行每个项目的一些打包工作，这个时候就涉及到gradle多脚本的相互调用了，本次和大家一起探讨

* 多个gradle脚本相互调用task的方法
* 多个gradle脚本中共享Property

<!-- more -->

# 多个gradle脚本相互调用的方法

## 通过apply from

利用``apply from ``，在``build.gradle``中引入其他Gradle脚本

``build.gradle``:

```gradle
    apply from: 'other.gradle'
```

``other.gradle``:

```gradle
    task show << {
        println "123"
    }
```

## 通过GradleBuild Type

``build.gradle``:

```gradle
    task showAll(type: GradleBuild) {
        buildFile = 'other.gradle'
        tasks = ['show']
    } 
```

``other.gradle``:

```gradle
    task show << {
        println "123"
    }
```

# 在多个gradle脚本中共享Property

关于Property的分类，可以先看看[Gradle Property使用详解](http://caizhitao.com/2016/02/20/gradle-properties/)

## 共享System Property 

默认都能共享

## 共享Gradle Property 

在``build.gradle``中通过``GradleBuild``方法调用其他脚本的时候，指定[GradleBuild](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.GradleBuild.html) 的[StartParameter](https://docs.gradle.org/current/javadoc/org/gradle/StartParameter.html) 参数为本project传入的Properties，那么外部脚本即可共享本project的Gradle Property

``build.gradle``:

```gradle
    task showAll(type: GradleBuild) {
        buildFile = 'other.gradle'
        tasks = ['show']
        // 指定调用外部gradle的脚本的时候，该外部脚本采用的Properties参数为本项目所采用的
        startParameter.projectProperties = project.getGradle().getStartParameter().getProjectProperties()

        // or 

        // startParameter.projectProperties = gradle.startParameter.projectProperties

    } 
```

``other.gradle``:

```gradle
    task show << {
        println var1
    }
```

```gradle
    // 执行命令
    gradle -q showAll -Pvar1=test 

    // 输出 
    test
```

## 共享自定义的Property

利用``apply from ``，在``build.gradle``中引入其他Gradle脚本，然后通过自定义的Property即可跨脚本传递变量或者方法，详细做法可以参考[这里](http://paweloczadly.github.io/dev/2014/07/03/gradle-how-to-use-variables-and-methods-from-another-gradle-file) 

*ps：本来想着翻译转换一下思路，但是改着改着，到最后发现作者写得太好了，怎么改都都表达不出原文的原汁原味，于是就直接贴原文链接出来了*

# 选择与总结

1. ``共享System Property方式``：一般用于指定一些环境变量
1. ``共享Gradle Property方式``：如果其他脚本中所有的task已经写好了逻辑了，就差一个开关之类的控制一些简单的逻辑，那么不妨采用这种方法
2. ``共享自定义的Property方式``：可以很方便地和主脚本(``build.gradle``)进行交互，但是如果主脚本和其他外部脚本同时引入同一个``plugin``，那么在执行task（比如都使用了java插件，两个脚本中分别有一个任务都是依赖于``jar``）的时候，就可能会出现问题

## 参考资料

* [GradleBuild官方文档描述](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.GradleBuild.html)
* [StartParameter官方文档描述](https://docs.gradle.org/current/javadoc/org/gradle/StartParameter.html) 
* https://www.coveros.com/passing-p-parameters-from-one-gradle-script-to-another/
* http://paweloczadly.github.io/dev/2014/07/03/gradle-how-to-use-variables-and-methods-from-another-gradle-file


