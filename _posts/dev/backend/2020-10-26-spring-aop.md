---
title: "Springboot 에서 Custom Annotation 활용하기 (AOP, Aspect Object Programming)"
categories: [dev, backend]
tag: [spring, aop, annotation]
last_modified_at: 2020-10-26
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

#### @interface 생성

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

아래 두 부분은 실재 Aspect class에서 사용하게 될 부분으로서, 이 예시에 걸맞게 설정된 것입니다. ParamsPost 는 게시판에 글을 남기는 것을 염두에 둔 객체로서 아래 그 내용을 첨부합니다. (출처의 다른 예제에서 이어지는 내용이라, 상세한 내용이 필요하시면 [출처](https://daddyprogrammer.org/post/11356/springboot2-forbidden-word-by-aop-annotation/)를 참고하시기 바랍니다.)

- `String param() default "paramsPost.content";` : 대상이 되는 method의 parameter 중에 paramsPost 라는 이름의 객체에서 content 항목을 뽑아내는 것을 기본으로 하는 param() 함수를 의미합니다.

  만약, 이 부분이 객체가 아닌 파라미터, 예를 들어 String 파라미터에 대해서 param() 함수가 동작하게 하려면 `@ForbiddenWordCheck(param="{파라미터명}")` 형태로 넣으면 됩니다.

- `Class<?> checkClazz() default ParamsPost.class;` : 체크할 대상이 되는 클래스 정보를 넣어둡니다. 아래 부분에서 설명하겠지만 객체에 대해서 안전한 처리를 하는 대신 파라미터를 고정적으로 사용한다면 안적어도 될 것 같습니다(확장성을 고려한다면 넣어주는 것이 좋습니다).

```java
@Getter
@Setter
@NoArgsConstructor
public class ParamsPost {

    @NotEmpty
    @Size(min=2, max=50)
    @ApiModelProperty(value = "작성자명", required = true)
    private String author;

    @NotEmpty
    @Size(min=2, max=100)
    @ApiModelProperty(value = "제목", required = true)
    private String title;

    @Size(min=2, max=500)
    @ApiModelProperty(value = "내용", required = true)
    private String content;
}
```

<br/>

#### @Aspect 생성

아래와 같이 `@Aspect` annotation을 가진 ForbiddenWordCheckAspect 클래스를 생성합니다.

```java
@Slf4j
@Aspect
@Component
public class ForbiddenWordCheckAspect {

    @Before(value = "@annotation(forbiddenWordCheck)")
    public void forbiddenWordCheck(JoinPoint joinPoint, ForbiddenWordCheck forbiddenWordCheck) throws Throwable {

        log.debug(this.getClass().getSimpleName() + " - forbiddenWordCheck()");

        String[] param = forbiddenWordCheck.param().split("\\.");
        String paramName;
        String fieldName = "";
        if (param.length == 2) {
            paramName = param[0];
            fieldName = param[1];
        } else {
            paramName = forbiddenWordCheck.param();
        }

        Integer parameterIdx = getParameterIdx(joinPoint, paramName);
        if (parameterIdx == -1) {
            throw new IllegalArgumentException();
        }

        String checkWord;
        if (StringUtils.isNotEmpty(fieldName)) {
            Class<?> clazz = forbiddenWordCheck.checkClazz();
            Field field = clazz.getDeclaredField(fieldName);
            field.setAccessible(true);
            checkWord = (String) field.get(joinPoint.getArgs()[parameterIdx]);
        }else{
            checkWord = (String) joinPoint.getArgs()[parameterIdx];
        }

        checkForbiddenWord(checkWord);
    }

    private Integer getParameterIdx(JoinPoint joinPoint, String paramName) {
        MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
        String[] parameterNames = methodSignature.getParameterNames();
        for (int i = 0; i < parameterNames.length; i++) {
            String pName = parameterNames[i];
            if (pName.equals(paramName)) {
                return i;
            }
        }
        return -1;
    }

    private void checkForbiddenWord(String content) {
        List<String> forbiddenWords = Arrays.asList("개새끼", "쌍년", "씨발");
        Optional<String> forbiddenWord = forbiddenWords.stream().filter(content::contains).findFirst();
        if (forbiddenWord.isPresent()) {

            log.debug(this.getClass().getSimpleName() + " " + forbiddenWord.get());

            throw new CForbiddenWordException(forbiddenWord.get());
        }
    }
```

위에서 보면 `@Before(value = "@annotation(forbiddenWordCheck)")` 를 통해서 <mark style='background-color: #fff5b1'>@forbiddenWordCheck 라는 annotation 앞에서 이 처리가 이루어짐</mark>을 정의한 것을 볼 수 있습니다.[^1]

[^1]: 이러한 표현식을 pointcut expression 이라고 하며, 다음과 같이 사용할 수 있습니다. 

    |Pointcut|JoinPoints|
    |--------|----------|
    |execution(public * *(..))|public 메소드 실행|
    |execution(* set*(..))|이름이 set으로 시작하는 모든 메소드명 실행|
    |execution(* set*(..))|이름이 set으로 시작하는 모든 메소드명 실행|
    |execution(* com.xyz.service.AccountService.*(..))|AccountService 인터페이스의 모든 메소드 실행|
    |execution(* com.xyz.service.*.*(..))|service 패키지의 모든 메소드 실행|
    |execution(* com.xyz.service..*.*(..))|service 패키지와 하위 패키지의 모든 메소드 실행|
    |within(com.xyz.service.*)|service 패키지 내의 모든 결합점 (클래스 포함)|
    |within(com.xyz.service..*)|service 패키지 및 하위 패키지의 모든 결합점 (클래스 포함)|
    |this(com.xyz.service.AccountService)|AccountService 인터페이스를 구현하는 프록시 개체의 모든 결합점|
    |target(com.xyz.service.AccountService)|AccountService 인터페이스를 구현하는 대상 객체의 모든 결합점|
    |args(java.io.Serializable)|하나의 파라미터를 갖고 전달된 인자가 Serializable인 모든 결합점|
    |@target(org.springframework.transaction.annotation.Transactional)|대상 객체가 @Transactional 어노테이션을 갖는 모든 결합점|
    |@within(org.springframework.transaction.annotation.Transactional)|대상 객체의 선언 타입이 @Transactional 어노테이션을 갖는 모든 결합점|
    |@annotation(org.springframework.transaction.annotation.Transactional)|실행 메소드가 @Transactional 어노테이션을 갖는 모든 결합점|
    |@args(com.xyz.security.Classified)|단일 파라미터를 받고, 전달된 인자 타입이 @Classified 어노테이션을 갖는 모든 결합점|
    |bean(accountRepository)|"accountRepository" 빈|
    |!bean(accountRepository)|"accountRepository" 빈을 제외한 모든 빈|
    |bean(*)|모든 빈|
    |bean(account*)|이름이 'account'로 시작되는 모든 빈|
    |bean(*Repository)|이름이 "Repository"로 끝나는 모든 빈|
    |bean(accounting/*)|이름이 "accounting/"로 시작하는 모든 빈|
    |bean(&ast;dataSource) 또는 bean(&ast;DataSource)|이름이 "dataSource" 나 "DataSource" 으로 끝나는 모든 빈|

각 부분에 대한 상세 설명은 다음과 같습니다. 

1. forbiddenWordCheck 이라는 annotation이 들어오면, param() 함수를 통해 <mark style='background-color: #fff5b1'>앞서 지정했던 것 처럼 paramsPost.content</mark> 라는 것을 뽑아냅니다. 그리고 그 것을 `.` 으로 split 합니다. 

2. 그 결과로 나온 개수를 확인합니다. 2개라면 `클래스명.필드명` 형태라고 가정하고 각각을 변수에 넣고, 1개라면 String이라고 가정하고 param() 의 결과를 그대로 변수에 넣습니다. 
   
3. paramName 이라는 변수, 즉 파라미터 이름으로 되어있는 것을 이용해서 몇 번째 파라미터인지를 확인합니다. (getParameterIdx()) 이 과정에서 index를 구하지 못하면 <mark style='background-color: #ffdce0'>exception 을 발생</mark>시킵니다.

4. 해당 파라미터를 객체에서 뽑아냅니다. 그 과정에서 JoinPoints 에서 앞서 구한 index가 사용됩니다. 만약 객체형이 아니라면 그냥 JoinPoints 에서 뽑아내면 됩니다.

5. 금칙어가 포함되어 있는지 확인하고 포함되어 있다면 <mark style='background-color: #ffdce0'>exception 을 발생</mark>시킵니다.

이 과정에서 발생시킨 exception은 아래 후처리 부분에서 exception handling하여 처리합니다.

<br/>

#### Service에 반영

이제 이러한 기능(금칙어 확인)에 대하여 Service에 반영합니다. service에 반영하는 것에 대해서도 여러 의견이 있지만, service 혹인 controller 에 대해서 프로젝트 상황에 맞는 곳에 지정하면 됩니다. 

```java
...

    @ForbiddenWordCheck
    public Post writePost(String uid, String boardName, ParamsPost paramsPost){

...
```

`@ForbiddenWordCheck` annotation을 원하는 <mark style='background-color: #fff5b1'>method</mark> 앞에 선언하면 그 처리는 완료됩니다.

<br/>

#### 후처리

`@Aspect` 에서는 특정한 return을 하기가 매우 껄끄러운 상황이고 문제가 없다면 패스 하는 형태가 될 것입니다. 따라서, 문제가 있을 때에는 오류를 return 하는 것이 아니라 exception을 throw 하는 형태로 구현해야 합니다. 

```java
@RequiredArgsConstructor
@RestControllerAdvice
public class ExceptionAdvice {

    // 생략...

    @ExceptionHandler(CForbiddenWordException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public CommonResult forbiddenWordException(HttpServletRequest request, CForbiddenWordException e) {
        return responseService.getFailResult(Integer.valueOf(getMessage("forbiddenWord.code")), getMessage("forbiddenWord.msg", new Object[]{e.getMessage()}));
    }

    // 생략...
}
```

미리 구현해 두었던 ExceptionAdvice에 해당 Exception을 handle하는 코드를 넣어 마무리합니다.

<br/>

## 결론 및 활용

실제 구현을 해보면 구현 당시에는 class, field 등에 대한 충분한 이해와 함께 JoinPoints를 어떻게 활용하는지에 대한 문제, 그리고 Exception 처리에 대한 부분까지 모두를 신경쓰게 되어 고려할 부분이 많아지고 개발양이 많은 것처럼 보이지만, 기능 API들이 많아지고 동일 처리를 반복적으로 이루어지는 경우에는 한번 만들어둔 AOP기능을 이용하여 annotation만 추가하면 되므로 개발이 편리해 집니다. 또한 기능상에 변경점이 있는 경우에는 Aspect만 수정하면 되어 관리도 편해집니다.

<br/>

## 참고자료 및 출처

- <https://engkimbs.tistory.com/746>
- <https://daddyprogrammer.org/post/11356/springboot2-forbidden-word-by-aop-annotation/>
- <https://jeong-pro.tistory.com/171>
- <https://stackoverflow.com/questions/48625149/spring-aop-works-without-enableaspectjautoproxy>