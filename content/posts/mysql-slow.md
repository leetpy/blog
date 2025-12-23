---
title: MySQL 慢查询优化
date: 2025-12-11T17:12:06+08:00
lastmod: 2025-12-11T17:12:06+08:00
author: 云弋
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: /blog/img/mysql.webp
# covercaption: a description of the cover image
# images:
#   - /img/cover.jpg
categories:
  - database
tags:
  - mysql
# nolastmod: true
# math: true
draft: false
---

<!--more-->

## 查看

```sql
-- 查看是否开启了慢查询日志
SHOW VARIABLES LIKE 'slow_query_log';

-- 查看慢查询日志文件位置
SHOW VARIABLES LIKE 'slow_query_log_file';

-- 查看慢查询阈值（即多“慢”才算慢查询）
SHOW VARIABLES LIKE 'long_query_time';
```

## 配置

```sql
-- 临时开启（重启后失效）
SET GLOBAL slow_query_log = 'ON';
```

### 永久开启

编辑 MySQL 配置文件（通常是 /etc/my.cnf 或 /etc/mysql/my.cnf），在 [mysqld] 段添加或修改：

```toml
[mysqld]
slow_query_log = 1
slow_query_log_file = /var/log/mysql/mysql-slow.log
long_query_time = 1
log_queries_not_using_indexes = 1  # 可选：记录未使用索引的查询
```

然后重启服务

```bash
systemctl restart mysql
```

## 分析

```bash
# 直接看日志
tail -f /var/log/mysql/mysql-slow.log

# 使用 mysqldumpslow 工具
# 查看最慢的10条SQL
mysqldumpslow -s t -t 10 /var/log/mysql/mysql-slow.log

# 按照出现次数排序
mysqldumpslow -s c -t 10 /var/log/mysql/mysql-slow.log
```

## 场景

1. 检查是否走了索引，如果没有则优化 SQL 利用索引
