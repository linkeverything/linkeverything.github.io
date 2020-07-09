---
title: "Kubernetes 에서 docker private registry 사용하기"
excerpt: "How to use private docker registry on kubernetes"
categories:
  - "dev"
  - "container"
tag:
  - "kubernetes"
  - "docker"
  - "private registry"
last_modified_at: 2020-07-08
---

Kubernetes 사용하는 경우, [공식 docker hub](https://hub.docker.com) 를 사용하지 않고, 별도로 구축한 private registry 를 사용하는 방법을 설명합니다. 기본적으로 kubernetes 는 공식 registry 를 바라보도록 설정되어 있습니다.  
  
  
## 개요

kubernetes 에 대한 글을 적으면서 반복적으로 설명하겠지만, kubernetes 에서 동일한 역할을 하는 pod, node 등의 집합은 동일한 namespace 로 관리해야 편합니다. deployment, service 등을 생성할 때에 namespace 를 별도로 설정하지 않으면 kubernetes 에 있는 default namespace를 사용하게 됩니다. 만약 하나의 집단만을 관리할 것이라면 이렇게 설정한 채로 default namespace를 그대로 사용해도 동작에는 지장이 없겠지만, 유지보수 측면이나 다른 사람이 관리하는 것을 감안한다면 별도의 namespace를 생성해서 관리해 주는 것이 좋습니다. 


#### namespace 생성

다음 명령어로 namespace를 생성합니다. 이 이름이 모든 서비스들에 적용될 것이므로, 적절한 형태로 지어주는 것이 좋습니다.  

```bash
kubectl create namespace mynamespace
```
이렇게 해서 `mynamespace`라는 이름으로 namespace를 생성해 주고, 앞으로 생성되는 모든 요소에 metadata 부분에 `namespace : mynamespace`를 추가해 줍니다.


## secret 생성

특정 docker registry 에 접근하게 하려면 우선 kubernetes 의 요소 중에서 secret을 생성해 주어야 합니다. 이것이 secret 이라고 불려지는 이유는, id / password 등을 이용해서 특정 docker registry 에 로그인하는 정보를 담고 있기 때문입니다. 

여기서는 단순히 id / password를 이용해서 로그인 하는 것으로 예시를 들었습니다. 실제 환경에서는 password 대신에 github / gitlab 등에서 발생하는 **token** 등으로 대체하여야 보안 문제를 해소할 수 있습니다. 
{: .notice--warning}


```bash
kubectl create secret registry-credentials docker-registry-login --namespace=mynamespace --docker-server=<server-address>:<port> --docker-username={아이디} --docker-password={패스워드} --docker-email={이메일}
```    


## yaml 파일에 적용

다음으로는 kubernetes의 deployment 를 생성하는 yaml 파일을 수정합니다. 다른 부분은 별도로 수정할 필요가 없고, 아래와 같이 containers 를 설정해 주는 부분만 추가해 주면 됩니다.

```yaml

...(중략)

  template:
    metadata:
      labels:
        app: appapp
    spec:
      imagePullSecrets:
      - name: registry-credentials
      containers:
      - name: namenamename
        image: image:tag
        imagePullPolicy: Always
        env:

...(후략)

```

이렇게 하면, 해당 deployment 가 생성되는 시점에 image를 가져오는 것을 해당 registry 에서 가져오게 됩니다.


## 참고

아래는 참고할 만 한 내용들입니다. 


#### namespace 삭제 방법

```bash
kubectl delete namespace mynamespace
```


#### secret 삭제 방법

```bash
kubectl delete secret docker-registry-login -n mynamespace
```



