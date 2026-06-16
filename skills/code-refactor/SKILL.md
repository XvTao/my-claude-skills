---
name: code-refactor
description: 代码重构 — 在保持行为不变的前提下改善代码结构、可读性和可维护性，适用于 Go 后端项目
---

# 代码重构

在不改变外部行为的前提下，通过一系列受控的重构手法改善代码质量。适用场景：代码异味消除、架构优化、技术债务清理。

## 基本原则

- **行为不变**：重构不改变功能，不改接口签名（除非明确要求）
- **小步提交**：每次重构一个独立维度，每步都能通过编译和测试
- **测试护航**：重构前先确保有测试覆盖，没有则先加测试
- **一次只做一件事**：不混着重构和加功能

## Go 常见重构场景

### 1. 函数/方法过长
- 提取函数（Extract Method）：将逻辑块提取为具名函数
- 参数对象（Introduce Parameter Object）：多个相关参数封装为 struct
- 早返回（Early Return）：减少嵌套层级

### 2. 重复代码
- 提取公共逻辑为工具函数
- 使用 Go 组合替代继承式的重复
- 用 Go 1.22+ 的泛型消除类型特定重复逻辑

### 3. 接口设计
- 接口应小（1-3 个方法），遵循"接受接口，返回结构体"
- 检查是否有不必要的接口抽象（只有一个实现的接口未必需要）
- 用 `io.Reader`/`io.Writer` 等标准接口替代自定义

### 4. 错误处理
- 用 `errors.Is()` / `errors.As()` 替代类型断言
- 统一错误包装（`fmt.Errorf("...: %w", err)`）
- 避免 `_` 静默吞错误

### 5. 并发模式
- 用 `errgroup` 替代手写 `WaitGroup` + `chan error`
- 用 `sync.Once` 替代重复的初始化判断
- 用 `context.Context` 透传超时和取消信号
- 检查 goroutine 泄漏（有 `go` 是否有对应的退出机制）

### 6. 依赖管理
- 减少包循环依赖（用接口/事件解耦）
- 检查是否有多余的间接依赖

### 7. 配置与常量
- 硬编码字符串/数字抽为常量
- 环境变量读取集中化

## K8s Controller 重构专项

- Reconciler 中的长方法拆解（e.g. `reconcileXxx()` 按资源拆）
- 状态更新逻辑统一（`EnsureXXX` 模式：CreateOrUpdate → status patch）
- Event 录制集中化
- 判等逻辑（`equality.Semantic.DeepEqual`）替代手写字段对比

## 重构交付物

每次重构完成后输出：
- 改动摘要（改了哪些文件、什么手法）
- 风险点说明（哪些路径需要重点回归测试）
- 跑一遍 `go vet` + `golangci-lint` 确保无新问题

## Examples

- "帮我把这个 controller 的 Reconcile 函数拆一下，太长了" → Extract Method 分解
- "这个包里有很多重复的错误处理代码" → 统一 error wrapper
- "这块并发逻辑有 goroutine 泄漏风险" → 用 errgroup 重构
- "这个接口只有一处实现，要不要去掉" → 评估后简化
