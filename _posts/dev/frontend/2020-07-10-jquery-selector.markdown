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

jQuery selector 라고 하면 보통은 ```#```으로 아이디를 검색하거나 ```.```으로 class를 검색하는 것으로 많이 사용합니다. 그런데 조금만 더 높은 수준의 화면을 개발하려면 그에 걸맞는 selector 가 필요합니다. 여기서는 잘 모르는 사람이 많지만, 알아두면 유용한 몇 가지 selector들을 정리해서 소개합니다. 기본적인 id selector와 class selector는 소개하지 않겠습니다.

작성 기준일인 **2020년 7월** 현재 [jQuery 홈페이지](https://api.jquery.com/category/selectors/)에 올라와 있는 내용을 바탕으로 정리합니다. 
{: .notice--info}
  
## Attribute 관련 selectors

특정 속성 값을 기준으로 하여 선택하는 것은 아주 흔하고 많이 사용되는 방식입니다. 

| 사용법 | 기능 | 예시 |
|------|------|----|
|```[name|="value"]```|속성의 값이 ```value```로 시작|```$( "a[hreflang|='en']" )```|


#### namespace 생성

다음 명령어로 namespace를 생성합니다. 이 이름이 모든 서비스들에 적용될 것이므로, 적절한 형태로 지어주는 것이 좋습니다.  

```bash
kubectl create namespace mynamespace
```
이렇게 