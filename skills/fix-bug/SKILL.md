---
name: fix-bug
description: Systematic bug debugging workflow — reproduce, isolate, root cause, fix, verify, applicable to Go and Java backend services
---

# Bug 排查流程

A systematic approach to debugging backend service issues. Follows a 5-phase process: Reproduce → Isolate → Root Cause → Fix → Verify.

## 1. 复现（Reproduce）

- 明确触发条件（输入/请求/状态/环境）
- 确认是否稳定复现，还是偶发
- 获取错误信息（日志、堆栈、返回码）
- 记录现场状态（时间、版本、数据）

## 2. 缩小范围（Isolate）

### Go 项目
- 查找 panic / fatal 堆栈: 搜索 `panic:`、`fatal error:` 关键字
- pprof 采集: 在怀疑的性能点加 `net/http/pprof`
- `-race` 检测数据竞争: `go test -race ./...`
- 加 debug 日志: 在可疑路径引入 `log.Printf("DEBUG: ...")`

### Java 项目
- 查看堆栈: `jstack <pid>` 或 `jcmd <pid> Thread.print`
- GC 分析: `jstat -gcutil <pid> 1s` 或 `-XX:+PrintGCDetails` 日志
- Heap dump: `jmap -dump:format=b,file=heap.hprof <pid>`
- 排查 OOM: 检查堆/元空间/直接内存

## 3. 定位根因（Root Cause）

- 逐层追溯：API → Service → Store → DB/External
- 对比正常 vs 异常的输入输出
- 检查并发/竞态条件（goroutine leak、锁等待）
- 检查状态机/数据一致性
- **日志分析技巧**：
  - 搜索 `error`、`warn`、`exception`、`timeout`
  - 按 trace ID/request ID 聚合关联
  - 检查前后文时间戳（延迟突变）

## 4. 修复（Fix）

- 最小改动原则 — 只改根因，不顺便重构
- 加测试覆盖：单元测试 + 回归测试
- 检查是否有同类问题（搜索同模式代码）
- 修复后运行 `go vet` / `golangci-lint` 确保无新警告

## 5. 验证（Verify）

- 用 `/verify` skill 运行 app 确认修复
- 用 `/code-review` 检查改动
- 通过测试: `go test ./...` 或 `mvn test`
- 回归: 确认原触发路径不再复现

## Examples

- "有个 bug，cceone-backend 的某某 API 偶尔返回 500" → follow all 5 phases
- "线上 Go 服务 goroutine 数量一直在涨" → isolate with pprof → find goroutine leak → fix → verify
- "Java 服务 OOM 了" → get heap dump → analyze with jhat/jvisualvm → fix leak
