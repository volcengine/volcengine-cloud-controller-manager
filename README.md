# Kubernetes Cloud Controller Manager for Volcengine
English | [中文](./README_zh.md)

`volcengine-cloud-controller-manager` is the external Kubernetes cloud controller manager implementation for Volcengine. Running `volcengine-cloud-controller-manager` allows you build your kubernetes clusters leverage on many cloud services(now only support L4 [CLB](https://www.volcengine.com/docs/6406)) on Volcengine.
You can read more about Kubernetes cloud controller manager [here](https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/).

## Prerequisites
### Kubernetes version
Supports the Kubernetes cluster version between [v1.24, v1.28].
### Network requirement
`volcengine-cloud-controller-manager` need to visit `open.volcengineapi.com`, which is the OpenAPI address of Volcano Engine.

- The network from `volcengine-cloud-controller-manager` to DNS needs to be reachable if you use a self-built DNS. And because the self-built DNS returns the public IP when visiting the OpenAPI of Volcano Engine,
  it is a must to ensure that `volcengine-cloud-controller-manager` can access the public network.
- If you do not want `volcengine-cloud-controller-manager` to access the public network, you need to use `volcengine-cloud-controller-manager` in the Volcano Engine VPC network. And `open.volcengineapi.com` will be resolved to
  a private IP in the 100 network segment.

## Implementation Details

Currently `volcengine-cloud-controller-manager` implements:

* servicecontroller - responsible for creating LoadBalancers when a service of `Type: LoadBalancer` is created in Kubernetes.

## Getting Started
See the [Getting Started](docs/deploy.md) document.
## Usage
See the [Usage](docs/usage.md) document.
## Troubleshooting
See the [Trouble Shooting](docs/troubleshooting.md) document.
## Metrics
See the [Metrics](docs/metrics.md) document.
