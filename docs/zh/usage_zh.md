# 使用说明
`volcengine-cloud-controller-manager` 根据 `LoadBalancer` 类型的 Service 上填写的注解信息配置火山引擎的四层 CLB。
## 使用限制
请勿擅自在 CLB 控制台上更改通过 LoadBalancer 类型的 Service 维护的 CLB 实例、监听器、后端服务器组，否则可能造成集群中的负载均衡服务异常。
## Service 注解清单
| Key                                                                               | 类型      | 是否必须               | 描述                                                                                                                                                                                                                                                                    | 默认值               | 是否支持修改 |
|-----------------------------------------------------------------------------------|---------|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------|--------|
| service.beta.kubernetes.io/volcengine-loadbalancer-name                           | String  | 否                  | 创建的负载均衡实例名称，不指定则自动生成名称。                                                                                                                                                                                                                                               | 无                 | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-id                             | String  | 否                  | 对接已有 CLB 时，CLB 的 ID                                                                                                                                                                                                                                                   | 无                 | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-pass-through                   | Boolean | 否                  | 是否开启直通 Pod 模式。取值：<br> **true**：开启，直接关联 Pod 作为负载均衡器的后端。<br> **false**：不开启，关联 NodePort 作为负载均衡器的后端                                                                                                                                                                       | false             | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-subnet-id                      | String  | 是                  | 负载均衡实例所属的子网 ID。                                                                                                                                                                                                                                                       | 无                 | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-address-type                   | String  | 否                  | 负载均衡实例的网络访问类型。取值：<br> **PUBLIC**:：公网类型的负载均衡实例。<br> **PRIVATE**：私网类型的负载均衡实例。                                                                                                                                                                                           | PUBLIC            | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-isp-type                       | String  | 否                  | 负载均衡实例的线路类型。详细参数解释以及是否需要开白使用，请参见 [EIP 文档](https://www.volcengine.com/docs/6402/71034)。                                                                                                                                                                                | BGP               | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-billing-type                   | Integer | 否                  | 负载均衡实例的计费类型。取值：<br> **2**：按量计费-按规格计费。指定实例规格，并按照实例的使用时长计费。<br> **3**：按量计费-按使用量计费。无需指定实例规格，按照实例实际消耗的性能容量计费。当负载均衡选择 **按量计费-按使用量计费** 类型时，无需配置实例规格类型，即`loadbalancer-spec`参数。                                                                                               | 2                 | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-eip-billing-type               | Integer | 否                  | 公网 IP（EIP）计费类型。取值：<br> **2**：按量计费-按带宽上限，指定带宽上限后，将按照使用时长计费，与实际流量无关。 <br> **3**：按量计费-按实际流量，指定带宽上限后，将按照实际使用的出公网流量计费，与使用时长无关。                                                                                                                                             | 3                 | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-bandwidth                      | Integer | 否                  | 负载均衡实例的公网带宽峰值，单位为 Mbps。                                                                                                                                                                                                                                               | 1                 | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-spec                           | String  | 否                  | 负载均衡实例的规格类型。取值：<br> **small_1**：小型I。<br> **small_2**：小型II。<br> **medium_1**：中型I。<br> **medium_2**：中型II。<br> **large_1**：大型I。<br> **large_2**：大型II。<br> 负载均衡实例规格类型的详细说明，请参见 [产品类型与规格](https://www.volcengine.com/docs/6406/68063)                                      | small_1           | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-sync-fields                    | String  | 需要更新 CLB 实例的规格时，必选 | 更新 YAML 时，是否感知 `loadbalancer-spec`字段的变更，并更新 CLB 实例。取值：<br> **spec**：感知`loadbalancer-spec`字段的变更，并更新 CLB 实例的规格。                                                                                                                                                         | 无                 | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-master-zone-id                 | String  | 否                  | 负载均衡实例的主可用区 ID。<br> 取值不能与`slave-zone-id`参数取值相同。 <br> 不配置该参数或该参数为空时，默认分配子网所在可用区为主可用区。<br> 各地域支持的可用区情况以及可用区 ID，请参见 [地域与可用区](https://www.volcengine.com/docs/6406/74892)。                                                                                                | 无                 | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-slave-zone-id                  | String  | 否                  | 负载均衡实例的备可用区 ID。<br> 取值不能与`master-zone-id`参数取值相同。 <br> 如果配置该参数，则参数`master-zone-id`必须一并配置，否则会报错。 <br> 不配置该参数或该参数为空时，根据指定地域支持的可用区随机分配备可用区。                                                                                                                               |                   |        |
| service.beta.kubernetes.io/volcengine-loadbalancer-modification-protection-status | String  | 否                  | 负载均衡实例的删除保护功能，取值：<br> **NonProtection**：不开启控制台修改保护功能，表示允许通过控制台修改实例或删除实例。 <br> **ConsoleProtection**：开启控制台修改保护功能，表示禁止通过控制台修改实例或删除实例。<br> 不配置该参数或该参数为空时，默认为空，表示开启控制台修改保护功能。                                                                                             | ConsoleProtection | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-eip-BandwidthPackageId         | String  | 否                  | 共享带宽资源包 ID。<br> 仅在`service.beta.kubernetes.io/volcengine-loadbalancer-address-type=PUBLIC`时生效。<br> 生效后原先的付费方式（按流量计费或按固定带宽计费）会被覆盖。                                                                                                                                     | 无                 | 是      |
 | service.beta.kubernetes.io/volcengine-loadbalancer-scheduler                      | String  | 否                  | 负载均衡实例的监听器调度算法。取值：<br> **wrr**：权重值越高的后端服务器，被轮询到的次数（概率）越高。<br> **wlc**：将请求分发给“当前连接/权重”比值最小的后端服务器 <br> **sh**：基于源 IP 地址的一致性哈希，相同的源地址会调度到相同的后端服务器。<br> 详细说明参见 [调度算法](https://www.volcengine.com/docs/6406/68067#%E8%B0%83%E5%BA%A6%E7%AE%97%E6%B3%95%E5%8E%9F%E7%90%86)。 | wrr               | 否      |
| service.beta.kubernetes.io/volcengine-loadbalancer-health-check-flag              | String  | 否                  | 是否开启健康检查。取值：<br> **on**：开启健康检查。<br> **off**：不开启健康检查。                                                                                                                                                                                                                  | off               | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-health-check-interval          | Integer | 否                  | 健康检查时间间隔。取值范围:1~300.单位:秒。                                                                                                                                                                                                                                             | 2                 | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-health-check-connect-timeout   | Integer | 否                  | 健康检查的超时时间。取值范围：1~60。单位：秒。                                                                                                                                                                                                                                             | 2                 | 是      | 
| service.beta.kubernetes.io/volcengine-loadbalancer-healthy-threshold              | Integer | 否                  | 健康检查连续成功多少次后，将后端服务器的健康检查状态由 Fail 判定为 Success。取值范围：3~10                                                                                                                                                                                                                | 3                 | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-unhealthy-threshold            | Integer | 否                  | 健康检查连续失败多少次后，将后端服务器的健康检查状态由 Success 判定为 Fail。取值范围：3-10                                                                                                                                                                                                                | 3                 | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-acl-status                     | String  | 否                  | 负载均衡实例的监听器是否开启访问控制。取值：<br> **off**：不开启<br> **on**：开启。                                                                                                                                                                                                                 | off               | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-acl-type                       | String  | 否                  | 负载均衡实例的监听器开启访问控制的方式。取值：<br> **black**：黑名单方式。表示仅拒绝来自所选访问控制策略组中设置的 IP 地址或地址段的请求。 <br> **white**：白名单方式。表示监听器仅转发来自所选访问控制策略组中设置的 IP 地址或地址段的请求。                                                                                                                             | 无                 | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-acl-id                         | String  | 否                  | 负载均衡实例的监听器所绑定的访问控制策略组（ACL）ID。策略组相关信息，请参见[管理访问控制策略组](https://www.volcengine.com/docs/6406/68991)。最多支持传入 5 个策略组 ID，多个策略组 ID 间使用英文逗号（,）分隔。                                                                                                                               | 无                 | 是      |
| service.beta.kubernetes.io/volcengine-loadbalancer-proxy-protocol                 | String  | 否                  | 是否开启 Proxy Protocol 源 IP 透传，取值：<br> **standard**：开启 proxy protocol 源 IP 透传。 <br> **off**：关闭 proxy protocol 源 IP 透传。                                                                                                                                                   | off               | 是      | 

## 示例
### 对接已有 CLB
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
### 新建 CLB 
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
### 配置调度策略
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
### 配制健康检查
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
### 配置访问控制
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
### 配置源 IP 透传
> Proxy-Protocol 协议功能为 CLB 产品的邀测功能，如需使用，请[提交工单](https://console.volcengine.com/workorder/create/)或联系客户经理申请。
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
