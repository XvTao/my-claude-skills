---
name: k8s-operator
description: Kubernetes Operator development helper — controller-runtime, CRD, Webhook, Reconciler patterns and best practices
---

# K8s Operator 开发

Help the user develop Kubernetes Operators using controller-runtime / Kubebuilder patterns. Covers project structure, CRD management, Reconciler best practices, Webhooks, and local debugging.

## 项目结构（基于 Kubebuilder / controller-runtime）

```
.
├── api/          # CRD types (v1/, v1beta1/)
├── controllers/  # Reconciler 实现
├── internal/     # 内部工具包
├── config/       # Kustomize manifests
│   ├── crd/      # CRD YAML
│   ├── rbac/     # RBAC
│   ├── webhook/  # Webhook 配置
│   └── samples/  # CR 示例
├── Dockerfile
└── Makefile
```

## 常用操作

### CRD 管理
- 查看已安装 CRD: `kubectl get crd`
- 查看 CRD 定义: `kubectl describe crd <crd-name>`
- 查看 CR 实例: `kubectl get <resource> -n <ns>`

### Reconciler 最佳实践
- 幂等性：Reconcile 必须是幂等的
- 状态机：用 Status.Conditions 表达资源状态
- Requeue：非致命错误用 `ctrl.Result{RequeueAfter: 5*time.Second}`
- 终结器（Finalizer）：需要清理外部资源时必须加 Finalizer
- 事件录制: `recorder.Event()` 输出到 `kubectl describe`

### Webhook
- ValidatingWebhook: 校验字段合法性
- MutatingWebhook: 注入默认值
- 本地测试: `cert-manager` 或自签证书

## 本地调试

```bash
# 一：在集群外运行 controller
make run

# 二：结合 envtest 跑集成测试
make test

# 三：部署到集群测试
make docker-build docker-push deploy
```

## 常用依赖版本参考
- controller-runtime: 与 K8s 版本对齐
- apimachinery
- client-go

## Hints
- 用 `reconcile.RequestsByClusterObject` 处理集群类资源
- 多集群场景注意 `RESTConfig` 切换
- Status subresource 需要通过 `Status().Update()`

## Examples

- "帮我新建一个 CRD，包含 spec 和 status" → scaffold with kubebuilder → define types → generate CRD
- "这个 Reconciler 不幂等，看看怎么改" → review reconcile logic → add status tracking → handle edge cases
- "给这个 Operator 加一个 MutatingWebhook" → scaffold webhook → implement Default() → configure cert-manager
