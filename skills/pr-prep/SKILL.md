---
name: pr-prep
description: Pre-PR checklist — build, test, lint, self-review, adapts to Go or Java projects automatically
---

# PR 准备检查清单

Guides the user through a comprehensive pre-PR checklist. Auto-detects whether the project is Go or Java based and runs appropriate checks.

## 1. 自动检测项目类型

检测项目根目录是否存在以下文件，判断是 Go 还是 Java 项目：
- `go.mod` → 走 Go 流程
- `pom.xml` / `build.gradle` → 走 Java 流程

## 2. Go 项目检查

- [ ] 编译通过: `go build ./...`（带 `go vet ./...`）
- [ ] 单元测试通过: `go test ./... -count=1`
- [ ] 竞态检测: `go test -race ./...`（关键项目必做）
- [ ] Lint: `golangci-lint run ./...`
- [ ] 依赖干净: `go mod tidy` 后无多余依赖

## 3. Java 项目检查

- [ ] 编译通过: `mvn compile` 或 `gradle build -x test`
- [ ] 单元测试通过: `mvn test` 或 `gradle test`
- [ ] 检查: `mvn checkstyle:check`（如果项目配置了）

## 4. 通用检查

- [ ] **Commit message 规范**：
  - `feat/fix/chore/docs/refactor/test: 简短描述`
  - 关联 issue: `Closes #123`
- [ ] **Diff 自查**：
  - 无硬编码的敏感信息（密钥、IP、密码）
  - 无调试日志/注释遗留
  - 无死代码或 import 残留
- [ ] **运行 `/code-review`** 审查当前 diff
- [ ] 如果有 API 变更，检查兼容性

## 5. K8s 相关项目额外检查

- [ ] CRD 变更后，YAML 文件已更新
- [ ] RBAC（ClusterRole/ClusterRoleBinding）权限最小化
- [ ] 升级兼容性（存量资源不改名/不删字段）
- [ ] Webhook 更新后检查证书配置

## 6. 提 PR

```bash
git push origin <branch>
# 然后通过 gh CLI 或浏览器创建 PR
```

## Examples

- "帮我把当前分支检查一遍准备提 PR" → detect project type → run all checks → report results
- "检查一下这个 PR 有没有忘了改 RBAC" → check if K8s → verify RBAC yaml changes
- "帮我写 commit message" → check staged changes → format as conventional commit
