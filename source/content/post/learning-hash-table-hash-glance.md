---
title: "教你从零开始写一个哈希表"
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
完整的实现，大概200行代码。这个教程读完需要一到两个小时。
<!--more-->

&emsp;&emsp;哈希表是最有用的数据结构之一。它的快速和可扩展的插入、查询以及删除特性，使得它跟许多的计算机科学问题有关联。
&emsp;&emsp;在这个教程中，我们用C语言实现了开放寻址和再哈希的（方式解决冲突的）哈希表。通过学习这个教程，你将会掌握：
- 理解基础数据结构的底层原理;
- 更加深刻的认识到何时应该使用哈希表，何时不应该使用，以及为何在某些情况下它会失灵；
- 了解一些新的C语言代码。

C语言是编写哈希表极好的语言，因为：
- 这门语言并没有其他的依赖；
- 它是一门底层语言，所以你可以更深刻的理解程序在硬件层面是如何工作的。

&emsp;&emsp;这份教程假定读者熟悉编程以及C语言的语法。教程的代码写的比较直接明了。你遇到的大部分问题都应该先尝试用搜索引擎来解决。如果你遇到了更深层次的问题，请给我提一个[Github Issue](https://github.com/jamesroutley/write-a-hash-table/issues)。

&emsp;&emsp;完整的实现，大概200行代码。这个教程读完需要一到两个小时。

### 目录
1. [导读](https://blog.csdn.net/panxl6/article/details/84964178)
2. [哈希表结构](https://blog.csdn.net/panxl6/article/details/84981059)
3. [哈希函数](https://blog.csdn.net/panxl6/article/details/84981842)
4. [哈希冲突](https://blog.csdn.net/panxl6/article/details/84995844)
5. [哈希表接口](https://blog.csdn.net/panxl6/article/details/84995857)
6. [哈希表扩张](https://blog.csdn.net/panxl6/article/details/84995864)
7. [附录：其他的哈希冲突解决方案](https://blog.csdn.net/panxl6/article/details/84995871)

> [原文地址: https://github.com/jamesroutley/write-a-hash-table](https://github.com/jamesroutley/write-a-hash-table)

*下一篇:*[教你从零开始写一个哈希表--导读](https://blog.csdn.net/panxl6/article/details/84964178)