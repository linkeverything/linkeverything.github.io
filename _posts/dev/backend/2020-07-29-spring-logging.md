---
title: "Spring Boot 에서 logback 사용하기"
categories: [SpringBoot]
tag: [spring boot, logback]
last_modified_at: 2020-07-29
---
backend나 frontend나 프로그램 개발에 있어, logging 처리는 아주 중요한 부분입니다. 특별히 truble-shooting을 위한 목적이 아니라고 하더라도 시스템의 운영 층면에서 필요한 부분이므로 아래와 같이 정리합니다.

다양한 방법, 각자 선호하는 방법이 있겠지만, 여기서는 Springboot 환경에서 가장 간단하게 접근할 수 있는 logging 처리인 **logback** 에 대해서 살펴봅니다. 
{: .notice--info}

[Download Sample code from GitHub](https://github.com/simpl-ify/SampleProjects/tree/master/logback){:target="_blank" .btn .btn--primary}

## 기본사항

#### Springboot 생성

Springboot 프로젝트는 단순히 [Spring Initializr]()를 이용해서 생성하면 됩니다. 해당 페이지에서 아래와 같이 설정값을 적어주고 `GENERATE` 버튼을 눌러 다운로드 받고, 이를 IntelliJ 등 IDE 환경으로 Import 하면 됩니다.

![](/assets/images/posts/dev/backend/2020-07-29-spring-logging/start.spring.io-2020.07.29-18_04_14.png){: .align-center}

#### 기본 설정된 logback

Springboot 에는 기본적으로 logback 이 포함되어 있습니다. 따라서 Springboot 의 `*Application.java` 파일의 `main()`함수를 호출하여 실행할 떄, IDE의 console 창에 나타나는 내용이 그것입니다. 

![](/assets/images/posts/dev/backend/2020-07-29-spring-logging/screenCapture.png){: .align-center}

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

![](/assets/images/posts/dev/backend/2020-07-29-spring-logging/screenCapture2.png){: .align-center}

## logback 고도화

#### logback 설정 파일 만들기

logback은 기본적으로 `logback.xml` 파일을 로드하도록 되어 있습니다. 더 정확하게 이야기하자면 Springboot 에서는 `logback-spring.xml` 파일을 로드합니다. 

resources 폴더 아래에 `logback-spring.xml` 파일을 생성합니다. 그리고 아래 내용을 넣어 줍니다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <!--로그 파일 저장 위치-->
    <property name="LOGS_PATH" value="./logs"/>

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>[%d{yyyy-MM-dd HH:mm:ss}:%-3relative][%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="DAILY_ROLLING_FILE_APPENDER" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOGS_PATH}/logback.log</file>
        <encoder>
            <pattern>[%d{yyyy-MM-dd HH:mm:ss}:%-3relative][%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOGS_PATH}/logback.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!-- or whenever the file size reaches 100MB -->
                <maxFileSize>5MB</maxFileSize>
                <!-- kb, mb, gb -->
            </timeBasedFileNamingAndTriggeringPolicy>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>

    <logger name="com.simplify.logback" level="INFO">
        <appender-ref ref="DAILY_ROLLING_FILE_APPENDER" />
    </logger>

    <root level="INFO">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

- `<appender>` 는 실제 로그를 출력할 방법에 대한 부분입니다. 프로그래밍 언어로 치면 method 구현체 비슷하다고 생각하면 됩니다.
- `<logger>` 는 로그를 출력할 방식에 대한 것을 정의합니다. 여기 예제에서는 `com.simplify.logback` 이라는 패키지에 대해서 **INFO** 레벨로 `DAILY_ROLLING_FILE_APPENDER` 라는 이름의 appender 를 사용해서 출력하겠다는 의미입니다.
- `<root>` 부분에서는 전체 logback 에 대한 **default** 설정을 다룹니다. 이 프로그램 전반에 대해서 **INFO** 레벨로, `STDOUT` 이라는 이름의 appender 를 사용해서 출력하겠다는 의미입니다.

이렇게 만들고 샐행해 보면, 다음과 같이 출력되는 것을 확인할 수 있습니다. 

![](/assets/images/posts/dev/backend/2020-07-29-spring-logging/screenCapture3.png){: .align-center}

결과로, 프로젝트에 log 폴더가 생성된 것을 확인할 수 있고, 

![](/assets/images/posts/dev/backend/2020-07-29-spring-logging/screenCapture4.png){: .align-center}

파일 내용을 다음과 같습니다.

![](/assets/images/posts/dev/backend/2020-07-29-spring-logging/screenCapture5.png){: .align-center}

#### 프로파일 별 구분하기

Springboot 를 사용하면서 편리한 점 중 하나가 바로 프로파일 관리 기능입니다. 개발/테스트/운영 서버에 배포하는 것을 각 프로파일로 만들고, 이 프로파일 별 설정 파일들을 구분할 수 있습니다. 

구분된 프로파일을 이용해서 설정 값을 분리하려면 application.properties 파일의 파일명에 `-{프로파일명}` 을 붙여주면 됩니다. 만약 `dev` 라는 이름의 프로파일을 사용하려고 한다면 application-dev.properties 라고 파일을 생성하고 여기서 설정값을 지정해주면 됩니다.

`application-dev.properties` 파일을 생성하고, `-Dspring.profiles.active=dev` 라고 VM 옵션을 주어 실행하면, `application.properties` 파일에 선언된 정보들을 우선 로드한 뒤, `application-dev.properties` 를 로드합니다. 즉, 기본 값들은 `application.properties` 파일에 다 넣고, 변경되는 값들만 `application-dev.properties` 파일에 넣어주면 됩니다. (override 개념)
{: .notice--warning}

#### logback 설정 파일에서 프로파일 이용하기

logback 파일에서 프로파일별로 설정을 분리하려면, 파일로 분리해도 되겠지만, 간단히 xml 파일 내에서 특정 부분을 `<springProfile>` 태그로 감싸주면 됩니다. 

앞서 살펴본 `logback-spring.xml` 파일에서 아래와 같이 변경해 봅니다. 

```xml
...
    <!--로그 파일 저장 위치-->
    <springProfile name="dev">
        <property name="LOGS_PATH" value="./logs/dev"/>
    </springProfile>
...
```

이렇게 하면 `dev` 라는 프로파일로 실행될 때에는 `./logs/dev` 폴더 하위에 로그파일을 생성하게 됩니다.

## 참고자료 및 출처

- <https://romeoh.tistory.com/entry/Spring-Boot-Logback-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0>
- <https://supawer0728.github.io/2018/04/07/spring-boot-logging/>
- <https://dailyheumsi.tistory.com/173>