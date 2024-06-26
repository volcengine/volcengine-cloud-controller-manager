# TroubleShooting
## Logs And Events
### Check the Service Event
```bash
kubectl describe service <loadbalancer-svc-name> -n <namespace-of-svc-resource> 
```
### Check the Cloud-Controller-Manager Logs
```bash
kubectl get pod -n <deploy-namespace> | grep volcengine-cloud-controller-manager
kubectl logs -n <deploy-namespace> <pod-name>
```
## CLB Error Code
The meaning of each error code is described in the [CLB API document](https://www.volcengine.com/docs/6406/71749).
