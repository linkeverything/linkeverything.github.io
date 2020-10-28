---
title: "Springboot 에서 Custom Annotation 활용하기 (AOP, Aspect Object Programming)"
categories: [dev, backend]
tag: [spring, aop, annotation]
last_modified_at: 2020-10-26
published: false
---
일반적인 Java Programming 을 하다가, Spring Framework, Spring Boot 를 사용하여 개발을 진행하면, 가장 크게 다가오는 것이 Annotation이라고 할 수 있습니다. 현재는 다양한 분야에서 이 Annotation을 이용하여 여러 기능들을 구현하게 하고 있어, 흔한 일들이 되었지만, Spring이 처음 개발/배포되던 때만 하더라도 꽤나 신기한 부분이었다고 말할 수 있습니다. 

물론 Annotation이 Spring의 특징이라고 할 수는 없지만, 특히 개발 과정에서 그 편의성을 더해주고, 특정 기능에 대해서 공통적인 기능을 구현/배포(혹은 강제)할 수 있다는 점에서 그 사용범위가 넓다고 볼 수 있습니다. 

이렇게 <mark style='background-color: #dcffe4'>Annotation을 통해 공통의 기능들을 개발하여 중복을 최소화하고, 개발 편의성을 도모</mark>하는 것이 AOP(Aspect Object Programming) 의 시작입니다.

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/com.simplify.api){:target="_blank" .btn .btn--primary}

<br/>

## AOP?

<br/>

#### 개념/정의

AOP(Aspect Object Programming)는 관점 지향 프로그래밍 이라고 부르는데, <mark style='background-color: #dcffe4'>어떤 기능 하나를 분해하여, 주 로직과 부가적인 부분으로 나누어 바라보고, 부가적인 부분은 별도로 분리하여 하나하나의 모듈로 분리</mark>하는 것을 말합니다.

게시판에 글쓰기 기능 예로 들어 보겠습니다. 게시판에 글쓰기의 주 기능은,

- 어떤 게시판에
- 누가
- 어떤 제목으로
- 어떤 내용으로
- 언제

글을 적었느냐의 정보를 가지고 Database에 저장하는 것입니다. 그런데 실제로 게시판 기능을 개발하다 보면 여기에 부가적인 기능들이 필요할 것입니다.

- [ ] 게시판 이름이 존재하는가
- [x] 사용자는 로그인 상태인가(session)
- [x] 사용자는 유효한 사용자인가(권한 등)
- [x] 제목과 내용에는 부적절한 단어가 포함되어 있지는 않은가
- [ ] 날짜는 유효한가(미래이거나 과거를 가리키고 있지 않은가)

아마도 위에서 이야기한 것 보다 더 많은 정보들이 필요할 것이고, validation 이나 auth 와 관련한 부분들일 것입니다. 혹은 그 어떤 부가 기능일 수도 있습니다. 어떤 기능이던지 이러한 부가 기능들이 있는데, 이것들 중 위에 체크 표시한 것들은 다른 기능들에서도 사용될 수 있어, 공통 기능으로 분리하게 될 것 같습니다. 

과거에는 이러한 부분들을 `CommonUtil` 과 같은 공통 모듈로 분리하여 사용했었고, 이 방법도 나쁜 방법은 아닙니다. 그러나 공통 기능이 변경되면 그것을 사용하는 모든 부분에서 모두 수정해 주어야 한다는 점에서 유지보수가 불편하다는 단점이 있습니다. 

AOP는 이러한 공통 부분을 뽑아 모듈화하고, 주 로직(기능)에서 분리하여 재사용 가능하게 하는 Programming 방법입니다. 결국 위와 같은 각각의 기능들을 Annotation으로 분리/개발하여 체크 로직이 들어가는 부분 - 보통의 경우 Spring 에서는 Controller가 될 것인데 - 에 넣어 그 기능을 할 수 있게 하는 것입니다. 

설명이 복잡하지만, 특정 기능을 하는 것을 하나하나의 별도 모듈로 개발하고, 이를 Annotation(Aspect) 으로 개발하여 필요한 곳에 `@annotation`형태로 넣을 수 있게 하겠다는 것이 AOP의 편리한 점이라고 할 수 있습니다.

