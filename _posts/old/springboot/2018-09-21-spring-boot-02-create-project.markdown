---
title:      "SpringBoot - (2) Create new Project"
categories: [Old]
last_modified_at:       2018-09-21tags:
  - Spring
  - SpringBoot
published: false
---

## 프로젝트 생성

앞으로 사용할 프로젝트를 생성합니다.  신규 프로젝트를 생성하는 것 부터 시작하여, 각종 API 를 만들고 DB에 접속하고 하는 등의 과정을 모두 적을 예정입니다. 여기서는 신규 프로젝트를 만드는 것 부터, 기본 생성되는 파일들이 각각 의미하는 것들에 대해 설명합니다.

##### New Project 생성

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-02-create-project/spring-boot-02-create-project-00001.png)

STS를 실행하고 좌측 Project Explorer(Package Explorer) 에서 우클릭 > New > Other... 를 선택합니다.  

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-02-create-project/spring-boot-02-create-project-00002.png)

New 창에서 Spring Boot 폴더 하위에 있는 Spring Starter Project를 선택하고 Next > 를 클릭합니다. 

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-02-create-project/spring-boot-02-create-project-00003.png)

프로젝트 상세 설정을 하는 페이지에서 저는 위와 같이 입력하였습니다. 

(위 설정 중에서 'Artifact' 부분은 위와 같이 입력하면 향후 pom.xml 에서 에러가 발생합니다 띄어쓰기 없이 입력하세요..)

* Maven 빌드를 기본으로 가져갑니다. 

  근래에 gradle 을 이용하는 경우도 많이 있습니다만, 설정이 익숙치 않아서 그냥 maven으로 작업했습니다. gradle을 사용한다고 해도 크게 차이는 없어 보입니다. 

* 패키지명 작성에 주의하세요

  추후 변경 가능하지만, 패키지명을 직접 입력(xml파일 등..)하는 경우가 있으니 오탈자가 없는지 등등에 유의해야 합니다.
 
* Packaging 은 jar 로 변경하였습니다. 

  기본값은 war이고 이는 tomcat 에 올리기에 적당한 파일 형태입니다. 이것 역시 추후 pom.xml 에서 변경해서 사용 가능합니다.

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-02-create-project/spring-boot-02-create-project-00004.png)

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-02-create-project/spring-boot-02-create-project-00005.png)

그 이후 설정들은 그대로 둔 채로 마지막까지 진행하여 프로젝트를 생성합니다. 아래 그림처럼 프로젝트가 생성되면 됩니다.

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-02-create-project/spring-boot-02-create-project-00006.png)


##### 기본 생성된 파일 설명

위 과정을 거쳐서 프로젝트를 생성(Wizard 를 이용한 프로젝트 생성)하고 나면, 몇 개의 파일들이 기본적으로 생성됩니다. 우선 아래와 같이 펼쳐놓고 각 파일에 대한 설명을 진행합니다.

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-02-create-project/spring-boot-02-create-project-00007.png)

* pom.xml 

  Maven build 환경이라면 당연히 존재하는 파일입니다. 빌드 환경에 대한 정의가 이 파일에 정의되어 있고, 의존성 있는 라이브러리들에 대한 정보들이 나열되어 있습니다. 만약 Gradle build 환경을 선택하였다면, build.gradle 파일이 이 역할을 대신하게 됩니다. (이 부분에 대해서도 추후 다른 포스팅을 통해서 공유하도록 하겠습니다.)

* mvnw / mvnw.cmd

  maven 이 설치되어 있지 않은 환경에서 maven을 사용할 수 있도록 해 주는 Maven Wrapper 실행 파일입니다. 일반적으로 maven 이 없이도, eclipse 설치만으로도 maven 빌드가 가능한 이유가 이것 때문이라고도 할 수 있겠습니다. 설명이 잘 되어있는 블로그가 있어 이를 링크로 걸어드립니다.

* SpringBootSampleApplication.java

  가장 기본적으로 생성되는 java 파일이며, 안에 main 메소드가 있습니다. 시작 시점에 원하는 작업이 있으면 이 main 메소드 안에서 처리해 주어도 됩니다. (물론 권장 사항은 아닙니다)

* application.properties

  프로젝트 기본 설정 정보가 저장되는 곳입니다. 기본적으로 아무런 설정값이 없이 빈 칸으로 되어있고, 아이콘이 스프링처럼 바뀌어 있는 것을 보면 아시겠지만 파일명 역시 예약어입니다. 이 파일 안에 DB접속 정보, Logging 정보 등을 넣고 향후 Bean 에서 끌어다가 쓸 수 있습니다. spring 자체의 profile 관리도 할 수 있습니다. 차차 살펴보겠습니다. 


## 프로젝트 구동

프로젝트 루트에서 우클릭 > Run as ... > Spring Boot App 을 선택하시면 Spring Boot 로 실행시킬 수 있습니다. 이 방법은 향후 &#42;.jar 로 export 하고, java -jar 로 실행하는 것과 동일하다고 생각하시면 됩니다. 정상적으로 실행되는지만 확인하시기 바랍니다. 

##### Maven update

사실 Spring 프로젝트를 개발하는 데 있어서 Maven update 는 java 프로젝트의 clean & build 와 매우 유사합니다. 재 빌드를 시키는 것인데, maven update 의 경우 의존성에 대한 부분을 다시 한 번 검토(혹은 추가 다운로드) 하고, 프로젝트를 다시 빌드합니다. 프로젝트에서 우클릭 > Maven > Update Maven 을 선택하시거나, Alt + F5를 눌러 창을 띄워 실행해도 무방합니다. (아마 단축키는 자연스럽게 외우게 되실겁니다. 엄청 자주 사용하게 되거든요.)

GitHub 소스 위치 : https://github.com/Simplify-study/SpringBootSample.git

출처: https://4urdev.tistory.com/43 [Simplify]
