---
title:      "SpringBoot - (5) Create View"
categories: [old]
last_modified_at:       2018-10-01
tags:
  - Spring
  - SpringBoot
---

## 들어가며

앞서 만든 Controller는 데이터 형태를 return 해 주는 것이라고 볼 수 있습니다. String 을 return 함수로 갖는 controller 함수에 request mapping 을 해 두고 브라우져에서 확인했지만, response string 을 브라우져가 화면에 보여준 것이라고 볼 수 있습니다. 따라서 html, jsp 형태를 화면에 정상적으로 보여주기 위해서는 약간의 작업이 더 필요합니다. 

## View 연동하기

##### Dependency 추가하기


기존 프로젝트의 pom.xml에 아래 내용을 추가합니다. 

```xml
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
        </dependency>
```

위와 같이 jstl 과 jasper를 추가합니다. 

##### 경로 설정 값 추가하기

단순히 생각하기에 resource 폴더에 어딘가에 jsp파일을 두고 이를 불러다가 주면 될 것 같지만, 이 부분에 대해서도 상당 부분 자동화가 되어 있습니다. 즉 설정에 의해서 jsp 파일의 최상위 root를 지정해 줄 수 있고, 이를 바탕으로 상대 경로로만 return 해 주면 화면에 해당 jsp가 보입니다. 

우리는 설정 값들을 모두 application.properties파일에 추가 해 왔기 때문에 이 곳에 추가합니다. 물론 이를 더 세밀하게 설정하고 싶고, 손으로 직접 수정하고 싶다면 MVC handle 하는 bean 을 직접 설정해 주어도 무방합니다. 그러나 경험상 자주 바뀔 가능성이 적고, 다수의 폴더를 바라볼 경우도 없기 때문에 별도로 설정하는 과정은 생략하도록 하겠습니다. 그저 기본 로드되는 bean 들을 충분히 활용해 보고자 합니다. 


application.properties 파일을 열어서 아래 내용을 추가합니다. 

```properties
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```

prefix 가 폴더에 해당하는 내용이고, suffix가 jsp 확장자에 해당합니다. 개발 환경이나 여러 정책에 맞게 잘 조정해 주면 됩니다. 참고로 prefix의 상위 폴더는 src/main/webapp/ 폴더입니다. 이 폴더는 jstl, jasper 에서 기본으로 가지고 있는 정보이니, 변경하려면 큰 작업이 필요합니다. (기회가 되면 포스팅 하겠습니다.)

##### jsp 파일 생성하기

/src/main/webapp/WEB-INF/jsp/main.jsp 경로에 jsp 파일을 생성하고 아래처럼 작성 해 두었습니다. 여기서부터는 화면 개발 하는 과정과 동일합니다. 

```html
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Jsp page sample</title>
</head>
<body>
 
jsp body sample
 
</body>
</html>
```

##### Controller 생성하기

이제 controller 를 생성해 주면 됩니다. prefix 와 suffix 를 제외한 파일명 부분만 넣어주면 되는 상황이니, 그렇게 넣어주고 String return 하는 구조로 생성합니다. 

```java
package com.simplify.sample.jsp;
 
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
 
@Controller
public class JspController {
 
    @RequestMapping("/jsp")
    public String jsp() throws Exception {
        return "main";
    }
    
    @RequestMapping("/abcd")
    public String abcd() throws Exception {
        return "abcd";
    }
    
    @RequestMapping("/abcd2")
    public @ResponseBody String abcd2() throws Exception {
        return "abcd2";
    }
}
```
## 결과 확인

![](/assets/images/posts/old/img/post/2018-10-01-spring-boot-05-model-and-view/spring-boot-05-model-and-view-00001.png)

## 주의할 점 ★

위에서 return string 은 이제 더 이상 string으로서의 의미를 갖지 못하고 경로 안의 특정 값일 뿐입니다. 모든 return 에 대해서 prefix와 suffix 를 주게 되어 더 이상 이전처럼 값을 return 할 수 없는 상황이 됩니다. 

이는 위 controller 에서 두 번째 URL, /abcd를 호출하면 아래와 같은 에러가 나타나는 것을 보면 알 수 있습니다. 

![](/assets/images/posts/old/img/post/2018-10-01-spring-boot-05-model-and-view/spring-boot-05-model-and-view-00002.png)

따라서 이전처럼 데이터 형태로 결과를 반환하고 싶다면 @ResponseBody 를 꼭 명시해 주면 됩니다. 위 /abcd2가 이러한 예 입니다. 이렇게 명시를 해 주면 해당 url을 찾다가 에러가 발생하지 않고, 앞서 진행한 것 처럼 데이터 형태로 return 하게 됩니다. 

![](/assets/images/posts/old/img/post/2018-10-01-spring-boot-05-model-and-view/spring-boot-05-model-and-view-00003.png)



## Model And View 로 이용하기

이 부분은 별도의 설명이 필요없습니다. 앞서 dependency 추가한 jstl 에서 이 부분까지 커버하고 있기 때문입니다. 따라서 그저 controller 에서 ModelAndView 를 return 하게 만들고, 그 함수 안에서 적절한 object를 구해서 넣고 return 하면 됩니다. 

##### Controller

```java
    @RequestMapping("/mav")
    public ModelAndView mav() throws Exception{
        ModelAndView mav = new ModelAndView("mavSample");
        
        mav.addObject("key", "fruits");
        
        List<String> fruitList = new ArrayList<String>();
        
        fruitList.add("apple");
        fruitList.add("orange");
        fruitList.add("banana");
         
        mav.addObject("value", fruitList);
        
        return mav;
    }
```

##### /src/main/webapp/WEB-INF/jsp/mavSample.jsp

```html
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
 
${key}
 
${value}
 
</body>
</html>
```

##### 결과

![](/assets/images/posts/old/img/post/2018-10-01-spring-boot-05-model-and-view/spring-boot-05-model-and-view-00004.png)


## 최종모습

![](/assets/images/posts/old/img/post/2018-10-01-spring-boot-05-model-and-view/spring-boot-05-model-and-view-00005.png)


GitHub 소스 위치 : https://github.com/Simplify-study/SpringBootSample.git

출처: https://4urdev.tistory.com/43 [Simplify]
