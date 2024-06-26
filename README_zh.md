# Kubernetes Cloud Controller Manager for Volcengine
中文 | [English](./README.md)

`volcengine-cloud-controller-manager` 是火山引擎实现的外部 Kubernetes 云控制器。 在集群里运行 `volcengine-cloud-controller-manager` 可以帮助您对接火山的云资源（当前仅支持对接四层的 [CLB](https://www.volcengine.com/docs/6406)）。
可以在 [这里](https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/) 获取 Kubernetes 云控制器的更多信息。

## 约束限制
### Kubernetes 版本约束
支持版本号在 [v1.24, v1.28] 之间的 Kubernetes 集群。
### 网络约束
`volcengine-cloud-controller-manager` 需要访问火山引擎的 OpenAPI 的通用服务接入地址 `open.volcengineapi.com`。

- 如果您使用自建的 DNS，需要确保 `volcengine-cloud-controller-manager` 到 DNS 的网络打通。并且由于自建的 DNS 访问火山引擎的服务接入地址时返回公网 IP，
  需要确保 `volcengine-cloud-controller-manager` 能访问公网。
- 如果您不希望 `volcengine-cloud-controller-manager` 访问公网，需要在火山引擎 VPC 网络内使用 `volcengine-cloud-controller-manager`。在 VPC 网络内，
  `open.volcengineapi.com` 会解析到 100 网段内的私网 IP。

## 实现功能

当前，`volcengine-cloud-controller-manager` 实现了以下功能:

* servicecontroller - 当您在集群中创建 `LoadBalancer` 类型的 Service 时，servicecontroller 能根据 Service 上填写的注解信息配置火山引擎的四层 CLB。

## 部署指南
见[部署指南](docs/zh/deploy_zh.md)
## 使用说明
见[使用说明](docs/zh/usage_zh.md)
## 排障指南
见[排障指南](docs/zh/troubleshooting_zh.md)。
## 监控指标
见[监控指标](docs/zh/metrics_zh.md)。
