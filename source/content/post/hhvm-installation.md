---
title: "ubuntu下安装HHVM"
date: 2019-09-01T22:17:30+08:00
lastmod: 2019-09-01T22:17:30+08:00
draft: true
keywords: []
description: ""
tags: [ubuntu,HHVM,PHP7]
categories: [ubuntu]
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
为了更深入的理解PHP7，安装HHVM并学一下Hack语言。
<!--more-->


由于HHVM的ubuntu源无法直接访问（被墙），这里有两个国内的源：
hiphop-php.com的源比较新，目前是3.22的版本。

```bash
wget http://dl.hiphop-php.com/conf/hhvm.gpg.key | sudo apt-key add hhvm.gpg.key
echo "deb http://dl.hiphop-php.com/ubuntu $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/hhvm.list
sudo apt-get update
sudo apt-get install -y hhvm
```

还有一个是清华大学的镜像源：
不过是目前是3.18版本的。
```bash
wget https://mirrors.tuna.tsinghua.edu.cn/HHVM/conf/hhvm.gpg.key | sudo apt-key add hhvm.gpg.key
echo "deb https://mirrors.tuna.tsinghua.edu.cn/HHVM/ubuntu $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/hhvm.list
sudo apt-get update
sudo apt-get install -y hhvm
```

![安装完成](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcxMjI3MTUwNjMxMTk1?x-oss-process=image/format,png)

>注意：$(lsb_release -sc)表示ubuntu的版本代号。如果你使用的不是ubuntu官方版本，而是其他的衍生版本，比如linux mint、elementary os，请按照对应的版本进行修改。

By the way, 不提倡从源码安装。第三方依赖也下载下来的话接近２G。i5、８Ｇ内存、256G固态硬盘的配置，开了make -j8也要５个多小时才能编译完。