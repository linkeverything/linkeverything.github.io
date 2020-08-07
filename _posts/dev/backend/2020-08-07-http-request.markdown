---
title: "서버 간 http 통신하기 - Form / JSON"
excerpt: "How to send http request with Java(Spring) (with Form  or JSON parameter"
categories:
  - "dev"
  - "backend"
tag:
  - "java"
  - "http request"
last_modified_at: 2020-08-07
---

> Back-end 서버를 개발함에 있어, 보통은 client 의 요청(request)을 받아 특정 로직을 수행하고 그 결과를 보내(response)주지만, 시스템의 규모가 커질수록 서버 간 통신이 불가피하게 됩니다. 특히 시스템 초기에는 불필요햇으나, 서비스의 규모가 커지면서 시스템간 통신이 필요한 경우, 혹은 인증 시스템 등 공통 서비스가 생겨나면서 서버간 통신이 필요하게 됩니다. 여기서는 서버 간 통신을 위해 간단히 http request를 어떻게 구현하는지 살펴보겠습니다. 

편의상 Springboot 를 이용하여 요청과 응답을 모두 받을 것입니다. 내가 나 자신에게 요청을 보내는 것이지만, 두 개의 시스템이라면 서버 주소 등을 수정하여 적용 가능합니다.
{: .notice--warning}

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/httpReq){:target="_blank" .btn .btn--primary}

이 글에 사용된 프로젝트는 다음과 같습니다. 간혹 버전이 상이하여 다르게 동작한다는 분들이 있어, 아래 내용도 공유합니다.

![](/assets/images/2020-08-07-http-request/start.spring.io-2020.08.07-14_02_00.png)

<br/>

## 수신 서버 먼저 생성하기

우선 보내온 요청에 대해서 응답을 주는 쪽 먼저 개발을 진행하려고 합니다. 위에서 생성한 Springboot 프로젝트에 Controller 를 하나 생성하고 아래와 같이 작성하여 간단한 예제를 작성할 수 있습니다. 

아무런 기타 설정을 하지 않았기 때문에 `port`는 `8080` 을 사용합니다.
{: .notice--info}

```java
@Controller
public class ResponseController {

    @RequestMapping(value="/test")
    public @ResponseBody String test(){
        return "TEST";
    }
}
```

간단히 `http://localhost:8080/test`로 호출하면 문자열로 TEST라는 것을 return 하도록 작성했습니다. 이제 이 Controller 를 기반으로 해서 뒤에 나오는 여러 request 들을 처리할 예정입니다.

<br/>

## 참고자료 및 출처

- https://yarbong.tistory.com/69