---
title:      "SpringBoot - (1) Startup"
categories: [Old]
last_modified_at:       2018-09-21tags:
  - Spring
  - SpringBoot
published: false
---

## 들어가며..

Spring 은 최근 가장 많이 사용하는 서버 프로그래밍 프레임워크입니다. 추후에 Spring 자체에 대한 글도 한 번은 올릴 예정이긴 하지만, Spring 에 적응하고 나면 비교적 편하게 서버 프로그래밍을 할 수 있습니다. Spring 은 웹 서비스를 만들기 위한 프레임워크이기 때문에 보통은 Tomcat 을 설치하고 그 위에 띄우게 됩니다. Tomcat 위에 올라가다 보니, 관리해야 할 프로그램 instance가 늘어나게 되는 샘이고 이러한 불편함을 해소해 주는 것이 Spring Boot 입니다. (Spring boot 는 Tomcat 위에서도 실행 가능하긴 합니다)

그 외에도 Spring 에서 하나하나 설계하고 만들어야 하는 부분들이 Spring boot 에서는 비교적 쉬운 방법으로 개발할 수 있습니다. 사실 개발 및 유지보수 용이성을 위해서 Spring boot 를 사용한다고 보면 되고, 여러 Config 들을 기본설정으로 이미 만들어 두었다고 생각하면 되겠습니다. 저 역시 Spring Boot 로 개발하는 일이 많아져, 환경 설정부터 시작하여 주요 기능들의 개발 방법까지를 글로 남기려고 합니다. 



## 환경설정..

##### Java 설치

워낙 Java 설치에 대해서는 글들이 많아, 따로 언급하지 않으려고 했지만, 그래도 과정 중의 일부이니, 여기에 남깁니다. 

oracle 의 Java 다운로드 페이지에서 JDK를 다운로드 받습니다. 저는 Java 버전 중 최근 많이 쓰이는 Java 8 버전을 기준으로 작성하겠습니다. License 부분에 Agree 를 선택하고 OS에 맞는 버전을 다운로드 받습니다. 

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-01-start/spring-boot-01-start-00001.png)

적절한 파일을 다운로드 받아 설치를 진행하고 나면 최신 버전에서는 알아서 윈도우 환경변수에 넣어줍니다. 물론 다양한 버전을 사용하고 있다거나, 기존의 링크가 맘에 들지 않는다면 별도로 잡아주는 것이 좋습니다. 참고로, JDK 설치 과정에서 JRE도 자동으로 설치되는데, 기본적으로 잡혀있는 환경변수는 이 JRE를 바라보고 있으므로, 개발 목적으로는 환경변수를 변경하는 것이 좋습니다. 윈도우 10 부터는 환경변수가 아래처럼 표로 보기좋게 설정하게 되어 있습니다. 

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-01-start/spring-boot-01-start-00002.png)

보통의 경우, 저는 JAVA_HOME 변수를 시스템 변수에 만들어 두고, 이 경로를 바라보게 해 둡니다. %JAVA_HOME% 부분이 변수로 설정된 것을 읽어오게 되는 부분이고, 그 하위에 bin 디렉토리를 Path 에 넣어줍니다. 이렇게 하면 JAVA 버전이 변경되었거나 다수의 JAVA가 설치되어 있는 환경에서도 순쉽게 환경 변수를 변경할 수 있습니다. (필수사항은 아닙니다) 또한 저 변수가 암묵적으로 통용되는 항목이기 때문에 Tomcat 이나 Eclipse 등에서도 찾아보게 되어 있어, 편리합니다. 

> 기존 Javapath(?) 등으로 잡혀있는 것은 Path는 삭제해 주어야 정상 적용됩니다


##### STS (Spring Tool Suite) 다운로드

기존에는 Eclipse 에 Spring 개발 Plug-in을 추가로 설치했었으나, 근래에는 그냥 STS를 다운받아 사용하라고 합니다. 아무래도 필요한 Plug-in 들이 대부분 들어있고, 별도로 설치해 줄 필요가 없어 편리합니다. STS 다운로드 페이지(Spring Tools 페이지)를 방문하여 STS 를 OS에 맞는 것을 다운로드 받습니다. 

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-01-start/spring-boot-01-start-00003.png)

위와 같은 화면에서 See All versions 를 선택하여 상세 페이지에 진입합니다. 

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-01-start/spring-boot-01-start-00004.png)

OS 옆에 버튼을 눌러 확장하고, 64bit 라면 64Bit 를 받습니다. 32bit를 받아서 실행해도 무관하나, 성능을 최대로 끌어올려 사용하려면 이런 방법이 더 좋습니다. Eclipse 처럼 STS역시 zip 파일 형태로 다운로드 됩니다. 적절한 곳에 압축을 풀고, \sts-bundle\sts-3.9.5.RELEASE 경로로 이동하여 STS.exe 파일을 실행합니다. 



> JVM 에러가 발생하는 경우, 환경 변수를 다시 한 번 확인하시기 바랍니다. Path 에 잡아준 위치가 맞는지 확인하세요. 

> STS.ini 는 eclipse.ini 처럼, STS구동 환경을 설정하는 파일입니다. 이 안에서 메모리 할당 등을 설정할 수 있으며, -vm 옵션으로 JDK path를 직접 잡아줄 수도 있습니다. 

이클립스와 비슷한 UI가 실행되어 나타나면 정상적으로 설치된 것입니다. 

![](/assets/images/posts/old/img/post/2018-09-21-spring-boot-01-start/spring-boot-01-start-00005.png)

> 최초 실행하는 경우 workspace를 설정하라고 물어보는데, 기본으로 설정된 폴더보다는 특정 위치를 잡아놓는 것이 편합니다. 

> STS는 Eclipse 와 완전히 동일한 IDE입니다. 기존 사용하던 Eclipse 에서 사용하려면 다운로드 페이지에 있는 Plug-in 다운로드를 이용하세요


GitHub 소스 위치 : https://github.com/Simplify-study/SpringBootSample.git

출처: https://4urdev.tistory.com/43 [Simplify]