title: vim即时预览markdown
tags: 
  - vim plugs
categories:
  - 搭建个人博客
  - vim
description: write markdown via vim
date: 2015-09-22 22:40:14
---
这次和大家分享一个vim即时预览markdown的插件[vim-instant-markdown](https://github.com/suan/vim-instant-markdown)
<!--more-->

# 前言 
平时写东西的时候，大家应该很常用markdown，但是关于用什么编辑器写markdown却又是个纠结的事情，这里我们不纠结，直接和大家分享一个vim上的即时预览插件 [vim-instant-markdown](https://github.com/suan/vim-instant-markdown)

# 配置
1. 按照作者关于[插件安装说明描述](https://github.com/suan/vim-instant-markdown#installation)进行安装
2. 如果上面的方法之后不行就请看下面的

    vim-instant-markdown的安装相比其他插件较为特殊，它由 ruby 开发，所以你的 vim 必须集成 ruby 解释器，并且安装 ``pygments.rb``、``redcarpet``、``instant-markdown-d`` 三个依赖库：

```bash

    # 执行下面命令之前需要先翻墙

    gem install pygments.rb
    gem install redcarpet

    # 若系统提示无 npm 命令
    # 1. 安装npm ,如果没法用apt-get方法安装的话，参考这里http://blog.fens.me/nodejs-enviroment/
    sudo apt-get install npm

    # 2. 安装intant-markdown-d
    npm -g install instant-markdown-d

```

# 参考资料
* [ubuntu 升级ruby到2.1](http://my.oschina.net/fxhover/blog/382634)
* [vim 即时预览markdown](http://treelib.com/book-detail-id-48-aid-2914.html)
* [vim-instant-markdown具体详细配置](http://guqian110.github.io/pages/2015/02/01/learning_vim_markdown.html)
