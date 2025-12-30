---
title: 背包问题
date: 2025-12-16T19:59:41+08:00
lastmod: 2025-12-16T19:59:41+08:00
author: 云弋
# avatar: /img/author.jpg
# authorlink: https://author.site
# cover: /img/cover.jpg
# covercaption: a description of the cover image
# images:
#   - /img/cover.jpg
categories:
  - 算法
tags:
  - 背包
# nolastmod: true
# math: true
draft: false
---

# 背包分类

- `01 背包` : n 种物品，每种物品只有一个
- `完全背包` : n 种物品，每种物品无限个
- `多重背包` : n 种物品，每种物品个数不同

<!--more-->

## 01 背包

n 个物品，每个物品有重量和价值，背包最大重量为 m， 可以装的最大价值

### 暴力解法

每个物品只有取和不取两种状态，时间复杂度 O(2^n)

### dp 方式

定义 `dp[i][j]` 为下标为 [0, i] 之间的物品，任取，放到容量为 j 的背包里的最大价值

- dp[i][j] 当不放物品 i 时， 取值是dp[i-1][j]
- dp[i][j] 当放物品 i 时， 取值是dp[i-1][j-wi] + value[i]

所以有递推公式： dp[i][j] = max(dp[i-1][j], dp[i-1][j-wi] + value[i])


