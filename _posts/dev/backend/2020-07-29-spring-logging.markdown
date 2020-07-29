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

#### 기본 설정된 logback

Springboot 에는 기본적으로 logback 이 포함되어 있습니다. 따라서 Springboot 의 `*Application.java` 파일의 `main()`함수를 호출하여 실행할 떄, IDE의 console 창에 나타나는 내용이 그것입니다. 

![](/assets/images/2020-07-29-spring-logging/screenCapture.png){: .align-center}

아무런 설정을 하지 않고도 이렇게 로그가 나오고 있습니다. 기본 내장된 logback 때문에 나타나는 것입니다. 

단순하게 이 것을 이용하여 log를 한 줄 넣어 보겠습니다. LogbackApplication.java 파일을 열어 아래와 같이 수정합니다. 

```java
@SpringBootApplication
public class LogbackApplication {

	private static final Logger logger = LoggerFactory.getLogger(LogbackApplication.class);

	public static void main(String[] args) {

		logger.info("Hello logback");

		SpringApplication.run(LogbackApplication.class, args);
	}

}
```

- Logger 클래스를 멤버 변수로 하나 만듧니다. `LoggerFactory.getLogger({class명})` 을 이용해서 생성합니다. 

    여기서 `{class명}`부분에 String 값을 넣지 않고, 해당 클래스를 그대로 넣어주면 나중에 관리하기 편해집니다. logback 설정 시, 패키지별로 분리를 해서 설정하게 되는 경우가 많은데, refactoring 하는 경우를 감안하면 위와 같이 설정해 주는 것이 좋습니다. 

- 해당 객체를 이용해서 `info()`메소드를 이용하여 로그를 찍어주도록 하겠습니다. 

그 결과는 아래와 같습니다. 

![](/assets/images/2020-07-29-spring-logging/screenCapture2.png){: .align-center}


<br/>

## 참고자료 및 출처

- https://romeoh.tistory.com/entry/Spring-Boot-Logback-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0
- https://supawer0728.github.io/2018/04/07/spring-boot-logging/
- https://dailyheumsi.tistory.com/173