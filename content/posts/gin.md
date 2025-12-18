---
title: Go-Gin 参数
date: 2025-12-18T11:14:21+08:00
lastmod: 2025-12-18T11:14:21+08:00
author: 云弋
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: /blog/img/gin.webp
# covercaption: a description of the cover image
# images:
#   - /img/cover.jpg
categories:
  - web
tags:
  - gin
# nolastmod: true
# math: true
draft: false
---

<!--more-->

## 参数获取

### 位置参数

- 获取到的类型都是 string 类型，需要自己进行类型转换
- 如果参数不存在，返回的是空字符串

```go
// GET /users/:id
r.GET("/users/:id", func(c *gin.Context) {
    id := c.Param("id")
    c.JSON(200, gin.H{"id": id})
})

// 也支持多个
// GET /orders/:order_id/items/:item_id
r.GET("/orders/:id/items/:item_id", func(c *gin.Context) {
    id := c.Param("id")
    itemId := c.Param("item_id")
    c.JSON(200, gin.H{"id": id})
})
```

### Query 参数

```go
// GET /search?keyword=gin&page=2

keyword := c.Query("keyword")        // 不存在返回 ""
page := c.DefaultQuery("page", "1")  // 不存在给默认值
```

### Form 参数 (x-www-form-urlencoded / multipart）

```go
// POST /login
// Content-Type: application/x-www-form-urlencoded

// username=admin&password=123456

username := c.PostForm("username")
password := c.DefaultPostForm("password", "")
```

文件上传

```go
file, _ := c.FormFile("file")
```

### JSON

```go
type CreateUserReq struct {
    Name string `json:"name" binding:"required"`
    Age  int    `json:"age" binding:"gte=0,lte=150"`
}

var createParmas CreateUserReq
err := c.ShouldBindJSON(&createParmas)
```

### Header 参数

```go
token := c.GetHeader("Authorization")
reqID := c.GetHeader("X-Request-Id")
```

### Cookie 参数

```go
// 获取
cookie, err := c.Cookie("session_id")

// 设置
c.SetCookie("session_id", "xxx", 3600, "/", "", false, true)
```

## 参数校验

gin 在调用 bind 相关函数时，会提取 `binding` 注解，然后使用 `go-playground/validator.v10` 库来校验。

> 虽然 `go-playground/validator.v10` 库使用的是 `validate` 注解，但是在 gin 框架中，必须使用 `binding` 注解

### 结构体定义

```go
type CreateUserReq struct {
	Username string `json:"username" binding:"required,min=3,max=20"`
	Age      int    `json:"age" binding:"gte=1,lte=150"`
	Email    string `json:"email" binding:"required,email"`
}
```

### 自定义校验器

有时候，对于某个参数，validator 没有提供默认的校验方式，我们需要有自己的校验方法，
可以通过 `RegisterValidation` 来注册自己的校验器。

```go
import (
	"regexp"

	"github.com/gin-gonic/gin/binding"
)

v, _ := binding.Validator.Engine().(*validator.Validate)

v.RegisterValidation("mobile", func(fl validator.FieldLevel) bool {
    return regexp.MustCompile(`^1[3-9]\d{9}$`).MatchString(fl.Field().String())
})


type Req struct {
    Phone string `json:"phone" binding:"required,mobile"`
}
```

### 返回校验错误信息

一般情况下，我们在调用 `Bind` 之后，判断 err 是否为空，然后直接返回 err 的错误信息。
默认情况下，`validator` 返回的错误信息包含每个校验出错的信息。返回的是英文的，
直接把完整的校验信息返回给用户不太合适。

```go
func(c *gin.Context) {
  var req CreateUserReq

  if err := c.ShouldBindJSON(&req); err != nil {
    c.JSON(400, gin.H{
      "msg": err.Error(),
    })
    return
  }

  c.JSON(200, gin.H{"ok": true})
}
```

### 翻译错误

```go
import (
	"net/http"
	"regexp"

	"github.com/gin-gonic/gin"
	"github.com/gin-gonic/gin/binding"
	zh "github.com/go-playground/locales/zh"
	ut "github.com/go-playground/universal-translator"
	"github.com/go-playground/validator/v10"

	zhTranslations "github.com/go-playground/validator/v10/translations/zh"
)

type CreateUserReq struct {
	Username string `json:"username" binding:"required,min=3,max=20"`
	Age      int    `json:"age" binding:"gte=1,lte=150"`
	Email    string `json:"email" binding:"required,email"`
}

var trans ut.Translator

func InitValidator() {
	if v, ok := binding.Validator.Engine().(*validator.Validate); ok {
		zhT := zh.New()
		uni := ut.New(zhT, zhT)

		trans, _ = uni.GetTranslator("zh")
		_ = zhTranslations.RegisterDefaultTranslations(v, trans)
	}
}

func main() {
	InitValidator()

	r := gin.Default()

	r.POST("/create", func(c *gin.Context) {
		var req CreateUserReq
		if err := c.ShouldBindJSON(&req); err != nil {
			errs := err.(validator.ValidationErrors)
			msg := errs[0].Translate(trans) // 取第一个错误
			c.JSON(400, gin.H{"msg": msg})
			return
		}
		c.JSON(http.StatusOK, gin.H{
			"message": "OK",
		})
	})

	r.Run()
}
```

现在返回的错误信息类似：

```json
{
  "msg": "Age必须大于或等于1"
}
```

gin 默认使用字段名做 key， 我们还可以手动指定

```go
v.RegisterTagNameFunc(func(fld reflect.StructField) string {
    name := fld.Tag.Get("label")
    if name == "" {
        return fld.Name
    }
    return name
})

type CreateUserReq struct {
    Username string `json:"username" label:"用户名" binding:"required,min=3"`
    Age      int    `json:"age" label:"年龄" binding:"gte=1,lte=150"`
}
```

这样返回的错误信息，会优先使用我们指定的 `label` eg:

```json
{
  "msg": "年龄必须大于或等于1"
}
```

现在大部分问题都解决了，但是还有一个问题，就是我们自定义的校验器没有翻译，返回的错误信息如下：

```json
{
  "msg": "Key: 'CreateUserReq.Phone' Error:Field validation for 'Phone' failed on the 'mobile' tag"
}
```

我们可以指定校验器的翻译

```go
// 翻译 mobile
func registerMobileTranslation(v *validator.Validate) {
    v.RegisterTranslation(
        "mobile",
        trans,
        func(ut ut.Translator) error {
            // 校验器默认文案
            return ut.Add("mobile", "{0} 手机号格式不正确", true)
        },
        func(ut ut.Translator, fe validator.FieldError) string {
            // 实际渲染文案
            t, _ := ut.T("mobile", fe.Field())
            return t
        },
    )
}

// 初始化校验器
func InitValidator() {
	if v, ok := binding.Validator.Engine().(*validator.Validate); ok {
		zhT := zh.New()
		uni := ut.New(zhT, zhT)

		trans, _ = uni.GetTranslator("zh")
		_ = zhTranslations.RegisterDefaultTranslations(v, trans)

		// 1️⃣ 注册 mobile 校验规则
		_ = v.RegisterValidation("mobile", func(fl validator.FieldLevel) bool {
			mobile := fl.Field().String()
			return regexp.MustCompile(`^1[3-9]\d{9}$`).MatchString(mobile)
		})

    // 一定要放在翻译初始化之后，否则会 panic
		// 2️⃣ 注册中文翻译
		registerMobileTranslation(v)
	}
}

```
