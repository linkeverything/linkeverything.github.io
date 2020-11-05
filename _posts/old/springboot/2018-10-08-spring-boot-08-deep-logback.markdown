---
title:      "SpringBoot - (8) Deep into Logback"
categories: [old]
last_modified_at:       2018-10-08 23:01
tags:
  - Spring
  - SpringBoot
published: false
---

## 들어가며

Spring Boot 를 실행하면 로그가 잘 나옵니다. Eclipse (STS) 기준 Console View에 잘 나옵니다. Spring Boot 에는 기본적으로 Logback 이 포함되어 있기 때문에 아무런 작업 없이도 로그가 잘 나타납니다. 그런데 실제 환경에서는 이러한 Stream 형태의 Log는 아무런 의미가 없습니다. 왜냐하면 서버에 문제가 발생한 경우, 이를 추후에 Trace 할 방법이 없기 때문입니다. 따라서 결국 Log는 특정 폴더에 파일 형태로 남기게 되고, 이를 날짜별로 분류하거나, 용량별로 나눠서 남기게 됩니다. 


Spring Boot 에 기본적으로 Logback이 포함되어 있기 때문에 라이브러리 쪽에 아래 그림처럼 나타납니다. 

![](/assets/images/posts/old/img/post/2018-10-08-spring-boot-08-deep-logback/spring-boot-08-deep-logback-00001.png)


## 구현하기

##### Dependency 추가하기

기본 logback 은 포함되어 있으나, jdbc에 대한 logback 설정은 되어 있지 않기 때문에 이 부분은 추가로 설정해 줍니다.

```xml
        <!-- DB Logback -->
        <dependency>
            <groupId>org.bgee.log4jdbc-log4j2</groupId>
            <artifactId>log4jdbc-log4j2-jdbc4.1</artifactId>
            <version>1.16</version>
        </dependency>
        <!-- DB Logback -->
```

그리고 resources 폴더 아래에 log4jdbc.log4j2.properties 파일을 아래와 같이 생성합니다. 

```properties
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
log4jdbc.dump.sql.maxlinelength=0
```

##### DB 설정 정보 수정하기

기 작성되어 있었던 config.properties 파일에 다음과 같이 수정합니다. 

```properties
#spring.datasource.driverClassName=org.mariadb.jdbc.Driver
#spring.datasource.url=jdbc:mariadb://52.78.41.121:3306/TestDB
spring.datasource.driverClassName=net.sf.log4jdbc.sql.jdbcapi.DriverSpy
spring.datasource.url=jdbc:log4jdbc:mariadb://52.78.41.121:3306/TestDB
```

##### application.properties 를 application.yml 로 수정, 프로파일 추가하기

다음과 같이 파일명을 변경하고 application.yml 파일을 수정합니다. 각각의 프로파일은 logging path 를 다르게 가지고 있습니다.

```yml
spring.mvc.view.prefix: /WEB-INF/jsp/
spring.mvc.view.suffix: .jsp
 
logging:
  path: c:/dev/log/spring
  file: log-file  
  
published: false
---
spring:
  profiles: log-windows
  
logging:
  path: c:/dev/log/spring
published: false
---
spring:
  profiles: log-linux
  
logging:
  path: /log/spring/
```

##### Logback 설정 파일 추가하기

Spring Boot를 사용하면서 logback 설정 파일을 추가하려면 이름을 반드시 logback-spring.xml 로 설정해 주세요. 그냥 logback.xml 로 넣어도 동작하는 것 처럼 보이지만, 설정 파일이 최초에 불려지지 않고, 타이밍 이슈가 발생하여 실제 application.properties 등에 설정해둔 log 폴더 등이 제대로 동작하지 못하는 문제가 발생합니다. 아래와 같이 logback 을 만들어 주었습니다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="10 seconds">
 
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter" />
    
    <springProfile name="log-jdbc">
        <logger name="jdbc" level="OFF"/>
        <logger name="jdbc.sqlonly" level="OFF"/>
        <logger name="jdbc.sqltiming" level="DEBUG"/>
        <logger name="jdbc.audit" level="OFF"/>
        <logger name="jdbc.resultset" level="OFF"/>
        <logger name="jdbc.resultsettable" level="DEBUG"/>
        <logger name="jdbc.connection" level="OFF"/>
    </springProfile>
    
    <springProfile name="log-file">
        <include resource="appenders/appender-file.xml" />
          <root level="INFO">
            <appender-ref ref="FILE" />
          </root>
    </springProfile>
    
    <springProfile name="log-console">
        <include resource="appenders/appender-console.xml" />
          <root level="INFO">
              <appender-ref ref="CONSOLE" />
          </root>
    </springProfile>
</configuration>
```

지저분하게 늘어서는 것을 방지하기 위해서 appender 부분은 별도 파일로 빼 놓았습니다. appenders/appender-file.xml 및 appenders/appender-console.xml 파일을 아래와 같습니다. 각각 resources 폴더 아래에 둡니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<included>
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/${LOG_FILE}.log</file>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/${LOG_FILE}.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <maxFileSize>${LOG_FILE_MAX_SIZE:-10MB}</maxFileSize>
            <maxHistory>${LOG_FILE_MAX_HISTORY:-0}</maxHistory>
        </rollingPolicy>
    </appender>
</included>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<included>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{MM-dd HH:mm:ss.SSS} %clr(${LOG_LEVEL_PATTERN:-%5p}) [%t] %clr(%-40.40logger{39}){cyan} | %msg%n</pattern>
        </encoder>
    </appender>
</included>
```

##### 구동 profile 등록하기 

위에 선언한 것 처럼 각 profile을 걸맞게 Run Configuration에 추가해 줍니다. 

![](/assets/images/posts/old/img/post/2018-10-08-spring-boot-08-deep-logback/spring-boot-08-deep-logback-00002.png)

프로파일에 log-file 을 추가해 구동해보고 해당 경로에 파일이 생기는지 확인해 봅니다.








GitHub 소스 위치 : https://github.com/Simplify-study/SpringBootSample.git

출처: https://4urdev.tistory.com/43 [Simplify]
