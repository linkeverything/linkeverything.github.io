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

[Download Sample code from GitHub](https://github.com/linkeverything/studySpringBoot/tree/0003-create-project){:target="_blank" .btn .btn--primary}

## Spring Initializr를 활용하기

Spring Initializr는 Spring 측에서 제공하는 웹 페이지입니다. 간단히 Spring Boot Project를 구성할 수 있고 그 틀을 다운로드 할 수 있습니다. 

#### 사이트 이용하기

[Spring Initializr](https://start.spring.io/)의 주소는 `https://start.spring.io/` 입니다. 이 주소를 브라우저의 주소표시줄에 넣고 화면을 불러옵니다. 

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-23 PM 11.48.37.png)

이제 차례대로 선택하면서 프로젝트를 생성해 보겠습니다. 

- Project : `Gradle Project`

  여기서는 Gradle Project를 생성합니다. Maven으로 해도 무방하나, 최근 Gradle을 많이 사용하시는 것 같아 저 역시 예시를 Gradle로 하겠습니다. 

- Language : `Java`

  언어는 Java / Kotlin / Groovy 를 지원하는 것으로 보입니다. 가장 일반적인 형태인 Java를 선택하겠습니다. 

- Spring Boot : `2.4.0`

  현재 이 글을 쓰는 시점 기준으로 하여 기본값이 2.4.0 입니다. 그대로 사용하겠습니다. (앞서 여러 글을 적을 당시만 하더라도 2.3.6을 자주 사용했던 것 같은데 그새 버전이 조금 올라갔습니다. 만약 향후에 호환성 문제가 없다면 프로젝트 생성 이후에도 변경할 수 있는 부분이니 크게 걱정하지 않아도 됩니다.)

- Project Metadata

  - Group : `com.simplify`

    여기는 개발하는 프로젝트의 소속(?)을 입력합니다. 대게의 경우에 대형 프로젝트이고 그 프로젝트 자체가 의미를 갖는 경우, 그 이름을 그대로 적기도 합니다만, 그렇지 않은 일반적인 경우에는 회사 명이 이를 대체하기도 합니다. _`aaa.com` 과 같은 형태의 URL을 뒤집어 놓은 것 같은 느낌이다.._ 정도로 생각하면 됩니다.

  - Artifact / Name : `studySpringBoot`

    여기서 주어진 이름이 향후 결과물과도 연관이 있습니다. 보통의 경우 빌드의 경과물 이름을 의미하기도 하는 Artifact 는 역시나 입력하게 되면 아래 Name 부분에도 자동으로 입력이 됩니다. (거의 동일한 뜻으로 사용된다는 의미)

  - Description : `Study project for Spring Boot`

    여기서는 `Demo` 라는 글자만 `Study`라고 변경하여 그대로 사용하겠습니다. 

  - Package name : `com.simplify.studySpringBoot`

    역시 group 과 Artifact, name 을 적으면 자동 생성되는 부분입니다. 

  - Packaging : `Jar`

    `Jar`를 선택합니다. Tomcat 등 WAS에 올려서 구동할 것이 아니라면 Spring Boot 프로젝트는 내장 WAS를 그대로 사용하는 것이 편리합니다. 또한 나중에 설명하게 될 Docker Container 를 활용하는 경우에도 Jar로 빌드하여 Java로 실행하는 것이 훨씬 편하기 때문에 여기서는 `Jar`를 선택하겠습니다. 

  - Java : `8`

    11 정도도 최근에는 많이 사용하는 줄 알지만, 현재 대부분의 서버 프로젝트에서 1.8 정도까지는 지원하는 것으로 보고 최소 수준인 `JDK 1.8`을 사용하겠습니다. 물론 아직도 경우에 따라서는 1.6 같은 오래된 버전을 사용하기도 하지만 그렇게까지 하지는 않겠습니다. (여기에 Java 6 가 선택지가 없다는 것은, 내가 선택하고 만들려는 Spring Boot 프로젝트가 Java 8 이상만 지원하기 때문입니다^^)

여기까지 설정하고 나면 아래 그림과 같은 형태가 됩니다. 

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-30 PM 11.16.11.png){: .align-center}

아제 아래 `GENERATE` 버튼을 눌러 봅니다. 브라우져를 통해서 압축된 형태로 프로젝트가 다운로드 될 것입니다. 이제 이 프로젝트를 본인이 사용하는 IDE(IntelliJ, VSCode, Eclipse)에 import 하고 사용하면 됩니다.

#### IDE에 Import하기

앞서 이야기한 것 처럼, IDE는 IntelliJ를 사용하겠습니다. 거의 개념은 유사하니 본인이 사용하는 IDE에 맞춰서 사용법을 적절히 활용하면 됩니다.

우선 IntelliJ IDEA 를 실행합니다. 

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-30 PM 11.01.09.png)

첫 실행 안에서, `Open or Import`를 선택합니다. 그리고 앞서 Spring Initializr 를 이용하여 생성한 `zip` 파일을 선택하면, 아래와 같이 에러가 납니다.[^1] 다시 탐색기 혹은 파인더로 돌아와서 아까 생성했던 zip파일의 압축을 풀어줍니다. 그러면 Spring Boot 프로젝트 형태에 맞는, 폴더 구조로 된 하나의 패키지가 나오게 됩니다. 

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-30 PM 11.05.39.png)

이제 다시 `Open or Import`버튼을 눌러 선택기를 띄우고, <mark style='background-color: #ffdce0'>압축을 해제한 폴더를 선택</mark>하면 정상적으로 import 됩니다.

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-30 PM 11.17.53.png)

