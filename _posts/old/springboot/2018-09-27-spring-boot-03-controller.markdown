---
title:      "SpringBoot - (3) Controller"
categories: [old]
last_modified_at:       2018-09-21
tags:
  - Spring
  - SpringBoot
published: false
---


## Controller
앞서 생성한 프로젝트에 이어서, 서버 프로그램에 들어오는 접점이 되는 Controller 를 생성해 보도록 하겠습니다. Controller 는 당연히 @Controller annotation을 앞에 두면서 생성하며, 암묵적으로 Controller 라는 postfix 를 갖는 class 입니다. 반드시 그런 것은 아니지만, 특별한 경우를 제외하고서는 다른 class 를 implements 받거나 extends 하지 않는 것을 기본으로 합니다. 

## 실습

##### pom.xml 에 dependency 추가하기

기본 java 문법, 객체를 사용하는 것을 제외하고는 대부분이 의존성(dependency)을 추가해줘야 합니다. 여기서 알아볼 것은 controller, 즉 web 과 관련한 것이므로, 이와 관련한 dependency를 추가해 줍니다.


```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

위 항목은 web 프로젝트를 만들 때 필수적으로 들어가는 의존성 항목입니다. 프로젝트를 생성할 당시, 'Web' 항목에 체크하고 진행하였다면 pom.xml 에 이미 추가되어 있을 항목이지만, 글의 목적 상, 빈 깡통인 프로젝트에서 추가하는 방법을 설명드리고자 합니다. 위와 같이 적어넣은 뒤에, ALT + F5 를 눌러 Update Maven 을 실행해 줍니다. Background 작업이라서 눈에 잘 보이지는 않지만, 새로운 의존성 항목을 다운로드 받고, 이를 프로젝트에 포함시켜주는 작업이 금방 지나갑니다.

##### java 파일 생성하기

Spring 을 Eclipse 에서 개발할 때에는 java 파일은 src/main/java 항목 아래에 작성합니다. 결국은 다 같은 경로인데, 모아주는 의미에서 저렇게 보여지고 있고, 저 구조에는 조금 친해져야 할 필요가 있습니다. 해당 항목을 열면, 처음 프로젝트 생성 당시에 적어둔 패키지명이 보이고, 저는 그 아래에 controller 패키지를 만들어 구분을 두겠습니다. 

![](/assets/images/posts/old/img/post/2018-09-27-spring-boot-03-controller/spring-boot-03-controller-00001.png)

해당 패키지 아래에 CommonController.java 파일을 생성했습니다. 당연하지만, New > Class 를 이용해야 조금 더 편리합니다. 단순 파일 생성하게 되면 패키지 선언부분부터, 아래 class 부분까지를 다 직접 써넣어야 합니다. 

위 코드를 아래와 같이 완성시켜 줍니다. 

```java
package com.simplify.sample.controller;
 
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
 
@RestController
public class CommonController {
    
    @RequestMapping("/")
    public String root_test() throws Exception{
        return "Hello Root(/)";
    }
 
    @RequestMapping("/demo")
    public String demo_test() throws Exception{
        return "Hello demo(/demo)";
    }
 
}
```

@RestController annotation을 붙여주면 그대로 Restful API 를 사용하는 Controller 가 되고, 따라서 각 Request Mapping 에 적어준 url 이 호출될 때 마다 Return 으로 적어준 내용이 반환된다. 여기서는 String 으로 return 해 주므로, 그대로 내용이 보이고, 만약 method 에 return 이 List<String> 과 같은 형태라면 json 배열 형태의 내용이 화면에 보일 것이다. 이 부분은 Response body 를 알아서 handle해 주는 것이 Spring에 포함되어 있다. 또한 String 앞에 @ResponseBody 를 명시하지 않아도 정상 동작하는 이유는 @RestController 에 @ResponseBody 가 포함되어 있기 때문이다. 이 부분은 다음으로 설명할 @Controller 에서는 반드시 명시해주어야 하는 것과 일맥하여 중요하게 알아 두어야 할 부분이다. (즉, @Controller 에서는 일반 String 을 return 하기 위해서(ajax 에 대한 응답)는 반드시 @ResponseBody 를 적어 주어야 한다)

###### @Controller와 @RestController 의 비교

Spring 개발에 있어서 일반적으로 사용하는 @Controller annotation 을 사용하게 되면 아래와 같은 부분을 주의해야 한다. 아래 CommonController2 파일을 보면 /2 에 대해서는 String 을 return 하는데, 이에 대한 response body 는 정의되어 있지 않고, /demo2에 대해서는 ResponseBody annotation이 붙어 있으므로 그 자체를 body 로 인식하고 return 한다. 

향후 ModelAndView를 사용하게 되면 @Controller를 주로 사용하게 될 것이고, 하나의 Controller 안에 ModelAndView 를 return 하는 것과 Contents 자체를 return 하는 method 가 혼재하게 되면 Contents 를 return 하는 method는 반드시 @ResponseBody 를 붙여서 ajax call 등에 대처해 주어야 한다.

```java
package com.simplify.sample.controller;
 
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
 
@Controller
public class CommonController2 {
 
    @RequestMapping("/2")
    public String root_test() throws Exception{
        return "Hello Root(/)";
    }
 
    @RequestMapping("/demo2")
    public @ResponseBody String demo_test() throws Exception{
        return "Hello demo(/demo)";
    }
}
```

## 실행 후 확인

위와 같이 작업한 뒤에, 프로젝트를 Spring boot app 으로 실행시킨 뒤, 브라우져에서 아래와 같이 나오는지 확인한다. (https://localhost:8080/demo 에서도 확인한다)

![](/assets/images/posts/old/img/post/2018-09-27-spring-boot-03-controller/spring-boot-03-controller-00002.png)

## 참고

만약 8080 포트를 다른 용도로 사용하고 있거나, 개인적인 이유로 이 포트를 변경하고 싶다면, application.properties 파일에 아래와 같이 port 정보를 명시해 주면 된다.

> server.port=80


GitHub 소스 위치 : https://github.com/Simplify-study/SpringBootSample.git

출처: https://4urdev.tistory.com/43 [Simplify]
