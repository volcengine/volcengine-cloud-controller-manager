# Kubernetes Cloud Controller Manager for Volcengine

`volcengine-cloud-controller-manager` is the external Kubernetes cloud controller manager implementation for Volcengine. Running `volcengine-cloud-controller-manager` allows you build your kubernetes clusters leverage on many cloud services(now only support L4 CLB) on Volcengine. You can read more about Kubernetes cloud controller manager [here](https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/).

## Prerequisites
Supports the Kubernetes cluster version between [v1.24, v1.28].

## Implementation Details

Currently `volcengine-cloud-controller-manager` implements:

* servicecontroller - responsible for creating LoadBalancers when a service of `Type: LoadBalancer` is created in Kubernetes.

## Deployment

### Configurations
| Name                                                 | Explain                                                                       | Required  | Example                 |
|------------------------------------------------------|-------------------------------------------------------------------------------|-----------|-------------------------| 
| account.accountID                                    | Volcengine account                                                            |  **YES**  |                         |
| account.ak                                           | Volcengine account ak                                                         |  **YES**  |                         |
| account.sk                                           | Volcengine account sk                                                         |  **YES**  |                         |
| account.region                                       | The region of service                                                         |  **YES**  | cn-beijing              |
| account.topHost                                      | Volcengint Open API host                                                      |  **YES**  | open.volcengineapi.com  | 
| account.vpcID                                        | Volcengine VPC ID                                                             |  **YES**  | vpc-123                 |
| account.subnetIDs                                    | The subnets of the vpc, used to allocate ip for clb                           |  **YES**  |                         |
| account.clusterID                                    | The unique identifier                                                         |  **YES**  |                         | 
| account.projectName                                  | The name of project, default value is `default`                               |  **YES**  |                         | 
| controller.componentSetting.loadBalancerClass        | The service.spec.loadBalancerClass, default is `volcengine.com/clb`           |  **NO**   |                        |

### Download helm chart and install

```bash
helm install cloud-controller-manager {{CHART_FOLDER}}/cloud-controller-manager -n {{NAMESPACE}} \
--set account.accountID=210000 \
--set account.ak=akxxx \ 
--set account.sk=skxxx \
--set account.region=cn-beijing \
--set account.vpcID=vpc-xxx \
--set account.subnetIDs=["subnet-xxx", "subnet-yyy"] \
--set account.clusterID=clusterA \
--set account.projectName=default \
--set controller.image.registry={{IMAGE_REGISTRY}} \ 
--set controller.image.name=cloud-controller-manager \
--set controller.image.tag={{IMAGE_TAG}}
```

## Annotations for Service
The Kubernetes Services support richer L4 cloud load balance(CLB) capabilities through annotations, details [here](https://www.volcengine.com/docs/6460/100304#%E5%B8%B8%E7%94%A8-annotation-%E9%9B%86%E5%90%88)

## Metrics
TODO
