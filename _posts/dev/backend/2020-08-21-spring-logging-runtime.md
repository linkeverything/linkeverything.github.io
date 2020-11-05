---
title: "Spring Boot 에서 logback 레벨을 구동중에 변경하는 방법"
categories: [SpringBoot]
tag: [spring boot, logback, runtime]
last_modified_at: 2020-08-21
---
여기서는 앞선 포스트에서 살펴본 logback 기능에 대해서 log level을 실시간으로 변경하는 방법에 대해서 작성하겠습니다. 시스템을 개발하는 중에는 로그 레벨을 변경하고 빌드하면 그만이고, 이를 설정값 등으로 조정하면 되겠지만, 운영중인 시스템에서 로그 레벨을 부득이하게 변경해야 한다면 이 방법이 도움이 될 수 있습니다.

기존에 작성하고 셈플로 올려둔 소스에 일부만 수정하여 반영합니다. 동일 프로젝트입니다.
{: .notice--info}

[Download Sample code from GitHub](https://github.com/simpl-ify/SampleProjects/tree/master/logback){:target="_blank" .btn .btn--primary}



## Log Level 변경 방법

Springboot 프로그램 구동 중에 log level 을 변경하기 위해서는 특정 라이브러리를 하나 추가하고 그 추가된 라이브러리를 이용해서 LoggerContext 객체를 얻어와, 레벨을 변경해 주어야 합니다.



#### pom.xml 파일 수정

신규로 추가되는 라이브버리는 logback-classic 입니다. ch.qos.logback 그룹에 있는 라이브러리입니다. 저는 maven 환경을 사용하고 있으므로 pom.xml파일을 열어 아래 내용을 추가해 줍니다.

```xml
<dependency>
	<groupId>ch.qos.logback</groupId>
	<artifactId>logback-classic</artifactId>
	<version>1.2.3</version>
</dependency>
```

버전 부분은 이 글을 보는 시점에 따라서 조금 상이할 수 있습니다. 제 프로젝트를 보시고 동일하게 적용하거나, 혹은 최신 버전이 무엇인지를 [Maven Repository 홈페이지](https://mvnrepository.com/artifact/ch.qos.logback/logback-classic)를 통해서 확인해 보시기 바랍니다. 
{: .notice--warning}

그리고 eclipse 는 maven update 를 실행, intellij 는 maven 탭에서 update 버튼을 눌러줘서 최신화 합니다.

만약 gradle 을 이용하여 프로젝트를 구성했다면 아래와 같이 입력하고 gradle 쪽 업데이트를 수행합니다. 

```gradle
testCompile group: 'ch.qos.logback', name: 'logback-classic', version: '1.2.3'
```



#### logger 생성

이제 기존의 logback-spring.xml 파일을 조금 수정해야 합니다. 간단히 아래와 같이 수정해 줍니다. 이번 테스트를 위한 목적으로 생성하는 것이기 때문에 향후 프로젝트 상황에 맞게 수정하여 사용하면 됩니다.

```xml
    <logger name="com.simplify.logback" level="info" additivity="false">
        <appender-ref ref="STDOUT" />
    </logger>
```

여기서는 이름 부분에 `com.simplify.logback` 이라고 패키지명을 넣어주었습니다. 그리고 이는 STDOUT 이라는 기존 생성해 두었던 appender 를 참조합니다.

여기서 logback 이름에 대해서 다음 내용을 보면 알겠지만, 해당 패키지에 동일하게 적용된 모든 로거와 동일합니다. 기존에 만들어둔 logger 가 별도오 없고, 따라서 root 설정에 따라서 로깅 처리가 되고 있었기 때문에 명시적으로 하기 위해서 위와 같이 별도로 생성해 준 것입니다. 실질적으로는 동일한 로거일 것입니다. 
{: .notice--warning}



#### 테스트용 Controller 생성

실행과 동시에 로그 레벨을 변경하게 처리할 수 있겠지만, 실제 환경에서를 가졍하면 외부에서 로그 레벨을 바꿔주는 작업(?) 혹은 요청이 있어야 합니다. 

예를 들면 <mark style='background-color: #fff5b1'>controller 에 `RequestMapping` 을 추가하여, API 형태로 기능을 제공</mark>하거나, 아니면 <mark style='background-color: #fff5b1'>특정한 property를 바라보게 하는 `@Configuration` Bean 을 이용</mark>할 수도 있을 것입니다. (이건 향후에 더욱 자세하게 다룰 수 있도록 준비하겠습니다.) 여기서는 controller에 API 를 하나 열어주고, 이에 따라서 동작하게 하도록 간단히 구성하겠습니다.

기존에 만들어 두었던, 혹은 신규로 controller class를 하나 만들어서에 다음 부분을 추가해 줍니다. 

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

- 여기서는 `setLevel()` 이라는 함수를 이용하여 logger의 레벨을 변경해 줍니다. 변경하고 나면 기존의 log level 과 변경된 log level 을 이용하여 메시지를 return 합니다.

- 만약 유의미한 log level 이 아니라면 메시지를 적절하게 만들어 사용자에게 알려줍니다.

위에서 확인용으로 각 로그 레벨에 맞게 메시지를 출력하는 메소드가 있습니다. 아래와 같이 간단히 구현하여 줍니다. 

```java
    private void testPrintLog(String logMessage) {

        if (logger.isTraceEnabled()) {
            logger.trace("Start step {}", logMessage);
        }

        if (logger.isDebugEnabled()) {
            logger.debug("Start step {}", logMessage);
        }

        if (logger.isInfoEnabled()) {
            logger.info("Start step {}", logMessage);
        }

        if (logger.isWarnEnabled()) {
            logger.warn("Start step {}", logMessage);
        }

        if (logger.isErrorEnabled()) {
            logger.error("Start step {}", logMessage);
        }
    }
```

아직도 조금 의구심이 드는 부분은 위 내용과 같이 level 체크하는 부분입니다. `isTraceEnabled()` 를 체크하여 `trace` 로그를 남기는데, 실제로 체크하지 않아도 레벨에 따라서 남기거나 안남기거나가 결정되어 있습니다. 저런 부분을 강제로 체크하는 것이 시스템 부담을 줄여주는지는 의문입니다. 어차피 `trace()`함수 내에서도 이를 체크할 것이기 때문입니다. 혹시 더 정확하게 확인 되는 부분이 있으면 공유하겠습니다. 
{: .notice--info}

**2020. 08. 25. 추가**

위 내용에서 isDebugEnabled() 등으로 체크하는 것은 logger.debug() 함수에 파라미터로 들어가는 문자열의 연산(문자합치기)에 들어가는 부하를 최소화하기 위함입니다. 

```java
logger.debug("Start step {}", logMessage)
```

위와 같은 경우, `Start step ` 문자열과 logMessage 문자열을 합쳐서 긴 문장을 우선 만들고, 그 이후에 `debug()`함수를 호출하여 문자열을 logging 합니다. 이 때에 레벨이 안맞으면 그냥 문자열은 버려지게 됩니다. 

만약 레벨 체크를 하지 않으면 위에서 문자열을 합쳐 문장을 만드는 작업을 필요없는 작업임에도 발생하게 되는 부분입니다. 따라서 앞에서 로그 레벨을 체크함으로써 시스템 부하를 조금이나마 줄여주는 효과를 갖는다고 볼 수 있습니다. 



#### 실행 및 확인

이제 프로젝트를 실행하고 브라우저에 `http://localhost:8080/changeLogLevel/trace` 라고 입력해 보겠습니다. 

```
LOG LEVEL CHANGE FROM INFO TO TRACE
```

브라우저 화면에 위와 같은 문구가 나타나며, 콘솔 창에는 다음과 같이 나타납니다.

```
[2020-08-21 17:46:17:603223][http-nio-8080-exec-1] TRACE c.s.l.controller.LogbackController - Start step TEST
[2020-08-21 17:46:17:603225][http-nio-8080-exec-1] DEBUG c.s.l.controller.LogbackController - Start step TEST
[2020-08-21 17:46:17:603225][http-nio-8080-exec-1] INFO  c.s.l.controller.LogbackController - Start step TEST
[2020-08-21 17:46:17:603225][http-nio-8080-exec-1] WARN  c.s.l.controller.LogbackController - Start step TEST
[2020-08-21 17:46:17:603225][http-nio-8080-exec-1] ERROR c.s.l.controller.LogbackController - Start step TEST
```



**2020. 10. 06 추가**

이 글을 적고나서 조금 꼼꼼히 보니, 위 방법은 Spring boot 의 기본 logger를 확장해서 우회하는 방식처럼 보입니다. 

거의 대부분의 경우에서 lombok 라이브러리를 사용하고 있고, 따라서 `@slf4j` 를 넣어 간편하게 사용하고 있을 것이니 아래 방법도 추가로 공유합니다.

slf4j 를 사용하는 경우, 아래와 같이 `application.yml` (혹은 `application.properties`)파일에 설정할 수 있습니다.

```yml
logging:
  config: /Users/jonghunpark/logback.xml
  level:
    root: info
    com.simplify: debug
```

위와 같이 logback.xml 파일을 별도의 파일, 그러니까, jar의 classpath 에 포함되는 것이 아닌 별도 파일로 지정할 수 있습니다. logback.xml 파일에는 여러가지 설정들, 즉 로그를 남기는데 필요한 여러 appender들과 그를 사용하는 logger설정들이 잇는데, 이에 대해서 다음과 같이 <mark style='background-color: #fff5b1'>AUTO SCAN</mark> 기능을 설정할 수 있습니다. 

```xml
<configuration scan="true" scanPeriod="15 seconds">
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>TEST %d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

- scan : 설정 파일인 logback.xml 파일이 변경되었는지 여부를 판단하는 auto scan 기능을 사용할지 여부를 설정합니다. 
- scanPeriod : auto scan 주기를 설정합니다. 주기를 짧게 하면 바로바로 적용이 되는 것을 확인할 수 있겠지만, 너무 자주 scan 하게 하면 disk io 가 증가해서 오히려 부담이 될 수 있으니 적절한 값으로 설정합니다.

위와 같이 설정한 뒤, spring boot는 `java -jar XXX.jar` 명령어로 실행하고, 설정 파일인 외부의 logback.xml 파일에서 로그 레벨을 변경하게 되면 최대 15초 후에 설정이 적용되어 변경된 로그 레벨이 설정되게 됩니다.



## 참고자료 및 출처

- <https://examples.javacodegeeks.com/enterprise-java/logback/logback-change-log-level-runtime-example/>
- <https://www.baeldung.com/spring-boot-changing-log-level-at-runtime>