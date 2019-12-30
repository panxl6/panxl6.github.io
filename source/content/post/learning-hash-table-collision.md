---
title: "教你从零开始写一个哈希表--哈希冲突"
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
哈希函数把一个无穷大的输入集合映射到一个有限大小的输出集合。
<!--more-->

&emsp;&emsp;哈希函数把一个无穷大的输入集合映射到一个有限大小的输出集合。不同的关键字可能会被映射到同一个数组下标，如此一来就导致了哈希冲突。哈希表必须实现解决冲突的方法。
&emsp;&emsp;我们的哈希表将使用开放地址法和再散列法。在桶索引冲突后，再散列法会使用两个哈希函数来计算键值对将要保存的桶索引值。
&emsp;&emsp;有关其他哈希冲突的解决方法，请查看[附录](https://blog.csdn.net/panxl6/article/details/84995871)。

### 再散列法
&emsp;&emsp;索引`i`冲突后应该使用的新的索引位置根据下面的函数给出：
&emsp;&emsp;`index = hash_a(string) + i * hash_b(string) % num_buckets`
&emsp;&emsp;我们看到，如果没有哈希冲突的发生，i=0。此时，桶索引是字符串的`hash_a`值。如果发生了冲突，索引将由`hash_b`进行调整。
&emsp;&emsp;有种可能是，`hash_b`返回了0，导致`i*hash_b(string)%num_buckets`也返回了0。这会使得哈希表不停的向同一个桶中插入键值对。我们可以通过对第二个哈希的结果加一来避免这种情况，以确保第二个项的结果不会是0。
`index = (hash_a(string) + i * (hash_b(string) + 1)) % num_buckets`

*具体实现*
```c
// hash_table.c
static int ht_get_hash(
    const char* s, const int num_buckets, const int attempt
) {
    const int hash_a = ht_hash(s, HT_PRIME_1, num_buckets);
    const int hash_b = ht_hash(s, HT_PRIME_2, num_buckets);
    return (hash_a + (attempt * (hash_b + 1))) % num_buckets;
}
```

*上一篇:*[教你从零开始写一个哈希表--哈希函数](https://blog.csdn.net/panxl6/article/details/84981842)
*下一篇:*[教你从零开始写一个哈希表--接口](https://blog.csdn.net/panxl6/article/details/84995857)
