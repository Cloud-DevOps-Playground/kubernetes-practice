# Prometheus
## Deployment
```shell
# Render and review k8s manifest
$ kustomize build . --enable-helm
# Deploy resources
$ kustomize build . --enable-helm | kubectl apply -f -
```

## Testing
```shell
$ kubectl get ns monitoring
NAME              STATUS   AGE
monitoring   Active   2m22s

$ kubectl get cm,pv,pvc,pod,deploy,svc,ingress -n monitoring -o wide
NAME                              READY   STATUS              RESTARTS   AGE
pod/prometheus-575db4985c-mbxkt   0/1     ContainerCreating   0          3m23s

NAME                         TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/prometheus-service   NodePort   10.108.224.41   <none>        9090:30090/TCP   3m23s

# Ensure prometheus.k8s.internal is reachable (ping prometheus.k8s.internal)
$ curl -s http://prometheus.k8s.internal/metrics | tail -5
# HELP promhttp_metric_handler_requests_total Total number of scrapes by HTTP status code.
# TYPE promhttp_metric_handler_requests_total counter
promhttp_metric_handler_requests_total{code="200"} 14
promhttp_metric_handler_requests_total{code="500"} 0
promhttp_metric_handler_requests_total{code="503"} 0
```

# Troubleshooting
## From witin cluster
```shell
$ kubectl -n monitoring run -it --rm busybox --image=busybox --restart=Never
If you don't see a command prompt, try pressing enter.
/ # wget -q -O - prometheus-service:9090
Hello, world!
Version: 2.0.0
Hostname: hello-
/ # exit
pod "busybox" deleted
```