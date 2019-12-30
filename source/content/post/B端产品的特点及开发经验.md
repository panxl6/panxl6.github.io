---
title: "B端产品的特点及开发经验"
date: 2019-11-28T20:55:26+08:00
lastmod: 2019-11-28T20:55:26+08:00
draft: true
keywords: []
description: ""
tags: [B端,企业服务]
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
To B or not To B, that is a question. 企业服务跟消费服务应用的开发有哪些差别呢？
<!--more-->

# 租户隔离
B端的产品，是以企业为单位的。所有的数据和服务都按某个机构实体进行聚合。那么我们如何提供一个沙箱机制？
理论上，我们向客户提供的是无差别的服务。但是由于数据库实例、服务器性能受到任务繁重程度不同，对资源的占用也不同。这样就会造成同样付费，但是某些客户耗费的资源较多。
我们来看看docker。一个镜像一个应用。这样一来就把进程间的资源进行了隔离。但是有些东西是无法隔离的，比如系统时间。
数据的隔离，一个租户一个数据库可以有。一个租户一台服务器？一个租户一个Redis实例？这个时候，就需要按租户号进行隔离。每个租户号是一个命名空间。通过租户编号对资源包进行垂直分隔。


# 部署实施
有些客户，对服务提供商的安全性并不信任，或者希望对配置进行自由的升降配等原因，他们希望进行私有化部署。数据库、服务器、基础设置都是客户私有的。功能发布，线上问题的定位都会变得更加复杂。如果客户只是把某个产品私有化部署，其他产品在服务提供商的公有云部署，那么混合云部署的复杂程度会进一步提高。
私有化部署后，服务提供商可以运营的资源池就减少了。同时代运维是一个困难的事情。出现线上问题时，服务提供商较难响应。客户往往是提供远程桌面、VPN、子账号等临时性授权访问给运维人员。


# 产品意见领袖
C端产品人人都是产品经理。但是B端产品是由企业付费的。企业的管理人员对产品功能有着更大的话语权。此时并没有什么产品驱动、技术驱动，只有甲方驱动。


# 定制化
企业的业务在不断的发展，作为支撑工具，B端产品也必须提供更多相应的功能。云端软件，通过一个链接，大家用的都是同一套软件。如果不停的往其中堆功能，产品就会很臃肿。有些功能就只有某些客户才会用到。这样一来，就需要基于一个公共的基准版本进行差异化定制。
1） 功能灰度
我们把每个特性都做成颗粒度很小的功能点，客户需要那些扩展点，自己可以通过开关组合出比较适合自己的产品形态。但是灰度开关太多，学习成本、运营成本也会提升。
2）pass平台
灰度开关并不特别的灵活。只能是要或者不要。通过提供一些可视化编辑工具（像PPT），或者提供一些辅助编程接口（office的vb），用户或者运营人员就可使用这些低门槛的方式来定制产品。
3）二次开发
对于改动到底层业务模型的需求，在基本版本上派生一个新的分支进行开发。


# B端用户的一些特点
大众消费软件，尤其是一些国民软件，基本上达到了用户傻瓜式操作。但是B端产品一般是生产力工具，往往需要提供详细的使用文档、教程，需要一些专业的培训，甚至是资格认证。


# 领域知识驱动
相较于C端应用，B端产品的变更没那么频繁。对于可维护性的要求更高。同时，业务知识的深度和广度要求也更高一些。
