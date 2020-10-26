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

<br/>

## Spring Boot 에서 AOP 구현하기

<br/>

#### library

<br/>

#### @EnableAspectJAutoProxy

<br/>

## 샘플 구현하기

<br/>

#### @Aspect / @interface 생성

<br/>

#### Service에 반영

<br/>

#### 후처리

<br/>

## 참고자료 및 출처

- <https://engkimbs.tistory.com/746>
- <https://jeong-pro.tistory.com/171>
- <https://stackoverflow.com/questions/48625149/spring-aop-works-without-enableaspectjautoproxy>