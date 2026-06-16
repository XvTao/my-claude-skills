---
name: k8s-debug
description: Kubernetes troubleshooting — diagnose pods, workloads, networking, and cluster issues layer by layer
---

# Kubernetes 排障

Help the user diagnose Kubernetes cluster issues by walking through a structured layer-by-layer approach: Pod → Workload → Network → Cluster.

## kubeconfig 切换

- 提示用户切换 kubeconfig: `export KUBECONFIG=/path/to/kubeconfig` 或用 `--kubeconfig` 参数
- 记录常用 kubeconfig 位置供后续使用

## Pod 级别排查

1. **Pod 状态**: `kubectl get pods -n <ns> -o wide`
2. **Pod 详情**: `kubectl describe pod <pod> -n <ns>`
3. **Pod 日志**: `kubectl logs <pod> -n <ns> [--tail=100] [-f]`
4. **多容器日志**: `kubectl logs <pod> -c <container> -n <ns>`
5. **进入 Pod 调试**: `kubectl exec -it <pod> -- sh`
6. **事件**: `kubectl get events -n <ns> --sort-by='.lastTimestamp'`

## 工作负载排查

1. **Deployment**: `kubectl describe deploy <name> -n <ns>`
2. **滚动更新状态**: `kubectl rollout status deploy/<name> -n <ns>`
3. **历史**: `kubectl rollout history deploy/<name> -n <ns>`
4. **回滚**: `kubectl rollout undo deploy/<name> --to-revision=<rev>`

## 网络排查

1. **Service 端点**: `kubectl get endpoints <svc> -n <ns>`
2. **Ingress**: `kubectl describe ingress <name> -n <ns>`
3. **DNS 解析**: 启动 dnsutils pod 执行 `nslookup <svc>.<ns>`
4. **NodePort/LB**: `kubectl get svc -n <ns> -o wide`

## 集群/Node 排查

1. **Node 状态**: `kubectl get nodes -o wide` 然后 `kubectl describe node <node>`
2. **资源使用**: `kubectl top nodes` / `kubectl top pods -n <ns>`
3. **CRD**: `kubectl get crd | grep <关键词>`

## 实用工具 Pod

```yaml
# 调试用 busybox
kubectl run -it --rm debug --image=busybox -- sh
# 网络调试
kubectl run -it --rm net-debug --image=nicolaka/netshoot -- sh
```

## Examples

- "帮我查一下 cceone-backend 在 aliyun 集群上为什么起不来" → check kubeconfig → describe pod → check events → check logs
- "这个 Deployment 滚动更新卡住了" → `kubectl rollout status` → `kubectl describe replicaset`
- "看看集群里哪个节点资源最紧张" → `kubectl top nodes`
