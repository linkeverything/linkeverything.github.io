---
title: "Kubernetes 에서 docker private registry 사용하기"
excerpt: "How to use private docker registry on kubernetes"
categories:
  - "Container"
  - "Kubernetes"
tag:
  - "kubernetes"
  - "docker"
  - "private registry"
last_modified_at: 2020-07-08
---

Kubernetes 사용하는 경우, [공식 docker hub](https://hub.docker.com) 를 사용하지 않고, 별도로 구축한 private registry 를 사용하는 방법을 설명합니다. 기본적으로 kubernetes 는 공식 registry 를 바라보도록 설정되어 있습니다. 


# test page


```
# kubectl delete secret docker-registry-login -n awesome-shopping # 삭제
kubectl create secret docker-registry docker-registry-login --namespace=awesome-shopping --docker-server=gitlab.azr-multiverse.skcc.com:4567 --docker-username={아이디} --docker-password={패스워드} --docker-email={이메일}
```
