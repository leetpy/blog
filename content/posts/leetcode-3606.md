---
title: Leetcode 3606
date: 2025-12-13T15:37:22+08:00
lastmod: 2025-12-13T15:37:22+08:00
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

[https://leetcode.cn/problems/coupon-code-validator/description/?envType=daily-question&envId=2025-12-13]()

判断条件排序即可，需要不要多个数组排序

```go
import (
	"regexp"
	"slices"
	"sort"
)

func validateCoupons(code []string, businessLine []string, isActive []bool) []string {
	valid := make([][]string, 0)

	validBusiness := []string{"electronics", "grocery", "pharmacy", "restaurant"}

	pattern, _ := regexp.Compile(`^[a-zA-Z0-9_]+$`)
	for i := range len(code) {
		if !isActive[i] {
			continue
		}

		if !slices.Contains(validBusiness, businessLine[i]) {
			continue
		}

		matched := pattern.MatchString(code[i])
		if matched {
			valid = append(valid, []string{code[i], businessLine[i]})
		}
	}

	sort.Slice(valid, func(i, j int) bool {
		if valid[i][1] != valid[j][1] {
			return valid[i][1] < valid[j][1]
		}

		return valid[i][0] < valid[j][0]
	})

	ans := make([]string, len(valid))
	for idx, i := range valid {
		ans[idx] = i[0]
	}
	return ans
}

```