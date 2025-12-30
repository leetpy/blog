---
title: Leetcode 206
date: 2025-12-30T16:45:17+08:00
lastmod: 2025-12-30T16:45:17+08:00
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

[https://leetcode.cn/problems/reverse-linked-list/description/]()

```go
func reverseList(head *ListNode) *ListNode {
	var pre *ListNode

	for head != nil {
		next := head.Next // 保存下一个节点
		head.Next = pre   // 断开当前节点的原始指针，指向前一个节点
		pre = head        // 更新前置节点
		head = next       // 更新当前节点
	}

	// 最后 head 一定是 nil， 这里返回 head 的前一个节点
	return pre
}
```
