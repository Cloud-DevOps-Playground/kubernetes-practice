## Deploy resources
```shell
# Render and review k8s manifest
$ kustomize build .
# Deploy resources
$ kustomize build . | kubectl apply -f -
```

## Test resources
```shell
$ kubectl get pv task-pv-volume -n storage-testing
NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                           STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
task-pv-volume   50Mi       RWO            Retain           Bound    storage-testing/task-pv-claim   manual         <unset>                          83s

$ kubectl get pvc task-pv-claim -n storage-testing
  NAME            STATUS   VOLUME           CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
  task-pv-claim   Bound    task-pv-volume   50Mi       RWO            manual         <unset>                 74s

$ kubectl get pod task-pv-pod -n storage-testing
  NAME          READY   STATUS    RESTARTS   AGE
  task-pv-pod   1/1     Running   0          44s

$ kubectl exec -n storage-testing -it task-pv-pod -- /bin/bash
root@task-pv-pod:/# apt update
root@task-pv-pod:/# apt install curl
root@task-pv-pod:/# curl http://localhost/
  Hello from Kubernetes storage
root@task-pv-pod:/# exit
```

## Delete resources
```shell
$ kustomize build . | kubectl delete  -f -
```
