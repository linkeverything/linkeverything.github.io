---
title: "알아두면 유용한 jQuery selectors"
excerpt: "Some useful jQuery selectors those are useful in development"
categories:
  - "dev"
  - "frontend"
tag:
  - "jquery"
  - "selector"
last_modified_at: 2020-07-10
---

jQuery selector 라고 하면 보통은 ```#```으로 아이디를 검색하거나 ```.```으로 class를 검색하는 것으로 많이 사용합니다. 그런데 조금만 더 높은 수준의 화면을 개발하려면 그에 걸맞는 selector 가 필요합니다. 여기서는 잘 모르는 사람이 많지만, 알아두면 유용한 몇 가지 selector들을 정리해서 소개합니다. 

작성 기준일인 **2020년 7월**현재 [jQuery 홈페이지](https://api.jquery.com/category/selectors/)에 올라와 있는 내용을 바탕으로 정리합니다. 
{: .notice--info}
  
## 개요

kubernetes 에 대한 글을 적으면서 반복적으로 설명하겠지만, kubernetes 에서 동일한 역할을 하는 pod, node 등의 집합은 동일한 namespace 로 관리해야 편합니다. deplo

#### namespace 생성

다음 명령어로 namespace를 생성합니다. 이 이름이 모든 서비스들에 적용될 것이므로, 적절한 형태로 지어주는 것이 좋습니다.  

```bash
kubectl create namespace mynamespace
```
이렇게 