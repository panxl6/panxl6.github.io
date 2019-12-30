---
title: "码分复用"
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
关于代码复杂性的一些观点
<!--more-->

&emsp;&emsp;重载是某些现代编程语言的一个重要特性。
&emsp;&emsp;在c++里面，重载指的是函数由于具有不同的签名可以拥有不同的实现。
&emsp;&emsp;从直观的角度来讲，就是同一个符号有着不同的含义。

&emsp;&emsp;我们回忆一下，通信原理中的一些类似的概念，码分复用、波分复用、频分复用。
&emsp;&emsp;在这里，我就用码分复用来作为重载的隐喻。
&emsp;&emsp;个人认为，码分复用现象是导致编程复杂、代码难以阅读的一个原因。一次只做一件事的原则并没有被遵守的时候，不同用途的代码在概念上的重叠就会导致理解上的困惑。

### 操作符“重载”
&emsp;&emsp;其实C语言里面也有重载的概念。*号既可以表示两个数相乘，又可以表示取某个地址的值。&既可以表示位运算中的与运算，又可以表示取地址操作。
&emsp;&emsp;当一个维度无法进行表达的时候，我们会想到增加一个维度。
&emsp;&emsp;比如我们常见的转义字符。\表示执行转义。\\\才表示反斜杠。那么\这个字符就是具有条件意义的。在什么条件下具有什么意义。
&emsp;&emsp;举个例子，为字符串数组[abc', 'efgh'', 'ij', 'aaabbbcccddd']设计一个序列化和反序列化的方案。
&emsp;&emsp;比如说，我们用逗号`，`分隔不同的变量。用冒号分隔变量名和变量的值。问题来了，当变量的值是逗号的时候怎么表示？这就需要转义了（ANSI编码就是这个思路）。这就是二维规则。码分复用出现冲突的时候，需要扩张维度来描述事物。
&emsp;&emsp;如果我们规定，不允许使用逗号，因为逗号是保留字。那也就解决了这个问题了（编程语言中不允许使用保留字也是这个思路）。

###  标识符重载
&emsp;&emsp;在C语言中，异常结果通常是用一个int数值作为状态码的。即便是我们经典的数据结构书籍，在讲述链表的时候也是这么用的。

```c
int find(int arr) {
	return 0; // 表示没找到

	return position; // 找到了

	return -1; // 表示其他异常
}
```

&emsp;&emsp;这样一来就出现了码分复用的现象了。
&emsp;&emsp;比较容易理解的方案如下：

```c
void find(int arr) {

	return [result, statusCode]; // 当然了，C语言并不能返回多个值，这里只是举个例子。可以用python实现这一点

}
```


### 变量覆盖

```php
$a = 1; // 用户输入

$b = $a; //变量$a的使命已经完成了

$a = 3; // 又一次用$a变量这个容器去承载另一件事的数据
```

&emsp;&emsp;又比如，C语言中，数字0既可以表示空，又可以表示false。往往还会被程序员自定义为执行失败的异常码。
&emsp;&emsp;同一个变量在生命周期的不同阶段代表的多重含义，我们先把它定义为“变量的时分复用”。这借鉴了通信原理的概念。
&emsp;&emsp;这一现象可能会导致一些问题。至少会降低可读性，并由此带来语义上的困惑。
&emsp;&emsp;比如，在C语言中数值0可以ASCII码中的一个符号。它也可以表示false，条件为假。
&emsp;&emsp;这样的变量往往是充当临时变量的。

### 概念升维
&emsp;&emsp;在C语言家族中，错误码是很常见的一个概念。
&emsp;&emsp;在java中，遇到问题就直接抛出一个异常。就只有一个概念。
&emsp;&emsp;在C语言编程中，对于简单的问题，返回`true`代表执行成功，返回`false`表示执行失败。但是，当我们需要更细的粒度是，`true`和`false`就不够用了，毕竟一位二进制只能表示两种可能。那么我们就扩充一下，返回一个十进制的`errno`变量吧。这样一来就发生了概念的维度上升。
&emsp;&emsp;原来的`true`或者`false`仅仅表示系统错误(概念上对应于Java中的Error异常，不可预测的错误)，比如：mysql读取失败、redis读取失败、网络异常；errCode表示业务错误(对应于Java中的Exception，可以预测的错误)，比如：参数非法，输入不符合限制。

```php
// 版本1
// 此时，$ret变量既表示返回结果的内容，又表示执行状况
function getUserInfo()
{
	$ret = $redis->hget('hashName', $uid);
	// 系统错误
	if (!$ret)
		return false;

	// 业务逻辑错误
	if (empty($ret['uid']))
		return false;
		
	return true;
}

// 版本2
class Test
{
	protected $errCode = 0;
	function getUserInfo(&$data)
	{
		$ret = $redis->hget('hashName', $uid);

		// 系统错误(redis读取异常)
		if ($ret === false) {
			return false;
		}
		
		// 业务逻辑错误(缺少必要字段)
		if (empty($ret['uid'])) {
			$this->errCode = 10001;
			return true;
		}
		
		$data = $ret;
		return true;
	}
}
```


## 状态码歧义

```php
function demo()
{
	$userInput = $_GET['user_input'];
	
	if (empty($userInput)) {
		echo "用户投了否定票";
		return false;
	}

	echo "用户投了支持票";
	
	return true;
}
```

在应用中，我们一般使用枚举值来表示业务的状态。
从上面的例子来看，会产生歧义。
我们以0或者说空，作为否定票的标记，以非空的值作为支持票的定义。
但是，如果前端把参数传丢了，我们就默认为用户投了否定票。
而实际上是前端有bug。


```php
function demo()
{
	$userInput = $_GET['user_input'];
	
	if ($userInput == 1) {
		echo "用户投了否定票";
		return false;
	}

	if ($userInput == 2) {
		echo "用户投了支持票";
		return true;
	}
	
	echo "未定义的参数";	
	
	return false;
}
```