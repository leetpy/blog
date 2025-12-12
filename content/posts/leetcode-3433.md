---
title: Leetcode 3433
date: 2025-12-12T19:26:23+08:00
lastmod: 2025-12-12T19:26:23+08:00
author: Author Name
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

[https://leetcode.cn/problems/count-mentions-per-user/description/?envType=daily-question&envId=2025-12-12]()


## 几个注意点

- events 中不是按时间戳排序好的
- 用户离线 60s 后自动重新上线
- 在单条消息中，同一个用户可能会被提及多次。每次提及都需要被分别统计

```go
import (
	"fmt"
	"sort"
	"strconv"
	"strings"
)

func countMentions(numberOfUsers int, events [][]string) []int {
	// 按时间和事件排序
	sort.Slice(events, func(i, j int) bool {
		if events[i][1] != events[j][1] {
			tsI, _ := strconv.Atoi(events[i][1])
			tsJ, _ := strconv.Atoi(events[j][1])
			return tsI < tsJ
		}

		return events[j][0] != "OFFLINE"
	})

	ret := make([]int, numberOfUsers)

	offlineTs := make([]int, numberOfUsers)

	for _, event := range events {
		eventType := event[0]
		ts, _ := strconv.Atoi(event[1])
		if eventType == "MESSAGE" {
			switch event[2] {
			case "HERE":
				for i := range numberOfUsers {
					if offlineTs[i] == 0 {
						ret[i]++
					} else if offlineTs[i]+60 <= ts {
						offlineTs[i] = 0
						ret[i]++
					}
				}

			case "ALL":
				for i := range ret {
					ret[i]++
				}

			default:
				ids := strings.Fields(event[2])
				for _, id := range ids {
					idx, _ := strconv.Atoi(id[2:])
					ret[idx]++
				}
			}

		} else {
			node, _ := strconv.Atoi(event[2])
			offlineTs[node] = ts
		}
	}
	return ret
}
```