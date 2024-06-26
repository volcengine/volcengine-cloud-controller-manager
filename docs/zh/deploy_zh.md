# 部署指南
## IAM
`volcengine-cloud-controller-manager` 使用火山引擎的 AccessKeyID 和 SecretAccessKey 初始化。
[policy](./../policy.json)中列举的接口是对接四层 CLB 所需要的最小权限集合，需要授权给部署 `volcengine-cloud-controller-manager` 时使用的 AccessKeyID。

IAM 鉴权的更多信息见[IAM](https://www.volcengine.com/docs/6257/0)。
## 配置项
| 名称                                                   | 说明                                                                                                                    | 是否必须  | 示例                     |
|------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|-------|------------------------| 
| account.ak                                           | 火山引擎账号 AccessKeyID                                                                                                    | **是** |                        |
| account.sk                                           | 火山引擎账号 SecretAccessKey                                                                                                | **是** |                        |
| account.region                                       | 火山引擎资源所属的区域                                                                                                           | **是** | cn-beijing             |
| account.topHost                                      | 火山引擎网关地址                                                                                                              | **是** | open.volcengineapi.com | 
| account.vpcID                                        | 火山引擎 VPC ID。`volcengine-cloud-controller-manager` 涉及到的所有火山引擎云资源（例如部署时使用的 ECS 机器、四层 CLB 等）需要处于同一个 VPC 下                | **是** | vpc-123                |
| account.subnetIDs                                    | VPC 内的子网 ID。`volcengine-cloud-controller-manager` 自动创建的 CLB 将从这些子网中分配 IP。                                             | **是** |                        |
| account.clusterID                                    | 集群的**唯一**标识符。 部署多个 `volcengine-cloud-controller-manager` 时，需要为每个 `volcengine-cloud-controller-manager` 设置一个具有唯一性的 ID。 | **是** | cluster-a              | 
| account.projectName                                  | 资源项目组，`volcengine-cloud-controller-manager` 自动创建的 CLB 会分配到设置的项目组中， 默认值为 `default`                                     | **是** |                        | 
| controller.componentSetting.loadBalancerClass        | `volcengine-cloud-controller-manager` 只会处理 Spec.loadBalancerClass 为指定值的 Service， 默认值为 `volcengine.com/clb`            | **否** |                        |
| controller.componentSetting.watchServiceWithoutClass | 是否处理 spec.loadBalancerClass 为空的 Service，默认值为 false                                                                    | **否** |                        |
## 部署步骤
`volcengine-cloud-controller-manager` 以 Deployment 的形式部署，通过 inClusterConfig 或者指定 kubeconfig 的方式访问 Kubernetes 集群。
### 通过 inClusterConfig 的方式访问集群
```bash
helm install cloud-controller-manager {{CHART_FOLDER}}/cloud-controller-manager -n {{NAMESPACE}} \
--set account.ak=akxxx \ 
--set account.sk=skxxx \
--set account.region=cn-beijing \
--set account.vpcID=vpc-xxx \
--set "account.subnetIDs={subnet-xxx, subnet-yyy}" \
--set account.clusterID=clusterA \
--set account.projectName=default \
--set controller.image.registry={{IMAGE_REGISTRY}} \ 
--set controller.image.name=cloud-controller-manager \
--set controller.image.tag={{IMAGE_TAG}}
```

### 通过指定 kubeconfig 的方式访问集群
1. 创建 `volcengine-cloud-controller-manager` 需要使用的 rbac 资源。资源模板见[rbac.yaml](./../../manifests/cloud-controller-manager/templates/rbac.yaml)。
2. 创建 `volcengine-cloud-controller-manager` 的 service account 对应的 kubeconfig。
   用 `kubectl get secret -n kube-system volcengine-cloud-controller-manager -o json | jq -r '.data["token"]' | base64 -d` 输出的结果替换 `TOKEN`。

   用 `kubectl get secret -n kube-system volcengine-cloud-controller-manager -o json | jq -r '.data["ca.crt"]'` 输出的结果替换 `CA`。

   用您的集群的集群名称和 apiserver 的地址替换 `CLUSTER_NAME` 和 `CLUSTER_NAME`。   

```yaml
apiVersion: v1
kind: Config
contexts:
  - context:
      cluster: {{CLUSTER_NAME}}
      user: system:volcengine-cloud-controller-manager
    name: system:volcengine-cloud-controller-manager
current-context: system:volcengine-cloud-controller-manager
users:
  - name: system:volcengine-cloud-controller-manager
    user:
      token: {{TOKEN}}
clusters:
  - cluster:
      certificate-authority-data: {{CA}}
      server: {{APISERVER_ADDR}}
    name: {{CLUSTER}}
```
3. 创建包含了 kubeconfig 的 configmap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: volcengine-cloud-controller-manager-k8s
  namespace: kube-system
data:
  cloud-controller-manager.conf: |
    apiVersion: v1
    kind: Config
    contexts:
      - context:
          cluster: {{CLUSTER_NAME}}
          user: system:volcengine-cloud-controller-manager
        name: system:volcengine-cloud-controller-manager
    current-context: system:volcengine-cloud-controller-manager
    users:
      - name: system:volcengine-cloud-controller-manager
        user:
          token: {{TOKEN}}
    clusters:
      - cluster:
          certificate-authority-data: {{CA}}
          server: {{APISERVER_ADDR}}
        name: {{CLUSTER_NAME}}
```
4. 部署 `volcengine-cloud-controller-manager`
```bash
helm install cloud-controller-manager {{CHART_FOLDER}}/cloud-controller-manager -n {{NAMESPACE}} \
--set account.ak=akxxx \ 
--set account.sk=skxxx \
--set account.region=cn-beijing \
--set account.vpcID=vpc-xxx \
--set "account.subnetIDs={subnet-xxx, subnet-yyy}" \
--set account.clusterID=clusterA \
--set account.projectName=default \
--set controller.image.registry={{IMAGE_REGISTRY}} \ 
--set controller.image.name=cloud-controller-manager \
--set controller.image.tag={{IMAGE_TAG}} \
--set controller.componentSetting.authenticationMode=useKubeconfig
```