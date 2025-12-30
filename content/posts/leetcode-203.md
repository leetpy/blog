---
title: Leetcode 203
date: 2025-12-30T16:47:59+08:00
lastmod: 2025-12-30T16:47:59+08:00
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

[https://leetcode.cn/problems/remove-linked-list-elements/description/]()

# 思路

删除链表中的元素，需要控制连续的三个节点，控制要删除的节点是第二个节点。

# 代码

```go
func removeElements(head *ListNode, val int) *ListNode {
    dummyHead := &ListNode{
        Next: head,
    }

    for p := dummyHead; p.Next != nil; {
        if p.Next.Val == val {
            p.Next = p.Next.Next
        } else {
            p = p.Next
        }
    }

   return dummyHead.Next
}
```
