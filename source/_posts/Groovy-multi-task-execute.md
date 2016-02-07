title: 【Gradle随笔】多任务创建与执行
tags:
  - Groovy
  - Gradle
categories:
  - Gradle
  - Groovy
date: 2016-02-07 13:23:32
---
![](/img/Momentum/201602071323.jpg)
某日在逛stackoverflow的时候遇到一个提问，恰好和我之前遇到的差不多，于是就记下来了：
**Jörgen Lundberg 提出如何将下面的写法优化缩减**
```gradle
    task cleanCommon(type: GradleBuild) {
      buildFile = 'common/build.gradle'  
      tasks = ['clean']  
    }

    task cleanCrawler(type: GradleBuild) {
      buildFile = 'crawler/build.gradle'
      tasks = ['clean']
    }

    task cleanPortlet(type: GradleBuild) {
      buildFile = 'portlet/build.gradle'
      tasks = ['clean']
    }

    task cleanAll(dependsOn: ['cleanCommon', 'cleanCrawler', 'cleanPortlet']) { 
    }
```
<!--more-->

在围观了一众大神的回答之后，我突然觉得我自己的回复算是比较简洁的，因此就记录下来：

```gradle

    def List buildFileList = ['common/build.gradle', 'crawler/build.gradle', 'portlet/build.gradle']

    task cleanAll << {
        buildFileList.each() {
            def tempTask = tasks.create(name: "execute_$it", type: GradleBuild)
            tempTask.buildFile="$it"
            tempTask.tasks = ['clean']
            tempTask.execute()
        }
    }

```

PS：刚刚上去看了一下，也看到其他大神在陆续回复，如果大家还继续感兴趣的话，[点我围观](http://stackoverflow.com/questions/13703218/with-gradle-is-it-possible-to-call-gradlebuild-directly-instead-of-specifying-i)

