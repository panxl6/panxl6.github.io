---
title: "代码重定向"
date: 2019-09-01T22:17:30+08:00
lastmod: 2019-09-01T22:17:30+08:00
draft: true
keywords: []
description: ""
tags: [维护]
categories: []
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
代码迁移和维护的一些技巧。
<!--more-->


&emsp;&emsp;我们平常说的重定向，是指网址重定向。
&emsp;&emsp;比如，当年的京东网址是[360buy.com](360buy.com)，后来升级成了[jd.com](jd.com)。不知道的用户肯定还记着旧的网址，那怎么办呢？我们就把访问[360buy.com](360buy.com)的用户重定向到[jd.com](jd.com)。
&emsp;&emsp;在代码维护的过程中，我们可能也会遇到给运行中汽车换轮胎这样棘手的问题。
&emsp;&emsp;随着功能的迭代，可能版本1的代码和版本2的代码有了较大的差异，版本1的代码已经有其他程序员在用了，但是版本2的代码又必须上。把版本1的代码打包服务化吧，或者我们推倒重构吧。这样的方式代价有点大。在这个过渡阶段，我们就可以借鉴网址重定向的思路。

### 类的层面
&emsp;&emsp;项目初期，很多代码都是放在一个目录下的（单体应用）。随着业务的发展，某些基础代码可能要做为服务化做准备了；某些业务可能要分家了。
  ![这里写图片描述](https://img-blog.csdn.net/20180814084136890?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BhbnhsNg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
&emsp;&emsp;这里以一个电商的项目为例：
&emsp;&emsp;当业务还小的时候订单系统就一个文件。但是当业务规模变大，里面的每个文件都变得非常大，可能有5000行以上的代码。这个时候就需要重构或者拆分代码了。
![这里写图片描述](https://img-blog.csdn.net/20180814085404721?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BhbnhsNg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
&emsp;&emsp;让原有的Home\DealLogic继承Deal\DealLogic,Home\DealLogic变成了一个空文件，并没有实际的代码。所有的逻辑都迁移到了Deal目录下。通过类的继承，把所有对Home\DealLogic的调用都路由到了Deal\DealLogic。新旧代码之间依然有交互的地方，就让Deal\DealLogic分发到Deal下面的逻辑。
```
	// Home目录下的源文件
	class HomeDeal{
		function  A
		function B
		...
	}

	class DealDealLogic {
		function A
		function B
		...
	}
	
	// 重定向之后
	class HomeDealLogic extends DealDealLogic {
		// nothing
	}
	
```

### 函数的层面
&emsp;&emsp;某个函数版本1和版本2的增量改动比较大，维护的时候怎么办呢？
&emsp;&emsp;起两个名字吧，`function v1`以及`function v2`。v1继续用，新的业务就用v2嘛。但是如果v2跟v1是向下兼容的呢，而且v2里面有很多代码跟v1是兼容的，那岂不是有很多的冗余代码?
```
function v1 {
	// 实现功能1
}

function v2 {
	// 实现功能1
	// 实现功能2
}
```

&emsp;&emsp;如果你的函数是一个个参数进行传递的，那你可能要增加一个路由参数version。如果你传递的参数是一个对象那就在对象里面增加一个属性，上层改动不大，向下兼容。
```
function v1(version) {
	// 实现功能1
	if (version == 2) {
		// 实现功能2
	}
}
```
&emsp;&emsp;但是随着业务的迭代，增量功能的差异非常大的时候，想要剥离出去呢？
```
function v1(version) {
	// 实现功能1
	if (version == 2) {
		// 路由到v2函数
		return v2()
	}
}

function v1(version) {
	// 实现功能2
}
```
再比如linux系统的文件API中的open函数和creat函数的关系:
```c
int creat(const char *name, int mode)
{
    return open(name, O_WRONLY|O_CREAT|O_TRUNC, mode);return open(name, O_WRONLY|O_CREAT|O_TRUNC, mode);
}
```
	

### 变量的层面
&emsp;&emsp;有时候，我们可能把某个变量的名称拼写错了;或者变量太多了，零零散散，想把它封装成对象;又或者我们想通过getter增加其他的转换或功能等等。但是原本的变量名称已经被很多其他同事在使用了，怎么办呢？
&emsp;&emsp;在C++里面有个取别名的功能，新的名字跟旧的名字指向同一个引用。
  