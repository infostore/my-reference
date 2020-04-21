# Docker & Kubernetes

## Sample

https://github.com/arisu1000/kubernetes-book-sample
https://hub.docker.com/r/arisu1000/simple-container-app

## 정리할 자료

쿠버네티스 입문: P94

## Kubernetes

### Command

```sh
kubectl get nodes -o wide --no-headers

cpu1       Ready      <none>   42d   v1.16.3   10.1.5.17   <none>   Ubuntu 18.04.3 LTS   4.15.0-91-generic   docker://18.9.7
cpu2       Ready      <none>   42d   v1.16.3   10.1.5.18   <none>   Ubuntu 18.04.3 LTS   4.15.0-91-generic   docker://18.9.7
cpu3       Ready      <none>   42d   v1.16.3   10.1.5.19   <none>   Ubuntu 18.04.3 LTS   4.15.0-91-generic   docker://18.9.7
gpu1       Ready      <none>   42d   v1.16.3   10.1.5.15   <none>   Ubuntu 18.04.3 LTS   4.15.0-55-generic   docker://18.9.7
gpu2       Ready      <none>   42d   v1.16.3   10.1.5.16   <none>   Ubuntu 18.04.3 LTS   4.15.0-55-generic   docker://18.9.7
master1    NotReady   master   42d   v1.16.3   10.1.5.12   <none>   Ubuntu 18.04.3 LTS   4.15.0-91-generic   docker://18.9.7
master2    Ready      master   42d   v1.16.3   10.1.5.13   <none>   Ubuntu 18.04.3 LTS   4.15.0-91-generic   docker://18.9.7
master3    Ready      master   42d   v1.16.3   10.1.5.14   <none>   Ubuntu 18.04.3 LTS   4.15.0-91-generic   docker://18.9.7
storage1   Ready      <none>   42d   v1.16.3   10.1.5.20   <none>   Ubuntu 18.04.3 LTS   4.15.0-72-generic   docker://18.9.7
storage2   Ready      <none>   42d   v1.16.3   10.1.5.21   <none>   Ubuntu 18.04.3 LTS   4.15.0-72-generic   docker://18.9.7
storage3   Ready      <none>   42d   v1.16.3   10.1.5.22   <none>   Ubuntu 18.04.3 LTS   4.15.0-72-generic   docker://18.9.7
```

```sh
# 출력의 6번째 컬럼을 출력
kubectl get nodes -o wide --no-headers | awk '{print $6}'

10.1.5.17
10.1.5.18
10.1.5.19
10.1.5.15
10.1.5.16
10.1.5.12
10.1.5.13
10.1.5.14
10.1.5.20
10.1.5.21
10.1.5.22
```

```sh
kubectl get nodes -o json

{
    "apiVersion": "v1",
    "items": [
        {            
            "status": {
                "addresses": [
                    {
                        "address": "10.1.5.17",
                        "type": "InternalIP"
                    },
                    {
                        "address": "cpu1",
                        "type": "Hostname"
                    }
                ]
```

```sh
kubectl get nodes -o json | jq -r '.items[].status.addresses[] | select(.type=="InternalIP") | .address'

10.1.5.17
10.1.5.18
10.1.5.19
10.1.5.15
10.1.5.16
10.1.5.12
10.1.5.13
10.1.5.14
10.1.5.20
10.1.5.21
10.1.5.22
```

```sh
kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}

10.1.5.17
10.1.5.18
10.1.5.19
10.1.5.15
10.1.5.16
10.1.5.12
10.1.5.13
10.1.5.14
10.1.5.20
10.1.5.21
10.1.5.22
```

```sh
# json을 flat하게 보여주네
# sudo snap install gron
kubectl get pods kafdrop-deepphi-55d44fbbfc-wtrfv -o json | gron

json = {};
json.apiVersion = "v1";
json.kind = "Pod";
json.metadata = {};
json.metadata.creationTimestamp = "2020-04-06T04:45:22Z";
json.metadata.generateName = "kafdrop-deepphi-55d44fbbfc-";
json.metadata.labels = {};
json.metadata.labels["app.kubernetes.io/instance"] = "kafdrop-deepphi";
json.metadata.labels["app.kubernetes.io/name"] = "kafdrop";
json.metadata.labels["pod-template-hash"] = "55d44fbbfc";
json.metadata.name = "kafdrop-deepphi-55d44fbbfc-wtrfv";
```

```sh
# 네트워크 문제 추적
kubectl run -it --generator=run-pod/v1 --image=nicolaka/netshoot --overrides='{ "spec": { "nodeSelector": { "kubernetes.io/hostname": "master1" }}}' netshoot - bash

kubectl expose deployment hello-kube --type=NodePort --name=hello-kube-service
```

```sh
# 기본 네임스페이스 변경 -> kubens(https://raw.githubusercontent.com/abmetb/kubectx/master/kubens)를 사용해도 됨

# 현재 context 확인
$ kubectl config current-context

    kubernetes-admin@cluster.local

# context 정보 확인
$ kubectl config get-contexts kubernetes-admin@cluster.local

    CURRENT   NAME                             CLUSTER         AUTHINFO           NAMESPACE
    *         kubernetes-admin@cluster.local   cluster.local   kubernetes-admin

# 네임스페이스 변경
$ kubectl config set-context kubernetes-admin@cluster.local --namespace=deepphi
    
    Context "kubernetes-admin@cluster.local" modified.

# 변경 확인
$ kubectl config view

    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: DATA+OMITTED
        server: https://10.0.0.131:6443
    name: cluster.local
    contexts:
    - context:
        cluster: cluster.local
        namespace: deepphi
        user: kubernetes-admin
    name: kubernetes-admin@cluster.local
    current-context: kubernetes-admin@cluster.local
    kind: Config
    preferences: {}
    users:
    - name: kubernetes-admin
    user:
        client-certificate-data: REDACTED
        client-key-data: REDACTED
```

```sh
# api 버전 확인
$ kubectl api-versions
```

### 