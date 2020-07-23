---
title: "카카오톡 로그인 구현하기 - (2) Spring 샘플 구현"
excerpt: "How to login with Kakao account - (2) Develop Spring sample project"
categories:
  - "dev"
  - "backend"
tag:
  - "spring"
  - "kakao login"
last_modified_at: 2020-07-23
---

> 웹이든 앱이든 서비스를 개발하는 데 있어서 SSO(Single Sign On) 의 필요성이 계속 화두가 되는 것 같습니다. 이제 **기존 시스템** 이라는 것이 거의 다 있게 마련이고, 따라서 기존에 사용하던 로그인정보를 그대로 유지해 달라고 하거나, 더 나아가서 session 도 유지해 달라는 요구사항이 더러 있습니다. 다양한 방식의 SSO 를 구현할 수 있고, 프로그램도 많이 있지만, B2C 서비스에서 필수라고 할 수 있는 카카오 계정 로그인 방식에 대해서 설명해 보고자 합니다. 

시리즈로 작성될 것으로 예상되는 이 글은, 맨 아래에 있는 출처의 글을 상당 부분 참고했음을 밝힙니다. 많은 도움이 되었고, 저는 과정에 충실하게 정리하였습니다. 해당 글에는 개념 설명도 더러 해 놓으셨으니 가서 한번 보세요^^
{: .notice--info}

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/kakaoLogin){:target="_blank" .btn .btn--primary}

<br/>

## Spring project 생성

<br/>

#### Spring Initializr 를 이용한 프로젝트 생성

[Spring Initializr](https://start.spring.io/) 에 접속하여 아래 화면과 같이 생성을 해줍니다. Group, Artifact, Name, Description 등은 본인 환경에 맞게 잡아주면 됩니다.

![](/assets/images/2020-07-23-kakao-login-2/start.spring.io-2020.07.23-11_26_38.png)



## 참고자료 및 출처

https://antdev.tistory.com/34