---
title: "카카오톡 로그인 구현하기 - (2) Spring 샘플 구현"
categories: [springboot]
tag: [spring boot, kakao login]
last_modified_at: 2020-07-23
---
웹이든 앱이든 서비스를 개발하는 데 있어서 SSO(Single Sign On) 의 필요성이 계속 화두가 되는 것 같습니다. 이제 **기존 시스템** 이라는 것이 거의 다 있게 마련이고, 따라서 기존에 사용하던 로그인정보를 그대로 유지해 달라고 하거나, 더 나아가서 session 도 유지해 달라는 요구사항이 더러 있습니다. 다양한 방식의 SSO 를 구현할 수 있고, 프로그램도 많이 있지만, B2C 서비스에서 필수라고 할 수 있는 카카오 계정 로그인 방식에 대해서 설명해 보고자 합니다. 

시리즈로 작성될 것으로 예상되는 이 글은, 맨 아래에 있는 출처의 글을 상당 부분 참고했음을 밝힙니다. 많은 도움이 되었고, 저는 과정에 충실하게 정리하였습니다. 해당 글에는 개념 설명도 더러 해 놓으셨으니 가서 한번 보세요^^
{: .notice--info}

[Download Sample code from GitHub](https://github.com/simpl-ify/SampleProjects/tree/master/kakaoLogin){:target="_blank" .btn .btn--primary}

## Spring project 생성

#### Spring Initializr 를 이용한 프로젝트 생성

[Spring Initializr](https://start.spring.io/) 에 접속하여 아래 화면과 같이 생성을 해줍니다. Group, Artifact, Name, Description 등은 본인 환경에 맞게 잡아주면 됩니다. 그리고 dependency 에 web 으로 검색해서 그림과 같이 추가해 줍니다.

![](/assets/images/posts/dev/backend/2020-07-23-kakao-login-2/start.spring.io-2020.07.23-11_26_38.png)

아래 Generate 버튼을 누르고, 다운로드된 zip 파일을 IntelliJ 등 IDE 에 import 합니다.

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

#### 이미지 준비

로그인 버튼에 사용되는 이미지는 없어도 그만이긴 합니다만, 그래도 카카오 로그인에 맞춰 공식 이미지를 다운로드 받아 사용해 보겠습니다. [카카오 리소스 다운로드 페이지](https://developers.kakao.com/tool/resource/login) 에 이동하여 다운로드 받고, 이 파일을 `src/main/resources/static` 경로 하위에 위치시킵니다. 이 위치를 사용하는 이유는, 화면 resource 에서 접근할 때에 저 위치까지를 기본 위치로 잡고, 그 하위 경로를 이용할 것이기 때문입니다. 

#### 로그인 화면 개발하기

화면도 기본 설정되어 있는 화면 리소스 위치를 기본으로 사용하겠습니다. Spring project 에 능숙한 분들이라면 원하시는 경로에 놓고 사용하셔도 무관합니다. 

저는 `src/main/sebapp/WEB-INF/views` 하위에 `index.jsp` 파일을 만들어 사용해 보려고 합니다. 

![](/assets/images/posts/dev/backend/2020-07-23-kakao-login-2/screenCapture.png){: .align-center}

이렇게 파일을 생성해 주고, 다음 내용을 입력합니다. 

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Insert title here</title>
</head>
<body>
    <c:if test="${userId eq null}">
        <a href="https://kauth.kakao.com/oauth/authorize?client_id={REST API 키}&redirect_uri=http://localhost:8080/login&response_type=code">
            <img src="/img/kakao_login/ko/kakao_login_medium_wide.png">
        </a>
    </c:if>
    <c:if test="${userId ne null}">
        <h1>로그인 성공입니다</h1>
    </c:if>
</body>
</html>
```

위 내용을 그대로 붙여넣엇다면 챙겨야 할 부분이 몇 가지 있습니다. 
- client_id 부분의 값은 본인이 등록한 `REST API 키` 값으로 변경해 줍니다. 
- `img` 태그의 이미지 경로는 본인이 다운로드 받은 경로를 입력해 줍니다. 

#### application.properties 파일 수정하기

우선 화면이 잘 나오는지 확인을 해봐야 할 텐데, 그 이전에 설정을 몇 가지 잡아주도록 하겠습니다. `application.properties` 파일을 열어 아래 내용을 추가해 줍니다. 

아래 두 개는 jsp 를 제대로 사용하기 위한 경로 값이고, 위의 서버 `port` 는 웹 서비스 할 포트 번호입니다. (서버 포트 번호는 기본값이 **8080**인 것으로 알고 있습니다만, 명시적으로 하기 위해 적어주었습니다.)

```properties
server.port=8080
 
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```

#### controller 생성하기

이제 Controller를 생성해 보도록 하겠습니다. 저는 아래 경로에 파일을 생성하고 다음 내용을 입력하였습니다. 흐름으로 살펴보자면, 사용자가 `카카오 로그인` 버튼을 클릭하게 되면 카카오 측 로그인 화면으로 이동하고, 로그인이 완료되면 `redirect uri` 인 `localhost:8080/login` 으로 보내지게 됩니다. 그러면 controller 에서 `/index.jsp` 로 View를 이동시키고, 브라우저에서는 이 때 jsp 에서 사용자 정보가 있는지를 확인하고 있으면 **로그인 성공입니다** 라는 메시지를 보여주게 됩니다. 

![](/assets/images/posts/dev/backend/2020-07-23-kakao-login-2/screenCapture2.png){: .align-center}

```java
package com.simplify.kakaoLogin.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HomeController {

    @RequestMapping(value="/")
    public String index() {
        return "index";
    }

    @RequestMapping(value="/login")
    public String login() {
        return "index";
    }
}
```

## 실행 및 결과 확인

이제 기본 틀은 다 생성한 샘입니다. 프로젝트의 java 파일 중 최상위 파일 `*Application.java` 파일을 우클릭하고 `main()` 을 실행합니다. 아래처럼 로그인 버튼이 나타나면 우선 jsp 연동은 잘 되어있는 것입니다. 

![](/assets/images/posts/dev/backend/2020-07-23-kakao-login-2/screenCapture3.png){: .align-center}

여기서 저 **카카오 로그인** 버튼을 눌러 본인의 카카오톡 아이디로 로그인을 하면 다시 `localhost:8080/login` 으로 redirection 되는 것을 볼 수 있습니다. 

![](/assets/images/posts/dev/backend/2020-07-23-kakao-login-2/screenCapture4.png){: .align-center}

위 이미지에서 **로그아웃** 부분은 무시하세요. 다음에서 설명할 내용입니다.

## 참고자료 및 출처

- <https://antdev.tistory.com/34>