---
name: arch-design
description: 方案设计 — 从需求到架构决策，涵盖系统设计、API 设计、数据模型设计、K8s CRD 设计等场景
---

# 方案设计

协助完成技术方案设计，从需求到架构决策、模块划分、接口和数据模型定义。特别适配 Go 后端 + Kubernetes 生态项目。

## 适用场景

- 新功能/新服务的架构设计
- 现有系统的重大改造方案
- K8s CRD + Controller 方案设计
- API 设计（RESTful / gRPC）
- 数据模型与存储方案

## 设计流程

### 1. 需求分析
- 确认功能需求和非功能需求（性能/可用性/扩展性/安全性）
- 识别关键约束（技术栈、部署环境、团队能力）
- 评估现有系统的影响面

### 2. 总体架构

画出架构图关键要素（描述而非画图）：
- **分层结构**：API 层 → Service 层 → Store 层 → 外部依赖
- **模块划分**：每个模块的职责和边界
- **数据流**：请求的完整流转路径
- **组件通信**：同步（gRPC/REST）vs 异步（Event/Message Queue）

### 3. 详细设计

#### 接口设计（REST/gRPC）
- 资源模型（RESTful 资源 URL 设计）
- 请求/响应 Schema
- 错误码设计
- 版本策略（URI 版本 vs Header 版本）

#### 数据模型
- 数据库/存储选型依据
- 核心表/文档结构
- 索引设计
- 数据生命周期（归档/清理）

#### K8s CRD 设计
- Group + Version + Kind 命名
- Spec 字段（必需 vs 可选 + 默认值）
- Status 字段 + Conditions
- 是否需要有 Finalizer
- 是否需要有 Webhook（Validating/Mutating）
- 多版本转换策略（hub + spoke）

### 4. 技术选型决策

记录关键决策及其理由（Architecture Decision Record）：

```
## ADR-N: 标题
- 上下文：什么场景需要做这个决策
- 方案对比：候选方案及优缺点
- 决策：选了哪个方案
- 理由：为什么选它
- 后果：选了之后的影响和代价
```

### 5. 交付清单

- [ ] 技术方案文档
- [ ] 接口定义文件（proto / OpenAPI YAML）
- [ ] CRD 定义草案（如涉及）
- [ ] 数据模型定义
- [ ] 任务拆解列表

## 设计原则

- **简单优先**：不要在第一天做过度设计
- **渐进式变更**：支持灰度/兼容升级
- **可观测性**：设计时就考虑 metrics / tracing / logging
- **防御性编程**：假设外部依赖会挂、数据会错

## Examples

- "帮我设计一个多集群资源同步方案" → 分析需求 → 出架构 → CRD 设计 → 任务拆解
- "这个新功能需要在 cceone-backend 中加一组 API" → REST 资源设计 → Service 层拆分 → 数据模型
- "设计一个 Operator 来管理 xxx 资源" → CRD 设计 → Reconciler 架构 → Webhook 策略
- "比较两种方案在性能和可维护性上的优劣" → ADR 格式输出
