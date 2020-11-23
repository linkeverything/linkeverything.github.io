---
title: "Spring Boot 프로젝트 시작하기"
categories: [study, springboot]
tag: [initializr, gradle]
last_modified_at: 2020-11-23
---

Spring Boot 를 기반으로 한 프로젝트를 생성하는 것 부터 시작하겠습니다. 개발을 업무로 진행하시는 분들 중에서도 이 과정에 대해서 의외로 익숙하지 않은 분들이 더러 있습니다. 저 역시 대형 프로젝트의 경우에는 이런 과정을 거치기 보다는 S/W Architect라는 Role 을 갖춘 분이 프로젝트의 전체적인 구성을 잡아줬던 적이 많았습니다. 그렇지만 크기가 작거나, 특히 요즘 한창 화두가 되고 있는 MSA(Micro-Service Architecture)방식으로 개발을 진행하려면 한번 쯤은 짚고 넘어가는 것이 좋겠습니다. 

어쩌면 이 글을 적고 나서, 조금 시간이 지난 뒤에 더 편리한 도구(Tool)가 나오거나, 혹은 특정 IDE에서 이 기능을 대신해 줄 지도 모르겠습니다. 그렇게 되더라도 어떤 식으로 구성이 되는지에 대해서는 간단하게나마 살펴보고 지나가는 것이 좋을 것 같습니다. 

Spring, Spring Boot 모두 다 특이한 것은 아닙니다. 심지어는 과거 Eclipse에서 부르던 Dynamic Web Project역시 크게 특별한 Java 프로젝트는 아닙니다. 이러한 프로젝트들이 IDE상에서 특별하게 보이는 것은 <mark style='background-color: #ffdce0'>약속된 형태의 구조로 프로젝트의 뼈대가 만들어져 있기 때문</mark>입니다. 

예를 들어, Spring Boot 는 특정 라이브러리들 몇 개가 포함되어 있습니다. 또한 하나의 패키지 경로 아래에 `~Application.java` 파일이 존재하고, `resource`폴더 하위에는 `.properties` 혹은 `.yml(yaml)`파일이 있습니다. 이 외에도 여러가지 특징들이 있는데, 이러한 특징을 갖는 프로젝트를 IDE에서 import 하면, _"아 이 프로젝트는 Spring Boot 프로젝트구나!"_ 라고 인식하는 것입니다. 

실제로, STS(Spring Tool Suite)에 포함된 Spring Plug-in이나, IntelliJ IDEA 에 포함된 Spring 관련 플러그인 들이 이러한 부분들을 파악하고, 이를 Spring, 혹은 Spring Boot 프로젝트로 보여줍니다. 이러한 원리를 알고 있다면 누구나 쉽게 프로젝트를 생성할 수 있습니다. 

여기서는 다음 두 가지 방법을 소개하려고 합니다. 

- Spring Initializr를 이용하는 방법
- Gradle 프로젝트를 Spring Boot로 변경하는 방법

두 방법 중에서 편한 방법을 취사선택하여 사용하면 되겠습니다. 

## Spring Initializr를 활용하기

Spring Initializr는 Spring 측에서 제공하는 웹 페이지입니다. 간단히 Spring Boot Project를 구성할 수 있고 그 틀을 다운로드 할 수 있습니다. 

#### 사이트 이용하기

[Spring Initializr](https://start.spring.io/)의 주소는 `https://start.spring.io/` 입니다. 이 주소를 브라우저의 주소표시줄에 넣고 화면을 불러옵니다. 

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-23 PM 11.48.37.png)

#### IDE에 Import하기

## Gradle 프로젝트를 수동으로 구성하기

#### Gradle 프로젝트 생성하기

#### Library import하기

#### Main Java 파일 생성하기

#### properties 파일 추가하기

## IntelliJ IDEA Ultimate 가 있다면?

## 참고자료 및 출처

- 

