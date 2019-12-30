---
title: "教你从零开始写一个哈希表--哈希函数"
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
高概率的冲突，会使哈希表的性能急剧下降。
<!--more-->

&emsp;&emsp;
在这一节，我们来编写哈希函数。
我们选择的哈希函数应该具有（以下特性）：
- 把字符串作为输入，返回0到m（我们设计的桶数组的长度）的数字；
- 对于一组平均的输入返回分布比较均匀的桶索引。如果我们的哈希函数不是均匀分布的，它可将会把较多的一些键值对放在某几个桶中。这将会导致更高的冲突概率。冲突降低了哈希表的效率。

我们使用的是常见的字符串哈希函数，伪代码的表达如下：
```
function hash(string, a, num_buckets):
    hash = 0
    string_len = length(string)
    for i = 0, 1, ..., string_len:
        hash += (a ** (string_len - (i+1))) * char_code(string[i])
    hash = hash % num_buckets
    return hash
```
   
这个哈希函数有两个步骤：
- 把字符串转换成一个较大的整数；
- 通过取这个数的余数跟m取模来将整数的大小减小到固定的范围。
变量a得是一个比字符表大小要大的素数。我们散列的ASCII字符串，有128个字符。因此我们应该选择一个比128要大的素数。
`char_code`是一个返回一个表示字符的对应整数值的函数。我们用的是ASCII字码表。

一起来试一下这个哈希函数吧：

```
hash("cat", 151, 53)

hash = (151**2 * 99 + 151**1 * 97 + 151**0 * 116) % 53
hash = (2257299 + 14647 + 116) % 53
hash = (2272062) % 53
hash = 5
```
调整变量a的值，将会得到不同的哈希函数。

hash("cat", 163, 53) = 3
```c
// hash_table.c
static int ht_hash(const char* s, const int a, const int m) {
    long hash = 0;
    const int len_s = strlen(s);
    for (int i = 0; i < len_s; i++) {
        hash += (long)pow(a, len_s - (i+1)) * s[i];
        hash = hash % m;
    }
    return (int)hash;
}
```

### 病态数据
&emsp;&emsp;理想的哈希函数往往会产生均匀的分布。然而，所有的哈希函数都存在病态的输入集。这些输入都将哈希到同一个值中。为了找出这一类输入，我们用输入值域中的一个大集合的输入来运行这个函数。病态的数据集会哈希到一个特定的桶中。
&emsp;&emsp;病态输入集的存在意味着世间没有对所有输入都完美的的哈希函数。我们能做的是对于预期输入集设计一个表现良好的哈希函数。
&emsp;&emsp;病态输入也导致了一个安全问题。如果一个用户恶意地向哈希表写入一堆冲突的关键字，那么搜索这些关键字会花费超过`(O(n))` 复杂度的时间，而不是`(O(1))`。这可能会被不怀好意的人用来针对那些使用了哈希表的系统，进行拒绝服务攻击，例如DNS和某些web服务。

*上一篇:*[教你从零开始写一个哈希表--哈希表结构](https://blog.csdn.net/panxl6/article/details/84981059)
*下一篇:*[教你从零开始写一个哈希表--哈希冲突](https://blog.csdn.net/panxl6/article/details/84995844)
