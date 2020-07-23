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

[Spring Initializr](https://start.spring.io/) 에 접속하여 아래 화면과 같이 생성을 해줍니다. Group, Artifact, Name, Description 등은 본인 환경에 맞게 잡아주면 됩니다. 그리고 dependency 에 web 으로 검색해서 그림과 같이 추가해 줍니다.

![](/assets/images/2020-07-23-kakao-login-2/start.spring.io-2020.07.23-11_26_38.png)

아래 Generate 버튼을 누르고, 다운로드된 zip 파일을 IntelliJ 등 IDE 에 import 합니다.

<br/>

#### dependency 추가

프로젝트가 import 되면 pom.xml 파일을 열어 dependency를 추가해 줍니다. jsp 파일로 화면을 간단하게 구현할 것이기 때문에 tomcat-embed-jasper 와 jstl 라이브러리를 추가해 줍니다.

```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>
<dependency>
    <groupId>jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

<br/>

#### 이미지 준비

로그인 버튼에 사용되는 이미지는 없어도 그만이긴 합니다만, 그래도 카카오 로그인에 맞춰 공식 이미지를 다운로드 받아 사용해 보겠습니다. [카카오 리소스 다운로드 페이지](https://developers.kakao.com/tool/resource/login) 에 이동하여 다운로드 받고, 이 파일을 `src/main/resources/static` 경로 하위에 위치시킵니다. 이 위치를 사용하는 이유는, 화면 resource 에서 접근할 때에 저 위치까지를 기본 위치로 잡고, 그 하위 경로를 이용할 것이기 때문입니다. 

<br/>

#### 로그인 화면 개발하기

화면도 기본 설정되어 있는 화면 리소스 위치를 기본으로 사용하겠습니다. Spring project 에 능숙한 분들이라면 원하시는 경로에 놓고 사용하셔도 무관합니다. 

저는 `src/main/sebapp/WEB-INF/views` 하위에 index.jsp 파일을 만들어 사용해 보려고 합니다. 

![](/assets/images/2020-07-23-kakao-login-2/screenCapture.png){: .align-center}





## 참고자료 및 출처

https://antdev.tistory.com/34