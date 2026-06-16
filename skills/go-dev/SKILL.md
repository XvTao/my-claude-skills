---
name: go-dev
description: Go development workflow — build, test, lint, mock generation, performance profiling for backend Go projects
---

# Go 开发工作流

Help the user with Go project development tasks: compiling, testing, linting, generating mocks, profiling performance, and managing dependencies.

## 基本操作

- **编译检查**: `go build ./...` 或带 tags: `go build -tags "$TAGS" ./...`
- **运行测试**:
  - 当前包: `go test . -v -count=1`
  - 所有包: `go test ./... -count=1`
  - 带覆盖率: `go test . -coverprofile=coverage.out -covermode=atomic`
  - 查看覆盖率: `go tool cover -html=coverage.out`
  - 竞态检测: `go test -race ./...`
- **Lint**: `golangci-lint run ./...`（如果项目配置了 .golangci.yml）

## Mock 生成

- 优先使用项目已有的 mock 方案（如 mockgen、moq、go-mock）
- 未配置则推荐: `go install github.com/golang/mock/mockgen@latest`
- 用法: `mockgen -source=pkg/interface.go -destination=pkg/mock/mock.go -package=mock`

## 性能分析

- Benchmarks: `go test -bench=. -benchmem`
- pprof 采集: `go test -cpuprofile=cpu.out -memprofile=mem.out`
- trace: `go test -trace=trace.out`
- 分析: `go tool pprof -http=:8080 cpu.out`

## 依赖管理

- 更新依赖: `go get -u ./...` 后 `go mod tidy`
- 查看依赖图: `go mod graph`
- vendor: `go mod vendor`

## 项目构建

- 检查是否是多 module 仓库（存在 go.work 文件）
- Go 版本 >= 1.24，留意 go.sum 和工具链兼容性

## Examples

- "帮我运行当前项目的所有测试" → run `go test ./...`
- "检查这个包有没有竞态问题" → run `go test -race ./pkg/...`
- "生成这个接口的 mock" → run `mockgen -source=./pkg/service.go -destination=./pkg/mock/service.go`
- "看看这段代码的性能瓶颈" → add pprof then `go tool pprof`
