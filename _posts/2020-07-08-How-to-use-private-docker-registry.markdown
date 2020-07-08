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
  
  
# 개요

kubernetes 에 대한 글을 적으면서 반복적으로 설명하겠지만, kubernetes 에서 동일한 역할을 하는 pod, node 등의 집합은 동일한 namespace 로 관리해야 편합니다. deployment, service 등을 생성할 때에 namespace 를 별도로 설정하지 않으면 kubernetes 에 있는 default namespace를 사용하게 됩니다. 만약 하나의 집단만을 관리할 것이라면 이렇게 설정한 채로 default namespace를 그대로 사용해도 동작에는 지장이 없겠지만, 유지보수 측면이나 다른 사람이 관리하는 것을 감안한다면 별도의 namespace를 생성해서 관리해 주는 것이 좋습니다. 

### namespace 생성

다음 명령어로 namespace를 생성합니다. 이 이름이 모든 서비스들에 적용될 것이므로, 적절한 형태로 지어주는 것이 좋습니다.  


# secret 생성


```bash
# kubectl delete secret docker-registry-login -n awesome-shopping # 삭제
kubectl create secret docker-registry docker-registry-login --namespace=awesome-shopping --docker-server=gitlab.azr-multiverse.skcc.com:4567 --docker-username={아이디} --docker-password={패스워드} --docker-email={이메일}
```  


# yaml 파일에 적용

