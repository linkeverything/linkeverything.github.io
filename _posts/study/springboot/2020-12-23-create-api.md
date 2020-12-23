---
title: "Spring Boot 로 API 생성하기"
categories: [study, springboot]
tag: [create api]
last_modified_at: 2020-12-23
---
Spring Boot를 이용하여 프로그램을 개발하는 데 있어서 API를 개발하는 것은 당연한 부분입니다. 다른 시스템과의 Communication목적이거나, 혹은 화면(front-end)을 개발하고 그 화면에서 호출할 API를 제공하는 목적으로도 API개발은 필수적입니다. 물론 Spring Boot를 이용하여 스스로 동작하는, 배치 서비스 같은 시스템 개발을 할 수도 있습니다.

사실 제가 앱 개발을 한창 하던 시절에도, 서버 측 개발을 병행하지 않으면 Open API 정도만 사용할 수 있고, 사용자 설정을 저장/백업 하거나, 특별한 기능을 구현하는 등에서 많은 제약이 따라오는 것 같아서 Back-end 개발 쪽으로 관심을 돌리게 된 것도 있습니다. 이제는 Front-end 보다는, Back-end 쪽에 더 가까워져 있습니다만, 이 경우처럼 API 의 개발은 시스템에서 아주 중요한 부분이고, 이 글을 보는 대부분의 사람들이 이러한 목적을 가지고 있을 것입니다. 

예를 들어, 게시판 기능을 가진 시스템이라고 할 경우, 테이블에 저장되어 있는 게시물 목록을 조회하고, 새 글을 작성하고, 글을 수정하고, 글을 삭제하는 등의 일련의 작업들이 다 화면에서 호출하는 API를 통해서 이루어집니다. 물론 JSP 등을 이용해서 API를 사용하지 않고, 화면 내에서 모든 기능들을 다 구현하고 동작하게 할 수도 있겠지만, 확장성이나 개발, 유지보수 측면에서는 별도로 분리하는 것을 추천합니다.

여기서는 API를 개발하고 동작하게 하는 것에 초점을 두고 진행합니다. 실제로 API를 하나 만들게 되면 이정도 수준에서 멈추는 것이 아니라 더욱 많은 작업들이 있겠지만, 그런 부분은 향후에 다른 것들과 더불어서 작업하도록 하겠습니다.

