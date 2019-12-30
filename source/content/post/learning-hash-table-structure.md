---
title: "教你从零开始写一个哈希表--哈希表结构"
date: 2019-09-01T22:17:30+08:00
lastmod: 2019-09-01T22:17:30+08:00
draft: true
keywords: []
description: ""
tags: [哈希表,数据结构]
categories: [数据结构]
author: "panxl6"

comment: true
toc: true
autoCollapseToc: true
postMetaInFooter: false
hiddenFromHomePage: false
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

<!--more-->


&emsp;&emsp;一个web请求往往要求快速得到响应，但是一个请求对应的业务可能存在一些复杂、耗时的逻辑需要执行。此时，我们可以将后置逻辑以消息的形式将耗时的业务剥离出来。那么消息队列的信息该如何消费呢？

## 使用crontab拉取消费进程
&emsp;&emsp;我们可以在crontab里面配置一个定时任务来消费消息。比如，我们每隔1s拉起一个进程消费消息。
这种的实现方式比较简单,但是存在多个问题：
1. 如果一个topic建立一个定时任务来处理，那么crontab的数量会非常的巨大，后续改动crontab会难以维护；
2. 如果我们只建立一个crontab，将多个消费任务放在配置中心，那么所有的定时任务都必须依照同样的格式（继承同一个抽象基类）来实现任务；
3. 如果定时间隔太太，消息消费不及时。定时间隔太小，进程的创建、上下文切换频繁，服务器的资源利用效率低下；
4. 如果出现消息堆积，定时间隔较小时，服务器上会拉起大量的进程（进程数量不可控），这些进程同时存在，并发生频繁的上下文切换。此时消息消费进程，占用了大量的服务器资源，严重的情况下可能导致服务器崩溃。


## 常驻内存的守护进程
功能概述
1. 通过信号重启、关闭进程，重启的时候重新加载配置(队列名称 => job进程)；
2. 设置进程上限、请求处理数量上限，当处理请求次数达到上限时重启进程，防止内存泄漏；
3. 消费策略；

## 进程托管服务(远程调用)
&emsp;&emsp;上面的两个方法都必须要求你建立一个消息队列，这样的一个实现方式是比较重的。
&emsp;&emsp;如果我们把一个函数在一台指定的服务器上执行，且这个函数无需任何的返回。这就相当于退化成为一个没有返回的RPC调用。当发送的消息体积比较小，消息的发送间隔比较长（非实时流数据），可以考虑使用RPC服务。