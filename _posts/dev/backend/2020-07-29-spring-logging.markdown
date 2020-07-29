---
title: "Springboot 에서 logback 사용하기"
excerpt: "How to use logback in Springboot"
categories:
  - "dev"
  - "backend"
tag:
  - "spring"
  - "logback"
last_modified_at: 2020-07-29
---

> backend나 frontend나 프로그램 개발에 있어, logging 처리는 아주 중요한 부분입니다. 특별히 truble-shooting을 위한 목적이 아니라고 하더라도 시스템의 운영 층면에서 필요한 부분이므로 아래와 같이 정리합니다.

다양한 방법, 각자 선호하는 방법이 있겠지만, 여기서는 Springboot 환경에서 가장 간단하게 접근할 수 있는 logging 처리인 **logback** 에 대해서 살펴봅니다. 
{: .notice--info}

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/logback){:target="_blank" .btn .btn--primary}

<br/>

## 기본사항

<br/>

#### Springboot 생성

Springboot 프로젝트는 단순히 [Spring Initializr]()를 이용해서 생성하면 됩니다. 해당 페이지에서 아래와 같이 설정값을 적어주고 `GENERATE` 버튼을 눌러 다운로드 받고, 이를 IntelliJ 등 IDE 환경으로 Import 하면 됩니다.

![](/assets/images/2020-07-29-spring-logging/start.spring.io-2020.07.29-18_04_14.png){: .align-center}

#### logback

Springboot 에는 기본적으로 logback 이 포함되어 있습니다. 따라서 Springboot 의 `*Application.java` 파일의 `main()`함수를 호출하여 실행할 떄, IDE의 console 창에 나타나는 내용이 그것입니다. 

![](/assets/images/2020-07-29-spring-logging/screenCapture.png){: .align-center}

<br/>

## 참고자료 및 출처

- https://romeoh.tistory.com/entry/Spring-Boot-Logback-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0
- https://supawer0728.github.io/2018/04/07/spring-boot-logging/
- https://dailyheumsi.tistory.com/173