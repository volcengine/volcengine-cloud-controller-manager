# 监控指标
## 指标清单
| 名称                                    | 类型        | 说明                                                                                              |
|---------------------------------------|-----------|-------------------------------------------------------------------------------------------------|
| workqueue_depth                       | Gauge     | work queue 当前的深度                                                                                | 
| workqueue_adds_total                  | Counter   | 添加到 work queue 中的事件总数                                                                           |
| workqueue_queue_duration_seconds      | Histogram | 事件在 work queue 中等待的时间                                                                           |
| process_resident_memory_bytes         | Gauge     | 常驻内存用量                                                                                          |
| process_cpu_seconds_total             | Counter   | CPU 用量                                                                                          | 
| go_goroutines                         | Gauge     | goroutines 用量                                                                                   | 
| rest_client_requests_total            | Counter   | `volcengine-cloud-controller-manager` 向 apiserver 发送的请求总数，可以从 `method` 和 `code` 维度对请求进行聚合分析     |
| rest_client_request_duration_seconds  | Histogram | `volcengine-cloud-controller-manager` 向 apiserver 发送的请求总数，可以从 `verb` 维度对请求进行聚合分析                |
| ccm_managed_svc_total                 | Gauge     | `volcengine-cloud-controller-manager` 管理的 `LoadBalancer` 类型的 Service 总数                         |
| transport_request_duration_in_seconds | Histogram | `volcengine-cloud-controller-manager` 向火山引擎网关发送的请求时间分布，可以从 `action` 和 `status_code` 维度对请求进行聚合分析 |

## Prometheus 配置示例
| 名称                     | PromQL                                                                                                                                                         |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Workqueue depth        | workqueue_depth{job="volcengine-cloud-controller-manager"}                                                                                                     | 
| Workqueue adds qps     | sum(rate(workqueue_adds_total{job="volcengine-cloud-controller-manager"}[$interval])) by (name)                                                                |
| Workqueue latency      | histogram_quantile(0.99, sum(rate(workqueue_queue_duration_seconds_bucket{job="volcengine-cloud-controller-manager"}[$interval])) by (name, le))               |
| Memory                 | process_resident_memory_bytes{job="volcengine-cloud-controller-manager"}                                                                                       |
| CPU                    | rate(process_cpu_seconds_total{job="volcengine-cloud-controller-manager"}[$interval])                                                                          | 
| goroutines             | go_goroutines{job="volcengine-cloud-controller-manager"}                                                                                                       | 
| Kube api qps           | sum(rate(rest_client_requests_total{job="volcengine-cloud-controller-manager",code=~"2.."}[$interval])) by (method,code)                                       |
| Kube api latency       | histogram_quantile(0.99, sum(rate(rest_client_request_duration_seconds_bucket{job="volcengine-cloud-controller-manager", verb="PATCH"}[$step])) by (verb, le)) |
| Managed service        | ccm_managed_svc_total{}                                                                                                                                        |
| Volcengine api qps     | sum(rate(transport_request_duration_in_seconds_count{job="volcengine-cloud-controller-manager"}[$step])) by (action)                                           |
| Volcengine api latency | histogram_quantile(0.99, sum(rate(transport_request_duration_in_seconds_bucket{job="volcengine-cloud-controller-manager"}[$step])) by (action, le))            | 