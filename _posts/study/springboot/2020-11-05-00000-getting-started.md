---
title: "Spring Study 시작하며"
categories: [Study, Spring Boot]
tag: [spring boot]
last_modified_at: 2020-11-05
---

Spring Boot 에 대한 Study를 진행하면서 얻게 된 정보들, 그리고 저와 동일하게 공부를 시작하려는 분들에게 도움이 되고자, study 섹션으로 분리하여 정리하려고 합니다. 워낙 쉽게 개발 및 배포가 가능하도록 되어 있어 그런지, Spring Boot, Spring Framework에 대한 기본적인 지식 없이, Web 검색을 통해 얻어온 정보들을 정확하게 정리하고 스스로도 공부가 되었으면 합니다. 

## 기본 사항

#### 소스 코드

예제 등을 개발하면서 공유하려고 하고 있으며 모든 정보들은 이 [linkeverything GitHub Organization](https://github.com/linkeverything) 안에 있는 별도의 repository인 [studySpringBoot](https://github.com/linkeverything/studySpringBoot)로 관리하려고 합니다. 최초 시작부터 하여 모든 과정들은 branch 로 분리하여 각 과정의 시작부터 끝까지를 관리해보려고 합니다(이 정책은 향후 변경될 수 있습니다).

#### 출처 및 참고자료

모든 출처, 참고자료 들에 대해서는 그 원본 링크를 첨부하거나, 도서인 경우 어떤 도서에서 참고하였는지 명시합니다. 저작권 침해가 없는 수준에서 참고 및 자료 활용이 이루어질 예정입니다. 

## 사전 정의(Pre-defined)

여기에는 다음의 정보들을 기본으로 가지고 가겠습니다. 중간에 필요에 의해서 다른 방식을 참고할 수는 있겠지만, 큰 틀은 이 룰을 유지합니다.

#### 소스 및 라이브러리

모든 라이브러리는 작성하는 시점 기준으로 최신 소스를 반영합니다. 만약 Spring Boot나 사용하는 라이브러리의 업데이트가 발생한 경우, 정상적으로 동작하지 않을 수 있습니다.[^1] 

#### 빌드 도구

빌드 도구는 크게 Maven 과 Gradle이 있습니다. 그 중에서 Gradle을 선정합니다. 더 오랫동안 사용해 온 것은 Maven이고 관련 자료들도 많이 있겠지만, 현재 추세에 맞게 gradle로 선정하였습니다. 두 빌드 도구 간에 유사성이 많고 호환성을 갖을 수도 있으니 maven을 사용하는 환경이더라도 여기에 남겨진 내용으로 유사하게 구현할 수 있을 것입니다. 

#### IntelliJ IDEA

이 포스팅 전체에서 사용하는 IDE는 IntelliJ IDEA Community edition을 기준으로 합니다. 무료 툴이고 현재 기준으로 가장 많은 개발자가 사용하는 것이라고 판단되어 선정하게 되었습니다. 

다만 버전에 대해서는 특정지을 수 없음을 밝힙니다. IntelliJ와 Eclipse동일하게 매 분기마다 버전을 빌드하는 것을 규칙으로 가져가고 있으며 따라서 제가 이 글을 적는 동안에도 버전이 올라갈 수 있습니다. 

개인적으로 IDE의 업데이트는 바로바로 진행하는 편입니다. 자동화된 기능, 개발 편의성을 제공해주는 것인 만큼, IDE의 버그로 인한 문제는 없기를 바라는 마음에 그렇게 합니다.

- IntelliJ 홈페이지 : <https://www.jetbrains.com/idea/>

#### Java(JDK)

JDK 는 편의 상 1.8을 기준으로 작성합니다. 역시 현재 기준으로 가장 보편적인 버전이라고 판단되며, lamda표현식 등 가장 큰 변화가 있는 시점의 버전이라고 판단됩니다.[^2]

- oracle JDK 1.8_121 : <https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html> 에서 '121'로 검색

- Open JDK 1.8 : https://openjdk.java.net/install/

- brew 를 이용한 JDK 1.8 설치 : <https://linkeverything.github.io/mac/install-java-with-brew/>

#### Spring Initializr

![](/assets/images/posts/study/springboot/2020-11-05-00000-getting-started/capture%202020-11-05%20PM%2002.29.00.png)

Spring Boot 프로젝트는 신규 생성 시, IntelliJ의 방식을 사용하지 않습니다. IntelliJ IDEA 에서 제공하는 Spring initializr 기능은 일반 Community 버전(무료)이 아닌 Ultimate 버전에서만 사용 가능하기 때문에 보편적인 기능을 제공하지 못합니다. 따라서 위 그림에서 보이는 것 처럼 web 에서 Spring initializr를 실행하고, 이를 통해 IDE에 import하는 개념으로 사용합니다.


[^1]: 만약 아래 disqus 기능을 이용하여 안되는 부분을 지적해 주시면 확인 후 수정하도록 하겠습니다.

[^2]: 저는 MacOS 상에서 brew를 이용한 설치 방법을 사용하고 있습니다.