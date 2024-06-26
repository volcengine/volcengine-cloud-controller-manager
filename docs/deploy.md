# Deployment
## IAM
`volcengine-cloud-controller-manager` is initialized using AccessKeyID and SecretAccessKey.  
The permissions listed in [policy](./doc/policy.json) need to be authorized to the AccessKeyID. 

See [IAM](https://www.volcengine.com/docs/6257/0) for details.
## Configurations
| Name                                                 | Explain                                                                                                                            | Required | Example                |
|------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|----------|------------------------| 
| account.ak                                           | Volcengine account ak                                                                                                              | **YES**  |                        |
| account.sk                                           | Volcengine account sk                                                                                                              | **YES**  |                        |
| account.region                                       | The region of service                                                                                                              | **YES**  | cn-beijing             |
| account.topHost                                      | Volcengint Open API host                                                                                                           | **YES**  | open.volcengineapi.com | 
| account.vpcID                                        | Volcengine VPC ID used by ccm. Resources related to ccm, such as ecs and clb, need to be in the same vpc                           | **YES**  | vpc-123                |
| account.subnetIDs                                    | The subnets of the vpc, used to allocate ip for clb                                                                                | **YES**  |                        |
| account.clusterID                                    | The **UNIQUE** identifier of cluster. Different clusterIDs are needed when deploying different volcengine-cloud-controller-manager | **YES**  | cluster-a              | 
| account.projectName                                  | The name of project, default value is `default`                                                                                    | **YES**  |                        | 
| controller.componentSetting.loadBalancerClass        | The service.spec.loadBalancerClass, default is `volcengine.com/clb`                                                                | **NO**   |                        |
| controller.componentSetting.watchServiceWithoutClass | Whether to reconcile Service with nil `spec.loadBalancerClass`. Default is false.                                                  | **否**    |                        |

## Installation Steps
`volcengine-cloud-controller-manager` is deployed as Deployment, and can access to the cluster with inClusterConfig or specified kubeconfig.
### Connect to cluster with inClusterConfig
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

### Connect to cluster with specified kubeconfig
1. Create rbac. See [rbac.yaml](./../manifests/cloud-controller-manager/templates/rbac.yaml) for details.
2. Create kubeconfig for `volcengine-cloud-controller-manager` service account.
   
   Replace `TOKEN` with the output of command `kubectl get secret -n kube-system volcengine-cloud-controller-manager -o json | jq -r '.data["token"]' | base64 -d`.
   
   Replace `CA` with the output of command `kubectl get secret -n kube-system volcengine-cloud-controller-manager -o json | jq -r '.data["ca.crt"]'`.
   
   Replace `CLUSTER_NAME` and `CLUSTER_NAME` with your cluster information.
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
    name: {{CLUSTER_NAME}}
```
3. Create configmap which contains kubeconfig
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
4. deploy volcengine-cloud-controller-manager
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