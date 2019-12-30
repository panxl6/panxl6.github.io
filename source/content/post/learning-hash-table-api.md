---
title: "教你从零开始写一个哈希表--哈希表结构"
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
哈希函数将会实现以下的API：插入、查找、删除、更新。
<!--more-->

&emsp;&emsp;每一个键-值对（items）都会被保存在结构体中：
```c
// hash_table.h
typedef struct {
    char* key;
    char* value;
} ht_item;
```

&emsp;&emsp;哈希表保存了一组键值对的指针，以及哈希表大小的一些细节和它的负载：
```c
// hash_table.h
typedef struct {
    int size;
    int count;
    ht_item** items;
} ht_hash_table;
```

### 初始化和删除
&emsp;&emsp;这里需要为`ht_item`定义初始化函数。这个函数分配了`ht_item`大小的内存，同时在新的内存块中保存了字符串`k`和`v`的副本。此处把这个函数标记为`static`，是因为它只会被哈希表在内部调用一次。
```c
// hash_table.c
#include <stdlib.h>
#include <string.h>

#include "hash_table.h"

static ht_item* ht_new_item(const char* k, const char* v) {
    ht_item* i = malloc(sizeof(ht_item));
    i->key = strdup(k);
    i->value = strdup(v);
    return i;
}
```

&emsp;&emsp;`ht_new`函数初始化了一个新的哈希表。`size`变量定义了哈希表可以保存多少个键值对。此处定为53个，至于如何在后面程序中更改size，教程会在[《调整大小》](https://blog.csdn.net/panxl6/article/details/84995864)一节中详细介绍。我们用`calloc`函数初始化一组键值对。`calloc`这个函数用`NULL`对分配的内存进行填充。数组的首位存储是`NULL`意味着这个桶是空的。
```c
// hash_table.c
ht_hash_table* ht_new() {
    ht_hash_table* ht = malloc(sizeof(ht_hash_table));

    ht->size = 53;
    ht->count = 0;
    ht->items = calloc((size_t)ht->size, sizeof(ht_item*));
    return ht;
}
```

&emsp;&emsp;我们也需要函数来删除`ht_items`变量和`ht_hash+tables`结构。这将会释放已经分配的内存，不然就会导致内存泄漏。

```c
// hash_table.c
static void ht_del_item(ht_item* i) {
    free(i->key);
    free(i->value);
    free(i);
}


void ht_del_hash_table(ht_hash_table* ht) {
    for (int i = 0; i < ht->size; i++) {
        ht_item* item = ht->items[i];
        if (item != NULL) {
            ht_del_item(item);
        }
    }
    free(ht->items);
    free(ht);
}
```

&emsp;&emsp;哈希表的代码已经初具原型，它足以实现创建和销毁一个哈希表。尽管它此刻还做不了什么事情，但我们依然可以试着运行一下这份代码。

```c
// main.c
#include "hash_table.h"

int main() {
    ht_hash_table* ht = ht_new();
    ht_del_hash_table(ht);
}
```

*上一篇:*[教你从零开始写一个哈希表--导读](https://blog.csdn.net/panxl6/article/details/84964178)
*下一篇:*[教你从零开始写一个哈希表--哈希函数](https://blog.csdn.net/panxl6/article/details/84981842)