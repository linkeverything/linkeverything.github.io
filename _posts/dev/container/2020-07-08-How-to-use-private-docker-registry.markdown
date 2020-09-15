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

> Kubernetes 사용하는 경우, [공식 docker hub](https://hub.docker.com) 를 사용하지 않고, 별도로 구축한 private registry 를 사용하는 방법을 설명합니다. 기본적으로 kubernetes 는 공식 registry 를 바라보도록 설정되어 있습니다.  

Container 환경에 대해서 학습하기 시작하면서 가장 처음 접하게 되는 것이 아마도 docker 일 것입니다. docker 는 container 환경을 이용하여 특정 프로그램 등을 구동하는 container 구동 환경 그 자체라고 할 수 있습니다. docker 를 사용하게 되면 처음으로 어떤 이미지를 다운로드 받고(pull), 이를 구동(run) 하는 실습이 흔히 이루어집니다. 

조금 더 발전시켜서 진행한다면 특정 이미지에서 일부분을 수정하여 나만의 이미지를 만들게 됩니다. 다운로드 받은 이미지의 특정 부분을 수정하는 방법이 있을 수 있고, Dockerfile 이라는 이미지 빌드용 파일을 수정하여 커스텀 이미지를 생성할 수 있을 것입니다. 

다운받은 이미지에서 일부를 수정하는 방식은 대게의 경우 <mark style='background-color: #fff5b1'>netty</mark>와 같은 경량 WAS를 다운로드 받고 `index.html` 파일을 수정하여 변경된 사항이 적용된 이미지를 또 하나의 별도 이미지로 만듭니다. 

그렇지 않고 Dockerfile 을 수정하는 경우, 이미 작성해놓은 `index.html` 파일을 복사하게 `COPY` 구문을 사용하여 복사하게 하고 이를 빌드, 구동하게 할 것입니다. 

다 만들고 난 이미지는 어떻게 할까요?

다른 사람에게 공유하거나, 혹은 다음에 설명할 kubernetes 와 같이 다중화 솔루션을 구동하는 데 사용하기 위해서 이미지를 `hub`에 업로드하게 됩니다. 이렇게 이미지가 접근 가능한 어떠한 곳에 위치해야 필요한 시점(scale-out)에 다운로드 받아서 확장하여 사용할 수 있을 것입니다. 그렇다면 이미지를 업로드하게 되는 그  <mark style='background-color: #ffdce0'>어떤 곳</mark>은 무엇일까요?

기본적으로는 아미지를 생성하여 업로드하는 곳으로 docker 공식 registry 를 사용하게 됩니다. 우리가 `hub.docker.com`을 통해서 검색하고 다운로드 받는 모든 이미지들은 이 공식 registry 에 업로드되어 있습니다. 

그런데, Public network를 사용할 수 없는 상황, 즉 private cloud 를 구축해서 사용하는 경우이거나, 혹은 gitlab 같은 CI/CD 를 별도로 구축해서 사용하는 경우에는 private repository 를 사용해야 하는 경우가 생기고, 이럴 때에는 구동 환경, 즉 kubernetes 가 바라보는 registry 경로를 다르게 설정해야 합니다.

<br/>
  
## 개요

여기서는 단순히 docker registry 를 변경하는 방법만을 다루지는 않습니다. 그 과정에 있었던 개발 과정 중, 도움이 될 만한 것들 까지도 다 정리를 해 두겠습니다. kubernetes 를 처음 사용한다면, 아래 설명에서 이야기하는 것들을 모두 docker-compose 라고 생각해도 무방할 것입니다. 다만 문법이 조금씩 상이하기 때문에 조금은 어색하게 느껴질 수도 있으니, 문법에 맞게 생각해야 합니다. 

또한 docker-compose 조차도 생소하다면 이 부분에 대해서는 다른 글을 조금 검색해 보시고 오시기를 권해 드립니다. 몰라도 그만인 부분이라고 할 수 있지만, docker, container, kubernetes 를 학습하는 데 도움이 되는 부분이고 자주 사용하게 되므로 짚고 넘어가는 것이 좋습니다.

kubernetes 에 대한 글을 적으면서 반복적으로 설명하겠지만, kubernetes 에서 동일한 역할을 하는 pod, node 등의 집합은 동일한 namespace 로 관리해야 편합니다. deployment, service 등을 생성할 때에 namespace 를 별도로 설정하지 않으면 kubernetes 에 있는 default namespace를 사용하게 됩니다. 만약 하나의 집단만을 관리할 것이라면 이렇게 설정한 채로 default namespace를 그대로 사용해도 동작에는 지장이 없겠지만, 유지보수 측면이나 다른 사람이 관리하는 것을 감안한다면 별도의 namespace를 생성해서 관리해 주는 것이 좋습니다. 

<br/>

#### namespace 생성

다음 명령어로 namespace를 생성합니다. 이 이름이 모든 서비스들에 적용될 것이므로, 적절한 형태로 지어주는 것이 좋습니다.  

```bash
kubectl create namespace mynamespace
```
이렇게 해서 `mynamespace`라는 이름으로 namespace를 생성해 주고, 앞으로 생성되는 모든 요소에 metadata 부분에 `namespace : mynamespace`를 추가해 줍니다.

<br/>

## secret 생성

특정 docker registry 에 접근하게 하려면 우선 kubernetes 의 요소 중에서 secret을 생성해 주어야 합니다. 이것이 secret 이라고 불려지는 이유는, id / password 등을 이용해서 특정 docker registry 에 로그인하는 정보를 담고 있기 때문입니다. 

여기서는 단순히 id / password를 이용해서 로그인 하는 것으로 예시를 들었습니다. 실제 환경에서는 password 대신에 github / gitlab 등에서 발생하는 **token** 등으로 대체하여야 보안 문제를 해소할 수 있습니다. 
{: .notice--warning}


```bash
kubectl create secret registry-credentials docker-registry-login --namespace=mynamespace --docker-server=<server-address>:<port> --docker-username={아이디} --docker-password={패스워드} --docker-email={이메일}
```    

<br/>

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

<br/>

## 참고

아래는 참고할 만 한 내용들입니다. 

<br/>

#### namespace 삭제 방법

```bash
kubectl delete namespace mynamespace
```

<br/>

#### secret 삭제 방법

```bash
kubectl delete secret docker-registry-login -n mynamespace
```



