---
title: Redis
date: 2023-12-11T14:45:48+08:00
lastmod: 2025-12-11T14:45:48+08:00
author: 云弋
# avatar: /img/author.jpg
# authorlink: https://author.site
# covercaption: a description of the cover image
# images:
#   - /img/cover.jpg
categories:
  - redis
tags:
  - redis

# nolastmod: true
# math: true
draft: false
---

<!--more-->

# 基本数据类型

- hash:
- list:
- set:
- zset:
- string:
- stream
- pub/sub
- bitmap
- hyperLogLog
- geospatial

## string

string 是动态字符串，类似数组，采用预分配和冗余分配技术。最大 512M。

### 常用操作

```bash
# 设置 key
> set name zhangsan
OK

# 判断key 是否存在
> exists name
1

# 批量操作可以节省网络开销
# 批量设置
> mset age 18 sex male
OK

# 批量获取
> mget age sex
1) 18
2) "male"

# 如果是整型，可以进行自增操作
# +1
> incr age
19

# +n
> incrby age 5
24

# -n
> incrby age -4
20

# 整数是 signed long 类型，超过最大值会报错
# 2^63 -1
> SET codehole 9223372036854775807
"OK"

> INCR codehole
(Redis) ERR increment or decrement would overflow


# incr 一个不存在的 key 会从 0 开始计算
> incr nokey
1
```

## list

redis List 底层采用链表数据结构实现，所以插入和删除都比较快，但是查询慢；

既然是链表，就可以把它当队列或者栈使用。

### 队列

右边进，左边出

```bash
# 入队列
> rpush books python java golang
(integer) 3

# 获取长度
> llen books
(integer) 3

# 出队列
> lpop books
"python"
> lpop books
"java"
> lpop books
"golang"
> lpop books
(nil)
```

### 栈

右边进，右边出

```bash
# 入栈
> rpush books python java golang
(integer) 3

# 出栈
> rpop books
"golang"
> rpop books
"java"
> rpop books
"python"
> rpop books
(ni)
```
