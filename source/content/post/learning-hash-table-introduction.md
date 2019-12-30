---
title: "教你从零开始写一个哈希表--导读"
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

&emsp;&emsp;哈希表是一个可以提供快速实现关联数组API的数据结构。跟“哈希表”有关的一些术语也许有些让人产生困惑，因此我在下文列了一些摘要。
&emsp;&emsp;哈希表由一系列的桶组成，每一个桶保存一个键值对。为了找到存放键值对的桶，关键字要传递给哈希函数。哈希函数返回一个指明桶数组下标的整数。当我们想要查询一个键值对时，我们对哈希函数使用同样的关键字，接收哈希函数返回的数组下标，再使用下标找到键值对在桶数组的位置。
&emsp;&emsp;数组寻址的算法复杂度是`O(1)`，这使得哈希表在保存和查询数据时非常的快。
&emsp;&emsp;我们的哈希表将会把字符串关键字映射为字符串的（ASCII码中对应的）值，但理论上哈希表可以将任意的关键字映射为任意的值类型。我们这里只支持ASCII字符串，支持unicode并不是关键的点，而且也超出了本教程的讨论范围。

### API
&emsp;&emsp;关联数组是无序键值对的集合。它不允许出现重复的关键字。受支持的操作如下：
- `search(a, k)`：从关联数组`a`中返回跟关键字`k`关联的值`v`；如果关键字不存在，返回`NULL`
- `insert(a, k, v)`：将`k:v`键值对保存到关联数组`a`中
- `delete(a, k)`：删除跟关键字`k`关联的键值对`k:v`;如果关键字`k`不存在，不做任何操作

### 安装
&emsp;&emsp;安装C语言(开发环境)，请参考Daniel Holden的《Build Your Own Lisp》一书给出的指引。这本书非常的棒，我建议你通读全书。

### 代码结构
&emsp;&emsp;代码放在以下目录结构：
```
.
├── build
└── src
    ├── hash_table.c
    ├── hash_table.h
    ├── prime.c
    └── prime.h
```
`src`目录包含了所有代码，`build`目录包含了我们编译后的二进制文件。

### 术语
&emsp;&emsp;这里有一些可以互相替换的名词。具体如下：
- 关联数组：实现了上文所讲的API的抽象数据结构。它也叫做映射、符号表或者字典。
- 哈希表：关联数组使用哈希函数的快速实现。它也叫做哈希映射、哈希或者字典。

&emsp;&emsp;关联数组可以通过许多不同的底层数据结构来实现。一个（不考虑性能的）实现是通过简单的把数据保存在数据中，然后通过遍历数组来搜索。关联数组和哈希表往往会让人产生困惑，因为关联数组一般是由哈希表实现的。


### 友情链接
- [PHP版本的简易实现](https://github.com/panxl6/blog/blob/master/Write-hashtable-in-php/HashTable.php)
- [LeetCode系统设计题：实现一个哈希表](https://leetcode.com/problems/design-hashmap/)

*上一篇:*[教你从零开始写一个哈希表](https://blog.csdn.net/panxl6/article/details/84846229)
*下一篇:*[教你从零开始写一个哈希表--哈希表结构](https://blog.csdn.net/panxl6/article/details/84981059)
