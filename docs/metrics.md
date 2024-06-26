# Metrics
## Metrics list
| Name                                  | Type      | Explain                                                                                                                              |
|---------------------------------------|-----------|--------------------------------------------------------------------------------------------------------------------------------------|
| workqueue_depth                       | Gauge     | Current depth of work queue                                                                                                          | 
| workqueue_adds_total                  | Counter   | Total number of add tasks processed by work queue                                                                                    |
| workqueue_queue_duration_seconds      | Histogram | The time the task has been waiting in the work queue                                                                                 |
| process_resident_memory_bytes         | Gauge     | Resident set size that ccm used                                                                                                      |
| process_cpu_seconds_total             | Counter   | CPU usage                                                                                                                            | 
| go_goroutines                         | Gauge     | goroutines usage                                                                                                                     | 
| rest_client_requests_total            | Counter   | The request initiated by ccm to apiserver, which can be aggregated and analyzed from the `method` and `code` dimensions              |
| rest_client_request_duration_seconds  | Histogram | The request initiated by ccm to apiserver, which can be aggregated and analyzed from the `verb` dimension                            |
| ccm_managed_svc_total                 | Gauge     | Number of services managed by CCM                                                                                                    |
| transport_request_duration_in_seconds | Histogram | The request initiated by ccm to volcengine host, which can be aggregated and analyzed from the `action` and `status_code` dimensions |

## Prometheus example
| Name                   | PromQL                                                                                                                                                         |
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