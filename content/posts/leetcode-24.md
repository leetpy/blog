---
title: Leetcode 24
date: 2025-12-31T15:18:17+08:00
lastmod: 2025-12-31T15:18:17+08:00
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
  - 链表
# nolastmod: true
# math: true
draft: false
---

<!--more-->

# 题目

[https://leetcode.cn/problems/swap-nodes-in-pairs/description/]()

# 解题

直接两两交换即可。几个容易出错的点：

1. 空指针处理；
2. 一组交换之后，需要把上一组的最后一个节点指向交换后的第一个节点

```go
func swapPairs(head *ListNode) *ListNode {
	dummyHead := &ListNode{Next: head}

	temp := dummyHead
	for temp.Next != nil && temp.Next.Next != nil {
		p1, p2 := temp.Next, temp.Next.Next

		// 交换
		p2.Next, p1.Next = p1, p2.Next

		// temp 是前置节点，指向交换后的
		temp.Next = p2

		// 移动到下一组
		temp = p1
	}

	return dummyHead.Next
}
```