최초 실행하면 아래 그림처럼 조금 기다려줘야 합니다. 이 작업은 모든 IDE에서 동일한 작업이고, 필수 라이브러리들을 다운로드 받는 과정입니다. 여기서는 아마도 Spring Boot 관련 라이브러리들을 다운로드 받을 것이고(별도의 라이브러리는 추가하지 않았습니다), 조금 기다려 주고 대기 상태가 되면 됩니다.

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-30 PM 11.21.30.png)

다 완료가 되면, 아래 그림처럼, 프로젝트 구조가 보인다면 정상적으로 로드된(import된) 상태입니다. 

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-30 PM 11.23.57.png)

## Gradle 프로젝트를 수동으로 구성하기 (특수한 경우, 고급과정)

이 과정은 완전히 특수한 상황입니다. 기존에 사용하고 있던 프로젝트가 있다고 하더라도 이 방법을 굳이 사용하는 것을 권장하지는 않습니다. 다만 필요에 의해서든, 궁금증 때문이든, 이러한 원리로도 구성할 수 있다는 것을 참고로만 알아두시기 바랍니다. 

여기서는 IntelliJ IDEA 기준으로 설명합니다. 
{: .notice--info}

#### Gradle 프로젝트 생성하기

IntelliJ IDEA 에서 New Project를 실행하고, Gradle 프로젝트, Java 형태입을 아래와 같이 선택합니다. 나머지 정보들은 적당히 입력하여 프로젝트 생성을 마무리하고 IDE에 정상적으로 import된 것을 확인합니다.

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-12-01 AM 12.02.42.png)

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-12-01 AM 12.03.10.png)

#### Library import하기

이제 `build.gradle` 파일을 열어 아래와 같이 수정합니다. 

1. `plugins` 부분에 다음을 추가합니다. 

   ```
   id 'org.springframework.boot' version '2.4.0'
	 id 'io.spring.dependency-management' version '1.0.10.RELEASE'
   ```
2. dependencies 부분에 다음을 추가합니다.

   ```
   implementation 'org.springframework.boot:spring-boot-starter'
	 testImplementation 'org.springframework.boot:spring-boot-starter-test'
   ```

   만약 순수 Java 프로젝트에 맞는 아래 내용이 있다면 삭제합니다.

   ```
   testCompile group: 'junit', name: 'junit', version: '4.12'
   ```

3. gradle 을 업데이트 합니다. 

   ![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-12-01 AM 12.08.35.png)

#### Main Java 파일 생성하기

이제 `/src/main/java` 경로 하위에 적절한 이름으로 패키지를 생성하고, 그 하위에 Java파일을 `~Application.java` 와 같이 생성한 뒤 아래와 같이 main 함수를 입력해 줍니다. 중요한 것은 <mark style='background-color: #ffdce0'>@SpringBootApplication</mark> annotation을 반드시 추가해 주어야 합니다. 

```java
package com.sample;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SampleApplication {
    public static void main(String[] args) {
        SpringApplication.run(SampleApplication.class, args);
    }
}
```

#### properties 파일 추가하기

`/src/main/resources` 하위에 `application.properties` 파일을 생성합니다. (약속된 이름이므로 그대로 입력합니다.) 그리고 안에 내용은 지금은 입력하지 않아도 됩니다. 

## IntelliJ IDEA Ultimate 가 있다면?

유료 버전이고, 제가 사용하는 것이 어떠한 수익으로 연결되면 안되는 교육용 버전이기 때문에, 여기에 별도로 스크린샷을 붙이거나 하지는 않겠습니다. 

단지, IntelliJ IDEA Ultimate 버전을 사용할 수 있는 기회가 있다면 `웹에서 생성` &rarr; `IDE로 import` 과정을 `IDE에서 생성` 하나의 과정으로 마칠 수 있다는 장점이 있을 뿐 일반적인 개발을 하는 데 있어서 큰 차이점은 없습니다.(무료로도 충분히 사용할 만 합니다.)

## 실행하기

Spring Boot 초기 버전이 생성되었으니 이제 이것을 실행해 볼 차례입니다. 단축키도 있고 뭔가 실행 configuration을 만들어서 실행하는 방법도 있지만, 여기서는 쉽고 직관적으로 `java`파일의 `main` 함수를 실행해 보도록 하겠습니다.

조금 전 import 한 프로젝트에서 아래 경로에 있는 파일을 찾아갑니다.

```
/src/main/java/com.simplify.studySpringBoot/StudySpringBootApplication.java
```

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-30 PM 11.29.20.png)

그리고 위 그림과 같이 우클릭하고 `Run ... main()` 을 선택합니다. 실행하고 나면, 별도의 어떠한 처리도 하지 않은 Spring Boot 프로젝트이기 때문에 실행과 동시에 종료되는 것이 맞습니다. 아래 그림과 같이 console 창에 나타난다면 정상적인 상태입니다.

![](/assets/images/posts/study/springboot/2020-11-23-create-project/screenshot 2020-11-30 PM 11.32.25.png)

이 뒤부터는 <mark style='background-color: #dcffe4'>진짜 Backend Server 처럼 동작하게 하기 위해서</mark> 어떤 처리들을 거치고 어떻게 활용할 수 있는지 확인해보도록 하겠습니다.

## 참고자료 및 출처

- <https://start.spring.io/>

[^1]: Eclipse 와 다르게, IntelliJ IDEA는 압축 파일의 Import를 제공하지 않습니다. 