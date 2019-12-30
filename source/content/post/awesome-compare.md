---
title: "awesome compare系列"
date: 2019-11-20T09:37:59+08:00
lastmod: 2019-11-20T09:37:59+08:00
draft: true
keywords: []
description: ""
tags: [比较,选型]
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
我们在技术选型的时候，为了认识各类技术栈在各个场景下的优劣，往往需要两者作比较。常见的就是A vs B的文章。为了快速查询，这里收集web开发相关概念的比较列表。
<!--more-->

# (MySQL)主键和唯一索引的区别
| 主键 | 唯一索引      |
|:--------|:-------------|
| 不能为空 | 可以为null |
| 主键是行记录的编号，一般为自增id，auto_increment | 唯一索引，一般用来保证数据的唯一性，去重目的 |
| 主键是每一张表都必要，没有的话会报语法错误 | 唯一索引不是必要的 |
| 一张表只能有一个主键 | 可以有多个 |
| 主键可以被其他表引用为外键 | 无此特性 |
| 主键是一种约束，保证实体的完整性  | 辅助查询的索引 |
| Mysql中的实现是聚簇索引  | 无此特性 |


# Redis和Memcached的区别
| Redis | Memcached    |
|:--------|:-------------|
| 定位为NoSQL数据库 | 缓存中间件 |
| 可以持久化，支持追加和快照两种持久化方式 | 无此特性 |
| 支持五种数据类型 | 只有字符串类型 |
| 字符串类型可以保存二进制数据 | 二进制数据不安全的类型 |
| 支持主从复制 | 不支持 |
| 主键可以被其他表引用为外键 | 无此特性 |
| 单进程单线程  | 多线程 |
| 可以设置密码，实现访问控制 | 不能设置密码 |

# TCP和UDP的区别
| TCP | UDP |
|:--------|:-------------|
| 面向连接的协议 | 面向报文的协议 |
| 具有拥塞控制、失败重试功能保证可靠性 | 无此特性 |
| 只能一对一建立连接 | 支持广播 |
| 由于建立连接需要三次握手，断开连接需要四次挥手，速度较慢 | 速度较快 |
| 包体较大 | 包体较小 |
| 报文接收的顺序和发送的顺序一致 | 报文的发送无序 |