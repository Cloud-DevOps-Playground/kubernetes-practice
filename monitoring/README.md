# Deploy resources
```shell
# Render and review k8s manifest
$ kustomize build . --enable-helm
# Deploy resources
$ kustomize build . --enable-helm | kubectl apply -f -
```

# Test resouces
```shell
$ kubectl get ns monitoring
NAME              STATUS   AGE
monitoring   Active   2m22s

$ kubectl get cm,pv,pvc,pod,deploy,svc,ingress -n monitoring -o wide
NAME                                 DATA   AGE
configmap/grafana-server-conf        1      88s
configmap/ingress-nginx-controller   0      88s
configmap/kube-root-ca.crt           1      88s
configmap/prometheus-server-conf     1      88s

NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                       STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE   VOLUMEMODE
persistentvolume/pvc-214809fa-3ee4-4a78-9da9-02df7826f458   50Mi       RWO            Delete           Bound    monitoring/prometheus-pvc   hostpath       <unset>                          88s   Filesystem
persistentvolume/pvc-e6c67d04-d938-458d-9e3b-4e4dfe9d4032   49Mi       RWO            Delete           Bound    monitoring/grafana-pvc      hostpath       <unset>                          88s   Filesystem

NAME                                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE   VOLUMEMODE
persistentvolumeclaim/grafana-pvc      Bound    pvc-e6c67d04-d938-458d-9e3b-4e4dfe9d4032   49Mi       RWO            hostpath       <unset>                 88s   Filesystem
persistentvolumeclaim/prometheus-pvc   Bound    pvc-214809fa-3ee4-4a78-9da9-02df7826f458   50Mi       RWO            hostpath       <unset>                 88s   Filesystem

NAME                                           READY   STATUS      RESTARTS   AGE   IP           NODE             NOMINATED NODE   READINESS GATES
pod/grafana-75fcc7b74d-4p597                   1/1     Running     0          88s   10.1.0.241   docker-desktop   <none>           <none>
pod/ingress-nginx-admission-create-thbds       0/1     Completed   0          88s   10.1.0.243   docker-desktop   <none>           <none>
pod/ingress-nginx-admission-patch-hql7f        0/1     Completed   1          88s   10.1.0.244   docker-desktop   <none>           <none>
pod/ingress-nginx-controller-cd9d6bbd7-hf254   1/1     Running     0          88s   10.1.0.245   docker-desktop   <none>           <none>
pod/prometheus-6dd7dc7f-8vxrn                  1/1     Running     0          88s   10.1.0.242   docker-desktop   <none>           <none>

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES
                                                          SELECTOR
deployment.apps/grafana                    1/1     1            1           88s   grafana      grafana/grafana:latest
                                                          app=grafana
deployment.apps/ingress-nginx-controller   1/1     1            1           88s   controller   registry.k8s.io/ingress-nginx/controller:v1.12.0@sha256:e6b8de175acda6ca913891f0f727bca4527e797d52688cbe9fec9040d6f6b6fa   app.kubernetes.io/component=controller,app.kubernetes.io/instance=ingress-nginx,app.kubernetes.io/name=ingress-nginx
deployment.apps/prometheus                 1/1     1            1           88s   prometheus   quay.io/prometheus/prometheus:latest
                                                          app=prometheus

NAME                                         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE   SELECTOR
service/grafana-service                      ClusterIP      10.100.15.92     <none>        3000/TCP                     88s   app=grafana
service/ingress-nginx-controller             LoadBalancer   10.100.159.167   localhost     80:31558/TCP,443:32050/TCP   88s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=ingress-nginx,app.kubernetes.io/name=ingress-nginx
service/ingress-nginx-controller-admission   ClusterIP      10.106.47.162    <none>        443/TCP                      88s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=ingress-nginx,app.kubernetes.io/name=ingress-nginx
service/prometheus-service                   ClusterIP      10.107.192.47    <none>        9090/TCP                     88s   app=prometheus

NAME                                           CLASS   HOSTS                                          ADDRESS     PORTS   AGE
ingress.networking.k8s.io/monitoring-ingress   nginx   prometheus.k8s.internal,grafana.k8s.internal   localhost   80      88s
```

## Prometheus
```shell
# Ensure prometheus.k8s.internal is reachable (ping prometheus.k8s.internal)
$ curl -s http://prometheus.k8s.internal/
<a href="/query">Found</a>.

$ curl -s http://prometheus.k8s.internal/-/ready
Prometheus Server is Ready.

$ curl -s http://prometheus.k8s.internal/-/healthy
Prometheus Server is Healthy.

$ curl -s http://prometheus.k8s.internal/metrics | tail -5
# HELP promhttp_metric_handler_requests_total Total number of scrapes by HTTP status code.
# TYPE promhttp_metric_handler_requests_total counter
promhttp_metric_handler_requests_total{code="200"} 14
promhttp_metric_handler_requests_total{code="500"} 0
promhttp_metric_handler_requests_total{code="503"} 0
```

## Grafana
```shell
$ $ curl -s http://grafana.k8s.internal/
<a href="/login">Found</a>.
$ curl -s http://grafana.k8s.internal/healthz
Ok
```

# Delete resources
```shell
# Delete resources
$ kustomize build . --enable-helm | kubectl delete -f -
```

# Troubleshooting
## Prometheus from witin cluster
```shell
$ kubectl -n monitoring run -it --rm busybox --image=busybox --restart=Never
If you don't see a command prompt, try pressing enter.
/ # wget -q -O - prometheus-service:9090/-/ready
Prometheus Server is Ready.
/ # wget -q -O - prometheus-service:9090/-/healthy
Prometheus Server is Healthy.

/ # exit
pod "busybox" deleted
```

## Grafana from witin cluster
```shell
$ kubectl -n monitoring run -it --rm busybox --image=busybox --restart=Never
If you don't see a command prompt, try pressing enter.
/ # wget -q -O - grafana-service:3000/healthz
Ok
/ # exit
pod "busybox" deleted
```
