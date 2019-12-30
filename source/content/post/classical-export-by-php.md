---
title: "PHP数据导出功能优化案例"
date: 2019-09-01T22:17:30+08:00
lastmod: 2019-09-01T22:17:30+08:00
draft: true
keywords: []
description: ""
tags: [PHP,swoole,性能优化]
categories: [PHP]
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
本文以一个Excel表单导出功能的演化路线为示例，分享导出任务的通用模式、Reactor和Worker分离的思路等经验。
<!--more-->

&emsp;&emsp;本文以一个Excel表单导出功能的演化路线为示例，分享导出任务的通用模式、Reactor和Worker分离的思路等经验。
> 演示代码请移步Github:[PHP-export-optimize](https://github.com/panxl6/blog/tree/master/PHP-export-optimize)

## 版本1：快速上线
&emsp;&emsp;为了快速上线，代码写的比较粗糙，一般导出2000多条记录的表格，应用就已经爆掉了（执行时间超时或者内存超限）。
&emsp;&emsp;简单的通过分配更多的资源，就可以临时的满足要求。比如，增大PHP请求的允许占用内存等。但是，随着业务的快速发展，这种简单粗暴的方法很快就不能满足需求了。
&emsp;&emsp;在给出的示例中，导出10000条记录，内存就已经不够用了。
> *Allowed memory size of 134217728 bytes exhausted (tried to allocate 147456 bytes)*

&emsp;&emsp;当然了，这个极限值，也是根据机器的性能，SQL执行的情况等因素而定的。一般情况不超过10000条。

##  版本2：精耕细作
&emsp;&emsp;优化，无非就是找冗余：执行时间的冗余，存储空间的冗余。
&emsp;&emsp;通过在各个环节打桩记录，比较执行时间和内存占用，找出瓶颈，然后设计相应的方案。


 &emsp;**优化SQL**

 - 列表内容
 - 去除不必要的字段;
 - 采用分页的形式，削峰;


&emsp; **优化文件操作**
 
 - 索引优化;
 - 改用资源占用更少的文件格式(csv);
 - 文件读写时，使用缓冲的方式;

&emsp;**语法层面**

- 减少不必要的临时变量的使用;
- 关注垃圾回收的情况;

此时，最大导出记录数可以达到上万的级别。

##  版本3：异步执行
&emsp;&emsp;个人认为，web请求的执行不应该超过两分钟，因为web请求应该是快速响应的，同步的。相对耗时的任务，应该交给守护进程或定时任务来完成。在SQL很复杂、很耗时的场景下，同步执行再怎么优化也会达到比较明显的上限。，就应该考虑使用异步的方式。比如消息队列或RPC调用。
&emsp;&emsp;本文结合swoole进行讲解。无论是使用swoole实现RPC调用，抑或是通过消息队列进行解耦。执行查询任务的worker始终是需要设为守护进程的。而前端也存在一个缺陷，那就是需要不停的轮询后台来获取任务执行进度。

##  版本4：主动反馈
&emsp;&emsp;通过前面的改造，我们已经大大提升了记录导出条数的上线。但是我们又遇到一个新的问题。轮询的任务如果太多的话，会给数据库带来较大的压力。如果不去查询任务执行情况，用户又无法及时获得任务的执行状态。对于一些实时性要求比较高的任务，比如导出的是财务有关的数据，用户希望及时看到执行结果。
&emsp;&emsp;此时，我们可以通过在前端使用websoket与后台保持长连接，当导出任务执行完成时（进度条进入下一个环节时），通过消息队列或其他方式，将执行结果广播给用户。

参考文献：
1. [Thinkphp5文档官方](https://www.kancloud.cn/manual/thinkphp5_1/)
2. [swoole官方文档](https://www.swoole.com/)
