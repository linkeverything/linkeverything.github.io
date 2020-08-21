---
title: "Springboot 에서 logback 레벨을 구동중에 변경하는 방법"
excerpt: "How to change log level on runtime with Springboot"
categories:
  - "dev"
  - "backend"
tag:
  - "spring"
  - "logback"
  - "runtime"
last_modified_at: 2020-08-21
---

> 여기서는 앞선 포스트에서 살펴본 logback 기능에 대해서 log level을 실시간으로 변경하는 방법에 대해서 작성하겠습니다. 시스템을 개발하는 중에는 로그 레벨을 변경하고 빌드하면 그만이고, 이를 설정값 등으로 조정하면 되겠지만, 운영중인 시스템에서 로그 레벨을 부득이하게 변경해야 한다면 이 방법이 도움이 될 수 있습니다.

기존에 작성하고 셈플로 올려둔 소스에 일부만 수정하여 반영합니다. 동일 프로젝트입니다.
{: .notice--info}

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/logback){:target="_blank" .btn .btn--primary}

<br/>

## Log Level 변경 방법

<br/>

#### pom.xml 파일 수정

logback classic 라이브러리를 사용해야 하기 때문에 다음 부분을 pom.xml 파일에 추가해 줍니다.

```xml
        <dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>1.2.3</version>
		</dependency>
```

그리고 eclipse 는 maven update 를 실행, intellij 는 maven 탭에서 update 버튼을 눌러줘서 최신화 합니다.

<br/>

#### logger 생성

이제 기존의 logback-spring.xml 파일을 조금 수정해야 합니다. 간단히 아래와 같이 수정해 줍니다. 이번 테스트를 위한 목적으로 생성하는 것이기 때문에 향후 프로젝트 상황에 맞게 수정하여 사용하면 됩니다.

```xml
    <logger name="com.simplify.logback" level="info" additivity="false">
        <appender-ref ref="STDOUT" />
    </logger>
```

여기서는 이름 부분에 `com.simplify.logback` 이라고 패키지명을 넣어주었습니다. 그리고 이는 STDOUT 이라는 기존 생성해 두었던 appender 를 참조합니다.

<br/>

#### 테스트용 Controller 생성

기존에 만들어 두었던 Controller에 다음 부분을 추가해 줍니다. 

```java
    @RequestMapping( value="/changeLogLevel/{level}" )
    public @ResponseBody String changeLogLevel(@PathVariable String level){

        String returnMessage = "";

        LoggerContext loggerContext = (LoggerContext) LoggerFactory.getILoggerFactory();

        ch.qos.logback.classic.Logger logger = loggerContext.getLogger("com.simplify.logback");

        if(VALID_LEVELS.contains(level.toUpperCase())){
            Level oldLevel = logger.getLevel();
            logger.setLevel(Level.toLevel(level));

            returnMessage = "LOG LEVEL CHANGE FROM " + oldLevel + " TO " + logger.getLevel();

        }else{

            returnMessage = "NOT SUPPORT LOG LEVEL : " + level;

        }

        testPrintLog("TEST");

        return returnMessage;
    }
```

- `/changeLogLevel/{level}` 형태로 url 호출이 들어오게 되면 url 에 있는 level 이 `@PathVariable` annotation 이 붙은 level 이라는 이름의 String 을 통해서 변수로 넘어오게 됩니다. 

- 그러면 미리 List 에 담아두었던 **유의미한** Log level 이 맞는지를 체크한 뒤에, 로그 레벨을 변경해 줍니다.

- 여기서는 `setLevel()` 이라는 함수를 이용하여 logger의 레벨을 변경해 줍니다.

- 만약 유의미한 log level 이 아니라면 메시지를 적절하게 만들어 사용자에게 알려줍니다.

<br/>

#### 실행 및 확인

이제 프로젝트를 실행하고 브라우저에 `http://localhost:8080/changeLogLevel/trace` 라고 입력해 보겠습니다. 

```
LOG LEVEL CHANGE FROM INFO TO TRACE
```

화면에 위와 같은 문구가 나타나며, 콘솔 창에는 다음과 같이 나타납니다.

```sh
[2020-08-21 17:46:17:603223][http-nio-8080-exec-1] TRACE c.s.l.controller.LogbackController - Start step TEST
[2020-08-21 17:46:17:603225][http-nio-8080-exec-1] DEBUG c.s.l.controller.LogbackController - Start step TEST
[2020-08-21 17:46:17:603225][http-nio-8080-exec-1] INFO  c.s.l.controller.LogbackController - Start step TEST
[2020-08-21 17:46:17:603225][http-nio-8080-exec-1] WARN  c.s.l.controller.LogbackController - Start step TEST
[2020-08-21 17:46:17:603225][http-nio-8080-exec-1] ERROR c.s.l.controller.LogbackController - Start step TEST
```

<br/>

## 참고자료 및 출처

- https://examples.javacodegeeks.com/enterprise-java/logback/logback-change-log-level-runtime-example/