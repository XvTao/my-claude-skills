---
name: code-review-zh
description: 代码检视 — 逐层审查代码的正确性、健壮性、可读性和性能，产出结构化评审意见
---

# 代码检视

对代码变更进行结构化审查，逐层扫描问题，产出可落地的评审意见。与内置 `/code-review` 互补（本 skill 更侧重你的项目上下文和 Go/K8s 实践）。

## 检视维度（按优先级排序）

### 1. 正确性（Correctness）
- 逻辑是否符合预期？边界条件是否处理？
- 并发安全：共享变量有无 race？map 并发读写？
- 错误处理：error 是否被检查？该 wrap 的是否 wrap 了？
- 资源释放：defer Close？context 超时传递？
- K8s specific：Reconcile 是否幂等？Finalizer 正确处理？Status 更新用 `Status().Update()`？

### 2. 健壮性（Robustness）
- 输入校验：API 参数校验、CRD spec 字段合法性
- 降级处理：依赖不可用时是否有降级（熔断/超时/重试）
- 重入安全：Controller 重复 Reconcile 是否有副作用
- 数据一致性：并发写入冲突处理（乐观锁/resourceVersion）

### 3. 可读性（Readability）
- 命名是否达意？`GetXXX`/`SetXXX`/`EnsureXXX` 是否符合惯例
- 函数长度（超过 50 行考虑拆分）
- 注释：公共函数有 godoc？复杂逻辑有行注释？
- 日志：错误场景有足够上下文？日志级别使用是否合理？

### 4. 性能（Performance）
- 不必要的内存分配（`fmt.Sprintf` 在热路径？）
- 循环内的 HTTP/RPC 调用（可批量？可并发？）
- K8s API 调用频率（List vs Get、Informer 缓存利用）
- 锁粒度（大锁拆小锁？用 RWMutex？）

### 5. 安全（Security）
- SQL 注入（如有 DB 操作）
- 敏感信息泄露（密码/密钥打印到日志）
- RBAC 权限过大（ClusterRole 是否需要 cluster-scoped）
- Webhook 准入控制是否可绕过

## 评审输出格式

对每个问题按以下格式输出：

```
## [P0/P1/P2] 问题简述
- 文件：path/to/file.go:行号
- 问题：具体描述
- 建议：修改建议或示例代码
```

严重级别：
| 级别 | 说明 | 处理方式 |
|------|------|---------|
| P0 | 正确性/安全问题 | 必须修复 |
| P1 | 健壮性/性能隐患 | 建议修复 |
| P2 | 可读性/规范问题 | 可优化 |

## 检视流程

1. 先通读 diff 了解变更全貌
2. 按维度逐层扫描（正确性 → 健壮性 → 可读性 → 性能 → 安全）
3. 汇总评审意见，按 P0/P1/P2 分级
4. 避免风格偏好之争（聚焦客观问题）

## Examples

- "帮我 review 一下当前分支的改动" → 逐步扫描各维度
- "重点看看这个 controller 的 Reconcile 逻辑" → 聚焦幂等性和并发安全
- "这块代码有没有安全风险" → 按安全维度快速过一遍
