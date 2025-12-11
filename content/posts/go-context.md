---
date: "2025-11-11T09:47:42+08:00"
draft: false
author: 云弋
title: "Go Context"
categories:
  - go
tags:
  - go
---

<!--more-->

# Context 类型

```go
type Context interface {
    Deadline() (deadline time.Time, ok bool)

    Done() <-chan struct{}

    Err() error

    Value(key any) any
}
```

## 具体实现

### empytCtx

emptyCtx 就是一个空结构体，对应的方法实现都是返回零值

```go
type emptyCtx struct{}
```

### cancelCtl

- `done`: 存储的是 chan struct{}, 用来记录 Context 是否结束的

```go
type cancelCtx struct {
	Context

	mu       sync.Mutex            // protects following fields
	done     atomic.Value          // of chan struct{}, created lazily, closed by first cancel call
	children map[canceler]struct{} // set to nil by the first cancel call
	err      atomic.Value          // set to non-nil by the first cancel call
	cause    error                 // set to non-nil by the first cancel call
}
```

### valueCtx
