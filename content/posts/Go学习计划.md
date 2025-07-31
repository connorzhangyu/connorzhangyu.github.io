+++
title = "Go学习计划"
date = "2025-07-31T16:02:25+07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
+++

# 现代 Go 学习计划（适合 Java 背景开发者）

---

## 🏁 阶段一：语言基础（Week 1~2）

### 🎯 目标
掌握 Go 的语法结构、数据类型、控制流、函数机制。

### ✅ 学习内容
- 基本语法、变量、常量、数组、切片、map
- 控制结构（if, for, switch）
- 函数与多返回值、defer
- 指针（区别于 Java 的核心知识点）
- 结构体与方法

### 📚 推荐资料
- [Tour of Go（交互式）](https://go.dev/tour/)
- [Go by Example](https://gobyexample.com/)
- 李文周基础语法章节（可作为中文参考）

---

## 🚀 阶段二：并发与标准库（Week 3~4）

### 🎯 目标
理解 Go 的并发模型，熟悉关键标准库。

### ✅ 学习内容
- Goroutine、Channel、select
- 错误处理（error 接口、errors 包）
- 文件操作、时间处理、字符串处理、JSON 编解码
- 单元测试、基准测试

### 📚 推荐资料
- 《Go语言101》并发章节
- [Learn Go with Tests](https://quii.gitbook.io/learn-go-with-tests/)
- Go.dev 文档：[`net/http`](https://pkg.go.dev/net/http)、`time`、`io`

---

## 🧱 阶段三：现代开发工具与依赖管理（Week 5）

### 🎯 目标
熟练掌握项目结构、模块化、依赖管理。

### ✅ 学习内容
- Go Modules（go.mod、go.sum）
- 项目结构（cmd/pkg/internal）
- 日志库（zap, zerolog）
- 配置管理（viper）

---

## 🌐 阶段四：Web 开发实践（Week 6~7）

### 🎯 目标
掌握基于 Go 的 Web 服务开发

### ✅ 框架建议
- 首选 [Gin](https://github.com/gin-gonic/gin)
- 可选 go-zero、Fiber

### ✅ 学习内容
- 路由、请求响应、参数绑定、表单验证
- 中间件、日志、错误处理
- 连接数据库（GORM / sqlx）
- RESTful API 设计

---

## 📦 阶段五：实战项目构建（Week 8~10）

### 🎯 目标
构建实际项目，了解部署流程

### ✅ 项目建议
1. 简单任务管理系统（用户 + 任务 + 认证）
2. API 网关 + 用户微服务（适配 go-zero 或 kratos）

### ✅ 涉及技能
- 分层架构（handler/service/repository）
- Docker 容器化
- 使用 swagger/OpenAPI 文档
- Makefile、CI/CD 简化构建流程

---

## 🧠 阶段六：进阶与最佳实践（持续）

### 🎯 目标
深入理解语言设计、掌握泛型、性能调优与分布式实践。

### ✅ 内容方向
- 泛型（Go 1.18+）：类型参数、约束、泛型方法
- Context 上下文机制
- 高性能编程（内存管理、sync 包）
- 微服务框架 go-zero、kratos
- gRPC、protobuf、消息队列

---

## 🛠️ 学习辅助工具推荐

| 类型       | 工具       |
|------------|------------|
| IDE        | GoLand、VSCode + Go 插件 |
| 构建工具   | make、air（热加载） |
| 单元测试   | `go test -v`、`gotestsum` |
| API 测试   | Postman、curl、httpie |
| 容器部署   | Docker、Docker Compose |

---

> 如果你希望我进一步帮你生成 Notion 模板或具体实践项目样例，也可以继续告诉我！