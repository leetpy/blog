---
title: Leetcode 19
date: 2025-12-30T17:07:57+08:00
lastmod: 2025-12-30T17:07:57+08:00
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

[https://leetcode.cn/problems/remove-nth-node-from-end-of-list/description/]()

# 解题

- 题目有明确链表不为空
- n 在链表长度内

## 计算长度

通过循环一次获取链表长度，计算出要删除的是第几个元素，
然后再次循环删除即可。

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
	sz := 0
	for p := head; p != nil; p = p.Next {
		sz++
	}

	dummyHead := &ListNode{Next: head}
	tmp := dummyHead
	for range sz - n { // 从零开始计算，需要移动的步数，tmp.Next 是需要删除的元素
		tmp = tmp.Next
	}
	tmp.Next = tmp.Next.Next

	return dummyHead.Next
}
```
