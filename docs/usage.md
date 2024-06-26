# Usage
The Kubernetes Services support richer L4 cloud load balance(CLB) capabilities through annotations.
## Limitation
Can not modify the CLB instances, listeners, or backend server groups maintained by the LoadBalancer type Service on the CLB console, otherwise, the traffic of CLB may become abnormal.
## Service annotation list
| Key                                                                               | Type    | Required | Explain                                                                                                                                                                                                                                | Default | Changeable |
|-----------------------------------------------------------------------------------|---------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|------------|
| service.beta.kubernetes.io/volcengine-loadbalancer-name                           | String  | NO       | The name of the CLB to be created.                                                                                                                                                                                                     | -      | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-id                             | String  | NO       | ID of reused CLB.                                                                                                                                                                                                                      | -      | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-pass-through                   | Boolean | NO       | Whether to enable pass through mode. Value: <br> **true**: enable. The backends of CLB are enis of Pod. <br> **false**: disable. The backends of CLB are NodePorts.                                                                    | false  | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-subnet-id                      | String  | YES      | ID of subnet, which is used to allocate ip for CLB.                                                                                                                                                                                    | -      | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-address-type                   | String  | NO       | Address type of CLB. Value: <br> **PUBLIC**: CLB can be accessed from the public network. <br> **PRIVATE**: CLB can only be accessed in VPC.                                                                                           | PUBLIC | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-isp-type                       | String  | NO       | ISP type of PUBLIC type CLB. See [EIP document](https://www.volcengine.com/docs/6402/71034) for more details.                                                                                                                          | BGP    | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-billing-type                   | Integer | NO       | Billing type of CLB. Value: <br> **2**: Paid by specification of CLB. <br> **3**: No need to specify specification of CLB, and will be charged by usage.                                                                               | 2      | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-eip-billing-type               | Integer | NO       | Billing type of EIP. Value: <br> **2**: Paid by bandwidth. <br> **3**:  Paid by network traffic usage.                                                                                                                                 | 3      | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-bandwidth                      | Integer | NO       | The peak bandwidth(Mbps) of the CLB.                                                                                                                                                                                                   | 1      | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-spec                           | String  | NO       | Specification of CLB. Value: <br> **small_1**. <br> **small_2**. <br> **medium_1**.<br> **medium_2**.<br> **large_1**.<br> **large_2**.<br> See [Specification document](https://www.volcengine.com/docs/6406/68063) for more details. | small_1 | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-sync-fields                    | String  | NO       | Whether to update specification of CLB according to the annotation. Value:<br> **spec**: update specification of CLB according to `loadbalancer-spec`.                                                                                 | -      | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-master-zone-id                 | String  | NO       | Master zone ID of CLB. <br> Can not be the same with `slave-zone-id`. <br> The zone ID where the subnet is located will be used by default. <br> See [Region and Zone](https://www.volcengine.com/docs/6406/74892) for more details.   | -      | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-slave-zone-id                  | String  | NO       | Slave zone ID of CLB. <br> Can not be the same with `master-zone-id`. <br> `master-zone-id` is required when this field is set. <br> Will be assigned randomly.                                                                        | -      | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-modification-protection-status | String  | NO       | Value:<br> **NonProtection**: Allows you to modify or delete instances through the console. <br> **ConsoleProtection**: forbidden to modify or delete instances through the console.                                                   | ConsoleProtection       | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-eip-BandwidthPackageId         | String  | NO       | ID of shared bandwidth package. <br> Only takes effect when `service.beta.kubernetes.io/volcengine-loadbalancer-address-type=PUBLIC`. <br> The original payment type will be overwritten.                                              | -      | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-scheduler                      | String  | NO       | Scheduling algorithm of listeners. Value:<br> **wrr**.<br> **wlc**. <br> **sh**. <br> See [Scheduling algorithm](https://www.volcengine.com/docs/6406/68067#%E8%B0%83%E5%BA%A6%E7%AE%97%E6%B3%95%E5%8E%9F%E7%90%86) for more details.  | wrr    | NO         |
| service.beta.kubernetes.io/volcengine-loadbalancer-health-check-flag              | String  | NO       | Value:<br> **on**: enable health check. <br> **off**: disable health check.                                                                                                                                                            | off    | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-health-check-interval          | Integer | NO       | Health check interval.Range: 1-300. Unit: seconds.                                                                                                                                                                                     | 2      | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-health-check-connect-timeout   | Integer | NO       | Health check timeout. Range: 1-60. Unit: seconds.                                                                                                                                                                                      | 2      | YES        | 
| service.beta.kubernetes.io/volcengine-loadbalancer-healthy-threshold              | Integer | NO       | The number of consecutive successful health checks before the backend server's health check status is changed from Fail to Success. Range: 3~10.                                                                                       | 3      | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-unhealthy-threshold            | Integer | NO       | The number of consecutive health check failures before the backend server's health check status is changed from Success to Failure. Range: 3-10.                                                                                       | 3      | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-acl-status                     | String  | NO       | Whether to enable access control for listener of CLB. Value:<br> **off**: disable. <br> **on**: enable.                                                                                                                                | off    | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-acl-type                       | String  | NO       | Access control type. Value:<br> **black**: Deny requests from the IP addresses in the selected access control lists. <br> **white**: Accept requests from the IP addresses in the selected access control lists.                       | -      | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-acl-id                         | String  | NO       | IDs of the access control lists. See [ACL](https://www.volcengine.com/docs/6406/68991) for more details. A maximum of 5 IDs can be passed in. Multiple IDs can be separated by `,`.                                                    | -      | YES        |
| service.beta.kubernetes.io/volcengine-loadbalancer-proxy-protocol                 | String  | NO       | Whether to enable Proxy Protocol feature. Value:<br> **standard**: enable. <br> **off**: disable.                                                                                                                                      | off    | YES        | 

## Example
### Use existing CLB
```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/volcengine-loadbalancer-id: "clb-mim02n8g5kw05smt1b******"
  name: clb-service
  namespace: default
spec:
  loadBalancerClass: volcengine.com/clb
  externalTrafficPolicy: Cluster
  selector:
    app: nginx
  ports:
  - name: test
    port: 80
    protocol: TCP
    targetPort: 80
  type: LoadBalancer
```
### Create new CLB
```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/volcengine-loadbalancer-subnet-id: "subnet-mizw4xqzpssg5smt1b******"
    service.beta.kubernetes.io/volcengine-loadbalancer-address-type: "PUBLIC"
    service.beta.kubernetes.io/volcengine-loadbalancer-isp-type: "BGP"
    service.beta.kubernetes.io/volcengine-loadbalancer-billing-type: "2"
    service.beta.kubernetes.io/volcengine-loadbalancer-eip-billing-type: "3"
    service.beta.kubernetes.io/volcengine-loadbalancer-bandwidth: "25" 
    service.beta.kubernetes.io/volcengine-loadbalancer-spec: "small_1"
  name: clb-service
  namespace: default
spec:
  loadBalancerClass: volcengine.com/clb
  externalTrafficPolicy: Cluster
  selector:
    app: nginx
  ports:
  - name: test
    port: 80
    protocol: TCP
    targetPort: 80
  type: LoadBalancer
```
### Configure scheduling policy
```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/volcengine-loadbalancer-id: "clb-mim02n8g5kw05smt1b******"
    service.beta.kubernetes.io/volcengine-loadbalancer-scheduler: "wrr"
  name: clb-service
  namespace: default
spec:
  loadBalancerClass: volcengine.com/clb
  externalTrafficPolicy: Cluster
  selector:
    app: nginx
  ports:
  - name: test
    port: 80
    protocol: TCP
    targetPort: 80
  type: LoadBalancer
```
### Configure health check
```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/volcengine-loadbalancer-id: "clb-mim02n8g5kw05smt1b******"
    service.beta.kubernetes.io/volcengine-loadbalancer-health-check-flag: "on" 
    service.beta.kubernetes.io/volcengine-loadbalancer-health-check-connect-timeout: "2" 
    service.beta.kubernetes.io/volcengine-loadbalancer-health-check-interval: "2" 
    service.beta.kubernetes.io/volcengine-loadbalancer-healthy-threshold: "3" 
    service.beta.kubernetes.io/volcengine-loadbalancer-unhealthy-threshold: "3" 
  name: clb-service
  namespace: default
spec:
  loadBalancerClass: volcengine.com/clb
  externalTrafficPolicy: Cluster
  selector:
    app: nginx
  ports:
  - name: test
    port: 80
    protocol: TCP
    targetPort: 80
  type: LoadBalancer
```
### Configure access control lists
```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/volcengine-loadbalancer-id: "clb-mim02n8g5kw05smt1b******"
    service.beta.kubernetes.io/volcengine-loadbalancer-acl-status: "on"
    service.beta.kubernetes.io/volcengine-loadbalancer-acl-type: "white"
    service.beta.kubernetes.io/volcengine-loadbalancer-acl-id: "acl-3cj44nv0jhhxc6c6rrtet****,acl-2febxt4pu0zy85oxruw0t****"
  name: clb-service
  namespace: default
spec:
  loadBalancerClass: volcengine.com/clb
  externalTrafficPolicy: Cluster
  selector:
    app: nginx
  ports:
  - name: test
    port: 80
    protocol: TCP
    targetPort: 80
  type: LoadBalancer
```
### Configure proxy protocol
> Proxy-Protocol is a beta feature of CLB. Submit [work order](https://console.volcengine.com/workorder/create/) to have a try.
```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/volcengine-loadbalancer-id: "clb-mim02n8g5kw05smt1b******"
    service.beta.kubernetes.io/volcengine-loadbalancer-proxy-protocol: "standard"
  name: clb-service
  namespace: default
spec:
  loadBalancerClass: volcengine.com/clb
  externalTrafficPolicy: Cluster
  selector:
    app: nginx
  ports:
  - name: test
    port: 80
    protocol: TCP
    targetPort: 80
  type: LoadBalancer
```