[Download Sample code from GitHub](https://github.com/linkeverything/studySpringBoot/tree/0010-create-api){:target="_blank" .btn .btn--primary}

> 위 경로에서 다운로드 받아서 진행하는 경우, branch 를 반드시 확인하시고 다운로드(checkout) 받으시기 바랍니다. 각 게시글에 맞는 branch 로 생성되어 있으므로 해당 branch를 받는 것이 이해해 도움이 됩니다.

## 필요한 라이브러리 

API를 생성하는 것을 이야기할 때, API는 http request에 대해서만 이야기하겠습니다. 다른 형태의 요청을 처리하는 back-end server를 생성할 수 있고, 각 생성방법이 다양하게 있지만, 여기서는 spring boot 에서 가장 일반적으로 사용하는 http request에 대해서만 논하고 기회가 된다면 다른 게시물을 통해서 다른 요청에 대한 처리도 담아 보도록 하겠습니다. 

#### 프로젝트 실행

앞서 생성한 프로젝트 파일의 실행은 IntelliJ IDEA 기준으로 우측의 *Gradle > tasks > application > bootRun* 을 더블클릭하는 것으로 할 수 있습니다. 물론 `~Application.java` 파일에서 우클릭하고 `main()` 을 실행하도록 해도 동일합니다만, gradle 실행법을 기본으로 가져가려고 합니다.

![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 AM 11.26.34.png)

현재 프로젝트에서 위 처럼 실행해 보면, 아래와 같이 실행하고 바로 종료되는 것을 확인할 수 있습니다. 당연히 추가로 아무런 코딩을 하지 않았기 때문에 기본 클래스가 실행되었고, spring boot 라이브러리가 포함되었지만, 아무런 동작 없이 종료된 것을 볼 수 있습니다.

![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 PM 12.50.08.png)

#### build.gradle 파일 수정

gradle 프로젝트를 생성하면 `build.gradle` 파일이, maven 프로젝트를 생성하면 `pom.xml` 파일이 해당 프로젝트의 구조를 설계하는 파일이 됩니다. 여기서 말하는 구조란, 빌드에 필요한 이름, 버전 명 등의 여러 property 값들을 비롯하여 빌드를 어떤 형태로 할 지, 무슨 플러그인으로 빌드할지에 대한 정보들을 이야기합니다. 또한 이 프로젝트가 구동되기 위해서 필요한 라이브러리들을 명시해 줌으로써 gradle/maven 빌드 시점에 원격으로 어떤 라이브러리를 다운로드 받아 설치되고 함께 빌드되어야 하는지도 명시합니다.

`build.gradle` 파일을 열고, `dependencies` 부분에 다음의 내용을 추가해 줍니다. 

```
implementation 'org.springframework.boot:spring-boot-starter-web'
```

사실 처음부터 이 라이브러리를 사용하겠다고 한다면 `start.spring.io` 에서 바로 추가하고 시작해도 무방합니다(동일한 결과). [^1]

#### 다시 실행해 보기

이제 라이브러리를 추가했으면 앞서 실행한 것 처럼 `bootRun` 을 실행해 봅니다. 앞서 실행한 것 과는 다르게, 실행한 프로그램이 그대로 실행중인 상태로 머물로 있습니다. 소위 이야기하는 <mark style='background-color: #fff5b1'>서버</mark>가 실행되어 서비스 형태로 실행중인 채 남아있으며 우리가 원하는 상태가 되었습니다. 

![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 PM 12.56.23.png)

실질적으로 <mark style='background-color: #fff5b1'>API 서버</mark>라고 하는 것들 역시 대기 상태로 있으면서, 주기적으로 어떠한 요청이 들어오는지 빠르게 loop를 돌면서 확인하고, 뭔가 요청이 있으면 그에 따른 동작을 하는 형태라고 생각하면 됩니다. 위 실행 로그를 보면 `8080` 포트에 대해서 서버가 실행중임을 알 수 있습니다. 

이렇게 Spring boot에서는 라이브러리만 추가하고 아무런 작업을 해주지 않아도, 서버 형태로 동작하는 것을 알 수 있습니다. 포트번호는 앞서 살펴본 바와 같이 `8080`이 기본값으로 동작하고 있으며, 이는 내장 Tomcat이라는 형태의 서버임을 확인할 수 있습니다. 

## API 설계

이제 API를 실제로 만들어 보도록 하겠습니다. 여기서는 간단한 예제이기 때문에 그대로 따라하는 식으로 진행하면 됩니다. 

#### Controller 생성하기

API를 생성하기 위해서는 Controller를 생성해야 합니다. 기본 패키지 경로에 TestController.java 라는 이름으로 파일을 생성하고 아래와 같은 내용을 입력합니다.

```java
package com.simplify.studySpringBoot;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TestController {
    
    @GetMapping(value = "/test")
    public String testApi(){
        return "Hello API";
    }
}
```

각 내용에 대해서 조금 설명하면 다음과 같습니다. 

- `@RestController` : Rest API 를 설계하기 위해서 사용하는 내용입니다. Spring 에서 사용하는 `@Controller`를 사용해도 되지만, Return 하는 내용에 대한 처리가 조금 더 간단한 `@RestController`를 사용해 보았습니다.
   
  당연하게도 이 annotation을 사용하기 위해서는 위와 같이 *org.springframework.web.bind.annotation.RestController* 를 import해야 합니다.

- `@GetMapping(value = "/test")` : HTTP request 의 종류에는 GET / POST / DELETE / UPDATE 등이 있습니다만, 여기서는 간단히 GET을 사용하였습니다. 

  또한 이 annotation의 value 로 */test* 를 지정하여 주어, 실제 호출 경로가 
  ```
  http://{hostname or ip address}/test
  ```
  와 같이 생성되도록 지정하였습니다. 

즉 *http://{hostname or ip address}/test*를 호출하게 되면 Hello API 라는 문자열이 return message로 돌아오게 되는 형태입니다. 여기서 사용된 함수 명인 testApi라는 이름은 크게 중요하지 않습니다. Java에서 이야기하는 명명 규칙, 그리고 클래스 내에서 중첩되지만 않는다면 어떠한 형태의 이름을 사용해도 무방합니다만, 각 프로젝트에 맞게 네이밍 룰을 적용하여 API가 유추될 수 있는 이름으로 정하는 것이 유지보수에 유리합니다.

#### 실행 및 확인

이제 다시 기존에 실행중이던 프로세스를 종료시키고, bootRun을 실행해 줍니다. 실행 로그 상으로는 앞서 실행한 것과 차이가 없어 보입니다. 이제 브라우저를 열어 주소창에 아래와 같이 입력합니다.

```
http://localhost:8080/test
```

주소 중에서 `localhost`는 `127.0.0.1`로 바꿔서 적어도 무방합니다.
{: .notice--info}

![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 PM 1.16.31.png)

그러면 우리가 적어줫던 return 문자열인 **Hello API**가 나타남을 확인할 수 있습니다. 

#### 파라미터를 넣어 요청 해 보기 - GET

앞서 설명한 GET방식의 HTTP request에서 파라미터를 넣는 방식은 다음과 같이 두 가지 정도가 있을 수 있습니다. 

- URL의 일부에 파라미터가 포함된 경우
- URL의 마지막에 query string 이라고 말하는 ?aaa=bbb 와 같이 key=value 형태로 파라미터를 넣는 경우

앞서 만든 TestController에 다음 내용을 추가합니다.

```java
    @GetMapping(value = "/{firstPath}/test/{secondPath}")
    public String testApiWithPathVariable(@PathVariable() String firstPath,
                                          @PathVariable() String secondPath){
        return "Hello API - variables: 1)" + firstPath + " 2)" + secondPath;
    }

    @GetMapping(value = "/test2")
    public String testApiWithRequestParam(@RequestParam String abc){
        return "Hello API - requestParam: abc=" + abc;
    }
```

- `@GetMapping(value = "/{firstPath}/test/{secondPath}")` : URL 자체에 대해 parameter로 인식하는 방식입니다. 중간에도 들어갈 수 있다는 점을 강조하기 위해서 위와 같이 예제로 만들었을 뿐, 중간과 끝은 같이 사용하는 경우는 많지는 않습니다. 각 `{}` 안에 들어가는 내용은 method 의 parameter로 들어오는 String 값 두 개의 이름과 일치해야 정상적으로 찾아갑니다. 물론 `@PathVariable` 부분에 인자로 설정해 줄 수 있으나 굳이 그렇게 하지 않고 직관적으로 변수명과 일치시켰습니다.

  실행 결과는 아래와 같습니다.

  ![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 PM 1.39.11.png)

- `@GetMapping(value = "/test2")` : 이 선언부는 위 `/test`와 동일한 형태입니다. 그렇지만 함수 parameter로 `@RequestParam` 을 넣어주었고, URL뒤에 Get 요청에 대한 파라미터를 분석하여 보여줍니다. 여기서도 마찬가지로 method 의 parameter로 들어오는 변수명과 동일한 이름의 파라미터를 처리하도록 해 두었습니다. 

  실행 결과는 아래와 같습니다.

  ![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 PM 1.39.30.png)

#### 파라미터를 넣어 요청 해 보기 - POST

POST요청에 대한 부분은 가볍게 적고 넘어가겠습니다. 더욱 복잡하게 하려면 VO를 만들어 넣어 진행하는 것이 맞으나, 여기서는 그냥 간단히 하고 넘어가겠습니다. 
{: .notice--warning}

HTTP request의 여러 가지 형태 중에서 POST호출에 대해서 구현해 보겠습니다. 보통의 경우 GET은 조회(Retrieve)하는 경우에 주로 사용하고 POST는 생성(Create) 등의 파라미터 요청 시에 많이 사용합니다.

앞서 생성했던 TestController에 아래 내용을 추가합니다.

```java
    @PostMapping(value = "/testpost")
    public String testApiPost(@RequestBody String body){
        return "Hello API - requestParam: body=" + body;
    }
```

- `@PostMapping(value = "/testpost")` 여기서는 Post 요청을 처리할 것이라고 선언합니다. 
- `@RequestBody String body` : 요청에 들어오는 Parameter 중에서 body라는 이름의 내용을 여기에 담아오겠다는 의미입니다.

POST 요청은 브라우저에서 호출하기에는 한계가 있어, 여기서 테스트는 [Httpie](https://httpie.io/)라는 툴을 이용하였습니다. 

![](/assets/images/posts/study/springboot/2020-12-23-create-api/capture 2020-12-23 PM 2.03.32.png)

위와 같이 출력되는 것을 확인할 수 있습니다. POST방식에 대해서는 더욱 깊은 내용이 필요하므로 여기서는 다루지 않고, 향후 내용을 추가해서 다루도록 하겠습니다. 다만 여기서 주의할 것이, 기본적으로 POST api를 생성하고 호출하면 <mark style='background-color: #dcffe4'>JSON형태로 호출</mark>된다는 점입니다.

## Open API에 대해

흔히 이야기하는 Open API라는 것은 기본적으로 보안 요소(로그인 등)를 갖추지 않고 누구나 호출해서 그 값을 가져갈 수 있는 것들을 이야기 합니다. 물론 인가된 사용자에게 API호출 건수 등에 대한 제한을 두지 않는 경우도 Open API 로 통칭하는 경우도 있긴 합니다만, 일반적으로는 그러습니다.

## API 설계에 대한 몇 가지

## 참고자료 및 출처

- <https://start.spring.io/>
- <https://httpie.io/>

[^1]: start.spring.io 페이지에서 우측 dependencies 부분에 'web' 으로 검색하여 추가하면 됩니다.