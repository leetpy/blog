---
title: Leetcode 2110
date: 2025-12-15T18:58:10+08:00
lastmod: 2025-12-15T18:58:10+08:00
author: 云弋
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: /blog/img/leetcode.webp
# covercaption: a description of the cover image
# images:
#   - /img/cover.jpg
categories:
  - 算法
tags:
  - leetcode
# nolastmod: true
# math: true
draft: false
---


<!--more-->

# 题目

[https://leetcode.cn/problems/number-of-smooth-descent-periods-of-a-stock/description/?envType=daily-question&envId=2025-12-15]()

# 思路

按照联系下降划分数组，每个部分有 c(n, 2) + n 种组合方式

```go
func getDescentPeriods(prices []int) int64 {
	var ans int64 = 0
	count := 1
	for i := range len(prices) - 1 {
		if prices[i]-prices[i+1] == 1 {
			count++
		} else {
			ans += factorialIterative(int64(count))
			count = 1
		}
	}

	ans += factorialIterative(int64(count))

	return ans
}

func factorialIterative(n int64) int64 {
	var result int64 = 0
	if n >= 2 {
		// c(n, 2)
		result += n * (n - 1) / 2
	}

	return result + n
}
```