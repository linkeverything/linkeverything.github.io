---
title: "Spring Boot 로 API 생성하기"
categories: [study, springboot]
tag: [create api]
last_modified_at: 2020-12-23
---
Spring Boot를 이용하여 프로그램을 개발하는 데 있어서 API를 개발하는 것은 당연한 부분입니다. 다른 시스템과의 Communication목적이거나, 혹은 화면(front-end)을 개발하고 그 화면에서 호출할 API를 제공하는 목적으로도 API개발은 필수적입니다. 물론 Spring Boot를 이용하여 스스로 동작하는, 배치 서비스 같은 시스템 개발을 할 수도 있습니다.

사실 제가 앱 개발을 한창 하던 시절에도, 서버 측 개발을 병행하지 않으면 Open API 정도만 사용할 수 있고, 사용자 설정을 저장/백업 하거나, 특별한 기능을 구현하는 등에서 많은 제약이 따라오는 것 같아서 Back-end 개발 쪽으로 관심을 돌리게 된 것도 있습니다. 이제는 Front-end 보다는, Back-end 쪽에 더 가까워져 있습니다만, 이 경우처럼 API 의 개발은 시스템에서 아주 중요한 부분이고, 이 글을 보는 대부분의 사람들이 이러한 목적을 가지고 있을 것입니다. 

예를 들어, 게시판 기능을 가진 시스템이라고 할 경우, 테이블에 저장되어 있는 게시물 목록을 조회하고, 새 글을 작성하고, 글을 수정하고, 글을 삭제하는 등의 일련의 작업들이 다 화면에서 호출하는 API를 통해서 이루어집니다. 물론 JSP 등을 이용해서 API를 사용하지 않고, 화면 내에서 모든 기능들을 다 구현하고 동작하게 할 수도 있겠지만, 확장성이나 개발, 유지보수 측면에서는 별도로 분리하는 것을 추천합니다.

여기서는 API를 개발하고 동작하게 하는 것에 초점을 두고 진행합니다. 실제로 API를 하나 만들게 되면 이정도 수준에서 멈추는 것이 아니라 더욱 많은 작업들이 있겠지만, 그런 부분은 향후에 다른 것들과 더불어서 작업하도록 하겠습니다.

[Download Sample code from GitHub](https://github.com/linkeverything/studySpringBoot/tree/0010-create-api){:target="_blank" .btn .btn--primary}

> 위 경로에서 다운로드 받아서 진행하는 경우, branch 를 반드시 확인하시고 다운로드(checkout) 받으시기 바랍니다. 각 게시글에 맞는 branch 로 생성되어 있으므로 해당 branch를 받는 것이 이해해 도움이 됩니다.

## 필요한 라이브러리 

API를 생성하는 것을 이야기할 때, API는 http request에 대해서만 이야기하겠습니다. 다른 형태의 요청을 처리하는 back-end server를 생성할 수 있고, 각 생성방법이 다양하게 있지만, 여기서는 spring boot 에서 가장 일반적으로 사용하는 http request에 대해서만 논하고 기회가 된다면 다른 게시물을 통해서 다른 요청에 대한 처리도 담아 보도록 하겠습니다. 

#### 프로젝트 실행

앞서 생성한 프로젝트 파일의 실행은 IntelliJ IDEA 기준으로 우측의 *Gradle > tasks > application > bootRun* 을 더블클릭하는 것으로 할 수 있습니다. 물론 `~Application.java` 파일에서 우클릭하고 `main()` 을 실행하도록 해도 동일합니다만, gradle 실행법을 기본으로 가져가려고 합니다.

![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 AM 11.26.34.png)

현재 프로젝트에서 위 처럼 실행해 보면, 아래와 같이 실행하고 바로 종료되는 것을 확인할 수 있습니다. 당연히 추가로 아무런 코딩을 하지 않았기 때문에 기본 클래스가 실행되었고, spring boot 라이브러리가 포함되었지만, 아무런 동작 없이 종료된 것을 볼 수 있습니다.

![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 PM 12.50.08.png)

#### build.gradle 파일 수정

gradle 프로젝트를 생성하면 `build.gradle` 파일이, maven 프로젝트를 생성하면 `pom.xml` 파일이 해당 프로젝트의 구조를 설계하는 파일이 됩니다. 여기서 말하는 구조란, 빌드에 필요한 이름, 버전 명 등의 여러 property 값들을 비롯하여 빌드를 어떤 형태로 할 지, 무슨 플러그인으로 빌드할지에 대한 정보들을 이야기합니다. 또한 이 프로젝트가 구동되기 위해서 필요한 라이브러리들을 명시해 줌으로써 gradle/maven 빌드 시점에 원격으로 어떤 라이브러리를 다운로드 받아 설치되고 함께 빌드되어야 하는지도 명시합니다.

`build.gradle` 파일을 열고, `dependencies` 부분에 다음의 내용을 추가해 줍니다. 

```
implementation 'org.springframework.boot:spring-boot-starter-web'
```

사실 처음부터 이 라이브러리를 사용하겠다고 한다면 `start.spring.io` 에서 바로 추가하고 시작해도 무방합니다(동일한 결과). [^1]

#### 다시 실행해 보기

이제 라이브러리를 추가했으면 앞서 실행한 것 처럼 `bootRun` 을 실행해 봅니다. 앞서 실행한 것 과는 다르게, 실행한 프로그램이 그대로 실행중인 상태로 머물로 있습니다. 소위 이야기하는 <mark style='background-color: #fff5b1'>서버</mark>가 실행되어 서비스 형태로 실행중인 채 남아있으며 우리가 원하는 상태가 되었습니다. 

![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 PM 12.56.23.png)

실질적으로 <mark style='background-color: #fff5b1'>API 서버</mark>라고 하는 것들 역시 대기 상태로 있으면서, 주기적으로 어떠한 요청이 들어오는지 빠르게 loop를 돌면서 확인하고, 뭔가 요청이 있으면 그에 따른 동작을 하는 형태라고 생각하면 됩니다. 위 실행 로그를 보면 `8080` 포트에 대해서 서버가 실행중임을 알 수 있습니다. 

이렇게 Spring boot에서는 라이브러리만 추가하고 아무런 작업을 해주지 않아도, 서버 형태로 동작하는 것을 알 수 있습니다. 포트번호는 앞서 살펴본 바와 같이 `8080`이 기본값으로 동작하고 있으며, 이는 내장 Tomcat이라는 형태의 서버임을 확인할 수 있습니다. 

## API 설계

## Open API에 대해

## API 설계에 대한 몇 가지

## 참고자료 및 출처

- <https://start.spring.io/>

[^1]: start.spring.io 페이지에서 우측 dependencies 부분에 'web' 으로 검색하여 추가하면 됩니다.