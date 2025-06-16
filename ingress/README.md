# Ingress resource deployment
**Note**: Comment following lines in kustomization.yaml as under:
```yaml
# helmCharts:
#   - name: ingress-nginx
#     releaseName: ingress-nginx
#     repo: https://kubernetes.github.io/ingress-nginx
```

## Ingress controller deployment using `helm`
### deploy ingress controller
```shell
$ kubectl create namespace ingress-testing
$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
$ helm install --namespace ingress-testing local-ingress-nginx ingress-nginx/ingress-nginx
```

### test ingress controller
```shell
$ kubectl --namespace ingress-testing get services -o wide local-ingress-nginx-controller
```

### delete ingress controller
```shell
$ helm uninstall --namespace ingress-testing local-ingress-nginx
$ kubectl delete namespace ingress-testing
```

## Ingress controller deployment using `kubectl`
### deploy ingress controller
```shell
$ kubectl create namespace ingress-testing
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.0/deploy/static/provider/aws/deploy.yaml -n ingress-nginx
```

### test ingress controller
```shell
$ kubectl --namespace ingress-testing get services -o wide local-ingress-nginx-controller
```

### delete ingress controller
```shell
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.43.0/deploy/static/provider/cloud/deploy.yaml
$ kubectl delete namespace ingress-testing
```

## Testing with web-app deployment
```shell
$ kubectl create namespace ingress-testing
$ kubectl apply -n ingress-testing -k .
$ curl kubernetes.docker.internal
$ kubectl delete -n ingress-testing -k .
$ kubectl delete namespace ingress-testing
```

# Using `kustomize`
**Note**: Ensure following lines in kustomization.yaml are **uncommented**
```yaml
helmCharts:
  - name: ingress-nginx
    releaseName: ingress-nginx
    repo: https://kubernetes.github.io/ingress-nginx
```

## Deploy resources
```shell
# Render and review k8s manifest
$ kustomize build . --enable-helm
# Deploy resources
$ kustomize build . --enable-helm | kubectl apply -f -
```

## Test resources
```shell
$ curl kubernetes.docker.internal
```

## Delete resources
```shell
$ kustomize build . --enable-helm | kubectl delete  -f -
service "hello-service" deleted
deployment.apps "hello" deleted
ingress.networking.k8s.io "hello-ingress" deleted
```

# Detailed Testing
### From witin cluster
```shell
$ kubectl run -it --rm busybox --image=busybox --restart=Never
If you don't see a command prompt, try pressing enter.
/ # wget -q -O - hello-service:80
Hello, world!
Version: 2.0.0
Hostname: hello-
/ # exit
pod "busybox" deleted
```

### From host machine
```shell
$ kubectl get ns ingress-testing
NAME              STATUS   AGE
ingress-testing   Active   2m22s

$ kubectl get ing,pod,svc -n ingress-testing
NAME                                      CLASS   HOSTS                        ADDRESS     PORTS   AGE
ingress.networking.k8s.io/hello-ingress   nginx   kubernetes.docker.internal   localhost   80      2m53s

NAME                                           READY   STATUS      RESTARTS   AGE
pod/hello-65fd747b75-bsfrv                     1/1     Running     0          2m53s
pod/hello-65fd747b75-cqx5g                     1/1     Running     0          2m53s
pod/hello-65fd747b75-gv7dn                     1/1     Running     0          2m53s
pod/ingress-nginx-admission-create-7mxbg       0/1     Completed   0          2m53s
pod/ingress-nginx-admission-patch-htklb        0/1     Completed   1          2m53s
pod/ingress-nginx-controller-cd9d6bbd7-2nvhc   1/1     Running     0          2m53s

NAME                                         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/hello-service                        ClusterIP      10.107.24.148   <none>        80/TCP                       2m53s
service/ingress-nginx-controller             LoadBalancer   10.109.158.66   localhost     80:31921/TCP,443:31587/TCP   2m53s
service/ingress-nginx-controller-admission   ClusterIP      10.106.32.171   <none>        443/TCP                      2m53s

$ curl kubernetes.docker.internal
Hello, world!
Version: 2.0.0
Hostname: hello-65fd747b75-bsfrv
```


# Start minikube (Optional)
```shell
$ minikube start --cpus='4' --memory='4g' --disk-size='10g' --mount --mount-string="C:\Users\Yashodhan\Workspace\Cloud-DevOps-Playground\kubernetes\data:/minikube-data" --disable-metrics --delete-on-failure --driver="docker"
```