<br/>

#### Spring AOP?

스프링에서의 AOP는 접근 제어, 부가기능을 추가하기 위해서 프록시 패턴 기반의 AOP구현체를 사용한다는 것이 특징입니다. 당연하게도 Spring Bean 에만 AOP를 적용하는 것이 가능하며, AOP 자체의 모든 기능을 제공하는 것이 아니라 IoC와 연동하여 기능 개선을 하는 데 목적이 있다고 볼 수 있습니다. (차용한 내용)

<br/>

## Spring Boot 에서 AOP 구현하기

<br/>

#### library

Spring Boot 에서 AOP를 사용하려면 spring-boot-starter-aop 라이브러리가 필요합니다. 이를 위해 아래 코드를 pom.xml 이나 build.gradle에 추가합니다.

**pom.xml**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
    <version>2.3.4.RELEASE</version>
</dependency>

```
**build.gradle**
```gradle
compile 'org.springframework.boot:spring-boot-starter-aop'
```

2020.10.28 현재 Springboot 의 최신 버전인 2.3.4.RELEASE 에서는 해당 라이브러리가 포함되어 있기 때문에 별도로 라이브러리를 추가해줄 필요가 없습니다.
{: .notice--warning}

<br/>

#### @EnableAspectJAutoProxy

Springboot의 Application.java 파일 맨 앞에 @EnableAspectJAutoProxy 를 붙여 줍니다. 이 annotation은 AOP 기능을 사용하겠다는 의미로 사용됩니다. 

2020.10.28 현재 Springboot 의 최신 버전인 2.3.4.RELEASE 에서는 [Stackoverflow 에서 가이드](https://stackoverflow.com/questions/48625149/spring-aop-works-without-enableaspectjautoproxy) 한 것 처럼 이 annotation을 추가하지 않습니다.
{: .notice--warning}

<br/>

## 샘플 구현하기

여기서는 참고자료에 있는 금칙어 체크 로직을 넣어 구현하도록 하겠습니다. 설명 순서만 다를 뿐 해당 샘플을 그대로 차용했다는 점을 미리 말씀드립니다. 아래 순서대로 하셔도 좋고 출처에 있는 자료를 활용해도 됩니다.

<br/>

#### @Aspect / @interface 생성

이제 @Aspect와 @interface를 생성하겠습니다. 아래 사즌처럼 package를 구성하고 interface 를 아래와 같이 생성합니다.

![](/assets/images/2020-10-26-spring-aop/capture%202020-10-28%20PM%202.22.59.png)

```java
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface ForbiddenWordCheck {
    String param() default "paramsPost.content";
    Class<?> checkClazz() default ParamsPost.class;
}
```

interface 앞에 `@` 가 있는 것이 특징입니다. 그 앞에 다음의 내용이 있습니다. 

- `@Target` : 어떤 부분에 대해서 annotation이 감지하여 동작할지를 결정합니다. 여기서는 `ElementType.METHOD` 라고 주어 method 에 대해서 AOP 기능을 적용하는 것으로 정의하였습니다. 필요에 따라서는 다른 타입으로 지정할 수 있으며, 여러 개를 지정하여 여러 타입에 대해서도 적용이 가능합니다.

- `@Retention(RetentionPolicy.RUNTIME)` : annotation의 생명주기에 관한 내용입니다. 여기서와 같이 RUNTIME 을 지정하면 실행중인 동안에 정보를 가져갈 수 있다는 것을 의미합니다.

- `@interface` : interface앞에 `@`를 붙여 annotation 임을 의미합니다.

- `String param() default "paramsPost.content";` : 

- `Class<?> checkClazz() default ParamsPost.class;` : 


<br/>

#### Service에 반영

<br/>

#### 후처리

<br/>

## 참고자료 및 출처

- <https://engkimbs.tistory.com/746>
- <https://daddyprogrammer.org/post/11356/springboot2-forbidden-word-by-aop-annotation/>
- <https://jeong-pro.tistory.com/171>
- <https://stackoverflow.com/questions/48625149/spring-aop-works-without-enableaspectjautoproxy>