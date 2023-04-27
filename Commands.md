### Port forwarding to access the web application service on minikube that is hosted on an ec2 instance

```
kubectl port-forward --address 0.0.0.0 svc/train-schedule-service-canary 30209:8090
```
