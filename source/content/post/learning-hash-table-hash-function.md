---
title: "教你从零开始写一个哈希表--调整大小"
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
在这一节，我们来编写哈希函数。
<!--more-->

&emsp;&emsp;此时，哈希表已经有一定数量的桶了。随着越来越多的键值对插入，哈希表将会满负荷。这个问题很大，主要从两方面考虑：
1. 高概率的冲突下，哈希表的性能急剧下降；
2. 当前的哈希表只能存储固定数量的键值对。如果用户尝试着去存储的需求超过这个数值时，插入函数会执行失败。

&emsp;&emsp;为了解决这个问题，我们可以在桶数组将要满的时候增加数组的大小，然后用哈希表的`count`属性来记录存储的键值对总数。每次执行插入和删除时，计算表的负载，或者说使用率。如果这个指标高于或者低于一个固定的值，我们就调整桶的大小。

&emsp;&emsp;判断桶数组调整的阈值：
- 如果负载>0.7，我们就扩大数组；
- 如果负载<0.1，我们就减小数组。

&emsp;&emsp;调整哈希表的大小，我们简单的创建一个当前哈希表一半大小或者两倍大小的新的哈希表，然后把所有未被删除的键值对插入到其中。
&emsp;&emsp;新数组大小应该是与当前大小的一半或两倍最接近（小于或大于）的素数。计算新数组的大小并不简单。哈希表得保存一个基准的大小--用户预期的数组大小。然后把第一个比基准大小要大的素数设定为实际大小。扩大数组时，我们把这个基准大小翻倍，然后找出第一个比翻倍后的基准要大的第一个素数。将数组大小调小，我们将数组的大小减半，然后找到比当前数组的大小减半后大的第一个素数。

&emsp;&emsp;此处设定基准值为50。

&emsp;&emsp;我们不预先保存（素数值），而是通过检查每一个符合的要求数是否为素数的暴力搜索方法来查找邻近的素数。虽然暴力求解法听起来就有问题，但是实际上我们要找的数很小，而这个过程花的时间比重新计算哈希表中键值对的哈希值的时间更有价值。
&emsp;&emsp;先定义一个查找邻近素数的函数，然后在`prime.h`以及`prime.c`两个文件中去实现这个函数：

```c
// prime.h
int is_prime(const int x);
int next_prime(int x);
// prime.c

#include <math.h>

#include "prime.h"


/*
 * Return whether x is prime or not
 *
 * Returns:
 *   1  - prime
 *   0  - not prime
 *   -1 - undefined (i.e. x < 2)
 */
int is_prime(const int x) {
    if (x < 2) { return -1; }
    if (x < 4) { return 1; }
    if ((x % 2) == 0) { return 0; }
    for (int i = 3; i <= floor(sqrt((double) x)); i += 2) {
        if ((x % i) == 0) {
            return 0;
        }
    }
    return 1;
}


/*
 * Return the next prime after x, or x if x is prime
 */
int next_prime(int x) {
    while (is_prime(x) != 1) {
        x++;
    }
    return x;
}
```

&emsp;&emsp;接下来，`ht_new`函数需要升级以支持创建一个有固定大小的哈希表。新建一个函数，`ht_new_sized`，然后修改`ht_new`函数，让它带着一个默认的起始大小调用`ht_new_sized`函数。

```c
// hash_table.c
static ht_hash_table* ht_new_sized(const int base_size) {
    ht_hash_table* ht = xmalloc(sizeof(ht_hash_table));
    ht->base_size = base_size;

    ht->size = next_prime(ht->base_size);

    ht->count = 0;
    ht->items = xcalloc((size_t)ht->size, sizeof(ht_item*));
    return ht;
}


ht_hash_table* ht_new() {
    return ht_new_sized(HT_INITIAL_BASE_SIZE);
}
```

&emsp;&emsp;现在，哈希表大小调整函数已经功能完备了。
&emsp;&emsp;在大小调节函数中，我们确保减小哈希表大小时，不会把大小调的比它预设的最小值还小。我们初始化了预期大小的新的哈希表，并将所有的非空或者已被删除的键值对都被插入到新的哈希表中。在删除旧表之前，我们将旧表的属性值迁移到新的哈希表。

```c
// hash_table.c
static void ht_resize(ht_hash_table* ht, const int base_size) {
    if (base_size < HT_INITIAL_BASE_SIZE) {
        return;
    }
    ht_hash_table* new_ht = ht_new_sized(base_size);
    for (int i = 0; i < ht->size; i++) {
        ht_item* item = ht->items[i];
        if (item != NULL && item != &HT_DELETED_ITEM) {
            ht_insert(new_ht, item->key, item->value);
        }
    }

    ht->base_size = new_ht->base_size;
    ht->count = new_ht->count;

    // To delete new_ht, we give it ht's size and items 
    const int tmp_size = ht->size;
    ht->size = new_ht->size;
    new_ht->size = tmp_size;

    ht_item** tmp_items = ht->items;
    ht->items = new_ht->items;
    new_ht->items = tmp_items;

    ht_del_hash_table(new_ht);
}
```

&emsp;&emsp;为了简化大小调节，我们定义了两个小函数来调大和调小。

```c
// hash_table.c
static void ht_resize_up(ht_hash_table* ht) {
    const int new_size = ht->base_size * 2;
    ht_resize(ht, new_size);
}


static void ht_resize_down(ht_hash_table* ht) {
    const int new_size = ht->base_size / 2;
    ht_resize(ht, new_size);
}
```

&emsp;&emsp;为了执行大小调整（逻辑），我们要在插入和删除时检查哈希表的负载。如果高于或低于预设值0.7和0.1，我们就会相应的调小或调大哈希表。
&emsp;&emsp;为了避免浮点运算，我们把`count`属性乘以100，然后再检查它是否高于70或低于10。

```c
// hash_table.c
void ht_insert(ht_hash_table* ht, const char* key, const char* value) {
    const int load = ht->count * 100 / ht->size;
    if (load > 70) {
        ht_resize_up(ht);
    }
    // ...
}


void ht_delete(ht_hash_table* ht, const char* key) {
    const int load = ht->count * 100 / ht->size;
    if (load < 10) {
        ht_resize_down(ht);
    }
    // ...
}
```
*上一篇:*[教你从零开始写一个哈希表--接口](https://blog.csdn.net/panxl6/article/details/84995857)
*下一篇:*[教你从零开始写一个哈希表--附录](https://blog.csdn.net/panxl6/article/details/84995871)