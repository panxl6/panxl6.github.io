---
title: "开发环境的两种维护方式比较"
date: 2019-09-01T22:17:30+08:00
lastmod: 2019-09-01T22:17:30+08:00
draft: true
keywords: []
description: ""
tags: [自我]
categories: []
author: "panxl6"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---
维护一套公用的开发环境，还是每个人自己搭？这是一个问题。
<!--more-->

### 分散式开发环境的问题：
1. 重复搭建开发环境（从零开始重新配置），对新人不友好；
2. 对于跟第三方团队（外部依赖）有远程调用的接口，本地无法使用，因为对方设置了IP白名单限制；
3. 联调复杂，内网IP发生变更时，需要修改配置。如果一个项目联调时设计到多个成员，那么配置IP都要花很长的时间；
4. 联调的过程中如果调试代码，容易影响联调，需要再独立搭建一个联调环境；
5. 如果两个分支同时开发，而且两个分支之间的代码需要互相调用，双方的代码都在各自本地环境，联调困难；
6. 前后端没有一个可以完整体验的项目，因为各自的代码和功能都放在了本地；

### 分散式开发环境的好处：
1. 做到了天然的隔离，一个本地环境就可以看做一个单独的容器；

### 集中式开发环境的问题：
1. 一旦有一个人搞坏了开发环境，整个团队都无法正常工作；
2. 多个人同时修改一个文件时，存在代码冲突，必须立刻解决。不然继续后面的工作；
3. 集中式环境，一般需要通过ftp、ssh等方式将本地文件上传到远程服务器。如果没有版本控制，发生代码冲突时，难以感知；
4. 多人在服务器上远程调试，服务器的压力会比较大；


### 集中式开发环境的好处：
1. 由于整个团队都在维护一个环境，开箱即用，对新人比较友好；
2. 由于集中放在远程服务器，可以方便的进行远程办公；