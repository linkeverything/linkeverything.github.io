---
title: "Springboot 에서 Context Path 설정하기"
excerpt: "How to configure or change 'Context Path' in Springboot (multiple options for this setting)"
categories:
  - "dev"
  - "backend"
tag:
  - "spring"
  - "context path"
last_modified_at: 2020-07-31
---

> Springboot 에서는 default context path 를 **/** 로 설정합니다. 대부분의 웹서버, WAS, 혹은 Springboot 의 내장 서버 역시 다 root 를 기본으로 가지고 있습니다. 즉, 로컬 개발 시 **http://localhost:8080/** 과 같은 주소가 되는 것입니다. 그리고 각 `controller` 에서 설정한 Request Mapping 정보에 따라서 그 하위로 경로가 생성됩니다. 

그런데 경우에 따라서 이 경로를 수정해야 하는 경우가 있습니다. 물론 그에 따른 loadbalancing 이나 혹은 Gateway 역할은 다른 서버가 할 수도 있겠지만, 하나의 프로그램 단위별로 최상의 경로를 다르게 명명하는 경우도 있습니다. 이것 역시 (뒤에 살펴보겠지만) 설정 파일인 application.properties 에 설정하게 되므로, 실행하는 profile 에 따라서 application-dev.properties 등에 별도로 지정하여 활용할 수 있습니다. 

- http://xx.xx.xxx.xx:8080/dev/list
- http://xx.xx.xxx.xx:8080/test/list
- http://xx.xx.xxx.xx:8080/prod/list

아래 문서에는 1.X, 2.X 버전에 대해서 각각 설명하고 있지만, 2.X를 사용하시길 권장합니다. 많은 부분이 개선, 수정되었으며 2.X이 출시된 지도 오래되어 이제는 2.X 버전을 사용하는 것이 맞습니다.
{: .notice--info}

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/contextPathExample){:target="_blank" .btn .btn--primary}

<br/>

## Property 조정으로 설정하기

앞서 이야기한 것 처럼, `application.properties` 파일을 수정하여 적용하는 것이 가장 효율적인 적용 방법입니다. 다른 여러가지 방법이 있고, Spring 의 기본 객체를 상속(구현)하여 적용하는 방법 역시 가능합니다. 하지만 Springboot 는 `application.properties` 파일을 통한 설정 변경을 권장하고 있고, 더욱 상세한 설정이나 `properties` 파일로 적용되지 않는 사항을 제외하고는 가급적이면 `properties` 파일을 수정하여 적용하는 것을 권장합니다.

다른 설정들 처럼, Springboot 에서는 property 파일을 통해 이를 설정할 수 있습니다. 키 값은 <mark style='background-color: #ffdce0'>server.servlet.context-path</mark> 입니다.

Boot 1.X 에서는 `server.context-path` 입니다.
{: .notice--info}

<br/>

#### application.properties / yml 파일 수정

해당 파일을 열어 다음 내용을 추가해 줍니다.  

```properties
server.servlet.context-path=/simplify
```

스프링 프로젝트를 재실행하면 `http://localhost:8080/simplify` 를 통해서만 접속되는 것을 확인할 수 있습니다. 

<br/>

#### Java System property 이용

앞서 설명한 `application.properties` 파일을 수정하는 방법 이외에도 Java System Property(환경변수) 를 이용할 수도 있습니다. 이 방식을 사용하려면 context 가 초기화되기 이전에 넣우줘야 합니다.

```java
public static void main(String[] args) {
    System.setProperty("server.servlet.context-path", "/simplify");
    SpringApplication.run(Application.class, args);
}
```

<br/>

#### OS 환경 변수 이용

Springboot 는 시스템의 환경 변수를 사용할 수도 있습니다. 

Unix 기반 시스템에서는 다음을 입력합니다.

```sh
$ export SERVER_SERVLET_CONTEXT_PATH=/simplify
```

윈도우 환경이라면 아래 처럼 입력합니다.

```sh
> set SERVER_SERVLET_CONTEXT_PATH=/simplify
```

<br/>

#### Command line 에서 설정하기

Springboot 를 실행하는 명령어 상에서 argument 로 설정할 수 있습니다. 

```sh
$ java -jar app.jar --server.servlet.context-path=/simplify
```

<br/>

## Java Config 를 이용하기

Bean Factory 를 Configuration Bean 과 함께 생성하는 방법으로도 context path 를 설정할 수 있습니다. 

Springboot 2.X 버전에서는 아래와 같이 설정합니다.

```java
@Bean
public WebServerFactoryCustomizer<ConfigurableServletWebServerFactory>
  webServerFactoryCustomizer() {
    return factory -> factory.setContextPath("/simplify");
}
```

Springboot 1.X 버전에서는 아래와 같이 설정합니다.

```java
@Bean
public EmbeddedServletContainerCustomizer
  embeddedServletContainerCustomizer() {
    return container -> container.setContextPath("/simplify");
}
```

<br/>

## 설정한 내용들의 우선순위

이런 다양한 방법으로 설정 가능한데, 만약 중복으로 설정을 조정한 경우, 그 우선순위는 다음과 같이 작동합니다. 

1. Java Config
2. Command Line Arguments
3. Java System Properties
4. OS Environment Variables
5. application.properties in Current Directory
6. application.properties in the classpath (src/main/resources or the packaged jar file)

개인적인 견해로는 OS 환경변수를 이용해서 context path 를 조정하는 것은 맞지 않아 보이고, Java Config 혹은 command line argument 정도로 조정하는 것이 맞는 것 같습니다. 하지만 개발 편의성을 따진다면 properties 파일에 설정하는 것이 가장 빠르고 정확합니다. (사실 이 설정들이 중복되어 우선순위를 따진다는 것 자체가 문제입니다) 
{: .notice--info}

<br/>

## 참고자료 및 출처

- https://www.simplify.com/spring-boot-context-path