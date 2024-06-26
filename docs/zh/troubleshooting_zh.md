# 排障指南
## 日志和事件
### 检查 Service 的事件信息
```bash
kubectl describe service <loadbalancer-svc-name> -n <namespace-of-svc-resource> 
```
### 检查组件日志信息
```bash
kubectl get pod -n <deploy-namespace> | grep volcengine-cloud-controller-manager
kubectl logs -n <deploy-namespace> <pod-name>
```
## CLB 错误码
事件或者日志中如果包含错误码，可以查看[CLB API 文档](https://www.volcengine.com/docs/6406/71749)以获取错误码的含义。
