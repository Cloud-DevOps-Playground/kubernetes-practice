# Geting started with kubernetes-practice repo

1. [Install](https://docs.docker.com/desktop/setup/install/windows-install/) [**_Docker Desktop_**](https://www.docker.com/products/docker-desktop/)

2. [Enable **_Kubernetes_**](https://docs.docker.com/desktop/features/kubernetes/)

3. Download [**_Helm_**](https://helm.sh/) by downloading:
    - [_Windows amd64_ file](https://github.com/helm/helm/releases/latest/) and unzipping its contents
    - [_MacOS_](https://helm.sh/docs/intro/install/#from-homebrew-macos)

4. Download [**_Kustomize_**](https://kustomize.io/)
    - [binary](https://github.com/kubernetes-sigs/kustomize/releases/latest/) (useful tool)
    - [MacOS](https://kubectl.docs.kubernetes.io/installation/kustomize/homebrew/)

5. Practice basic K8s resources:
    - [Namespace | Deployment | Service | Ingress](https://github.com/me-coder/kubernetes-practice/tree/main/ingress)
    - [PersistentVolume | PersistentVolumeClaim](https://github.com/me-coder/kubernetes-practice/tree/main/storage)

6. Practice Prometheus and Grafana deplpoyment using Kubernetes ingress: [monitoring](https://github.com/me-coder/kubernetes-practice/tree/main/monitoring)

    6.1. Deploy Prometheus and Gradana using `docker-compose`: [monitoring deployments using docker-compose](https://github.com/Cloud-DevOps-Playground/kubernetes-practice/tree/main/monitoring#using-docker-compose)