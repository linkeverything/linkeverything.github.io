---
title: "Spring (Boot)에서의 의존성 주입(Dependency Injection)에 대한 이야기"
categories: [SpringBoot]
tag: [dependency, injection, RequiredArgsConstructor]
last_modified_at: 2020-11-04
---
스프링에 대해서 이야기를 하다보면 의존성 주입(Dependency Injection, DI)에 대해 이야기를 할 수밖에 없습니다. 스프링의 특성 중 하나가 Singleton pattern을 가지고 있다는 점인데, 일반적인 Java 프로그래밍과는 다른 형태로 구현되었다고 볼 수 있습니다.

여기서는 DI 자체에 대한 설명은 하지 않겠지만, 어떤 생각으로 이 부분을 살펴봐야 하는지, 그리고 저는 어떻게 생각하고 코드를 완성해 가고 있는지를 공유해 보고자 합니다.

<br/>

## Singleton Pattern?

Spring Framework 의 특징을 설명할 때에 Singleton pattern을 이야기합니다. Singleton pattern 은 객체를 생성할 때 하나만 생성하게 해서 JVM의 메모리 사용을 최소화할 수 있고, 같은 처리를 하나의 객체에 처리하게 하여 동시성 처리나 동작의 편의성을 제공한다고 합니다(사전적 정의 아님). 

이렇게 Singleton pattern을 하는 데 있어서 Plain Java형태에서는 아래와 같이 작성합니다. 

```java
public class Test{

    public static Test INSTANCE;

    public Test getInstance(){
        if(INSTANCE == null){
            INSTANCE = new Test();
        }
        return INSTANCE;
    }
}
```

이렇게 Singleton pattern을 만들어 두고, 이를 가져다 쓰는 쪽에서는 아래와 같이 작성해서 가져다가 씁니다. 

```java
// 이렇게 객체를 생성하지 않고,
//Test test = new Test(); 

// 이렇게 Singleton 객체를 가져옵니다.
Test test = Test.getInstance(); 
```

이런 과정을 생략하고 조금 더 사용하기 편하게 만들어주는 것이 DI의 핵심인데, 아래와 같은 방법으로 가능합니다.

<br/>

## 의존성 주입의 종류

보통 아래 세 가지 정도의 방법으로 의존성 주입을 시도합니다.

<br/>

#### Constructor

생성자 주입 방식은, Singleton 객체를 사용하는 객체에서 `@Authwired` annotation을 넣어주면서 멤버로 선언되어 있는 Singleton 객체를 주입해 주는 것입니다. 아래 예제에서 생성자의 멤버로 들어온 두 개의 객체는 Spring Framework에서 Bean 객체로 생성된 것을 가져다가 주입해 주게 되고, 아래 클래스에서는 `new` 없이 생성되어 있는 객체를 매핑하여 사용할 수 있습니다. 

```java
public class ExampleCase {
    
    private final ChocolateService chocolateService;
    private final DrinkService drinkService;
    
    @Autowired
    public ExampleCase(ChocolateService chocolateService, DrinkService drinkService) {
        this.chocolateService = chocolateService;
        this.drinkService = drinkService;
    }
}
```

<br/>

#### Setter

setter 주입 방식은 생성자 주입 방식과 유사하게 `@Autowired` annotation을 사용합니다. 사용하려는 멤버의 setter 메소드를 생성하고 `@Autowired` annotation을 붙여줍니다. 그러면 인자로 필요한 해당 객체를 Bean 객체로 인지하고 해당 객체를 멤버에 set 해주는 것입니다.

```java
public class ExampleCase{
    
    	private ChocolateService chocolateService;
    	private DrinkService drinkService;
    
    	@Autowired
    	public void setChocolateService(ChocolateService  chocolateService){
    		this.chocolateService = chocolateService;
    	}
    
    	@Autowired
    	public void setDrinkService(DrinkService  drinkService){
    		this.drinkService = drinkService;
    	}
    
    }
```

<br/>

#### Field

Field 주입 방식은 멤버로 선언된 객체 앞에 `@Autowired` annotation을 붙여 주는 것으로 해결합니다. 어쩌면 가장 최근까지도 많이 사용하는 injection방식이라고 할 수 있습니다. 최근까지도 Controller에서 Service를, Service에서 Dao(JpaRepository)를, 이렇게 Field 주입 방식을 이용해서 구현했었습니다.

```java
public  class  ExampleCase{
    
    @Autowired
    private ChocolateService  chocolateService;

    @Autowired
    private DrinkService  drinkService;
}
```

<br/>

## Field Injection 의 문제점

앞서 이야기한 것 처럼 Field 주입 방식을 가장 많이 사용한 것 같습니다. 다른 것 보다도 아무래도 기존 Java 방식과 유사함도 있고, 깊은 이해 수준 없이도 사용하기 편리해서 그랬던 것 같습니다. 

특시 MVC 패턴을 사용하게 되면서 과거 Service, ServiceImpl, Dao, DaoImpl 을 구현해 사용하던 시절에는 더더욱 이 방식을 선호했던 것 같고, Controller에서는 요청을 보내는 Service를 Field Injection으로, Service에서는 Dao를 Field injection으로 넣어주었었습니다. 기능이 고도화 될 수록 `@Autowired`를 붙여주는 Field가 많아지는 것 뿐, 크게 문제점은 없어 보였는데, 최근에는 `@RequiredArgsConstructor` 를 사용하는 것이 많이 보여서 찾아보니, 아래와 같은 문제점을 정리해놓은 글들이 있었습니다. 

<br/>

#### 불변성을 허용하지 않음
Disallows immutable field declaration

final 선언을 통해서 불변성을 확보할 수 없습니다. 이와는 반대로 생성자 주입 방식을 사용하면 final을 선언할 수 있습니다.

<br/>

#### 단일 책임의 정책을 위반하기 쉬움
Eases single responsibility principle violation

필드 주입 방식은 의존성을 주입하는 것이 간단합니다. `@Autowired` annotation 아래에 선언하는 만큼 주입이 가능합니다. 생성자 주입 방식을 이용하는 경우 의존성 주입할 대상이 많아지게 되면 생성자 파라미터에 

<br/>

#### DI 컨테이너의 결합성이 높음
Tightly coupled with dependency injection container

의존성 주입을 하는 프레임워크의 핵심 아이디어는 관리되는 클래스가 DI 컨테이너에 의존성이 없어야 한다는 내용입니다. 즉, 필요한 의존성을 전달하면 독립적으로 인스턴스화 할 수 있는 단순 POJO여야 한다는 것입니다. DI 컨테이너 없이도 유닛테스트에서 인스턴스화 시킬 수 있고, 각각 나누어서 테스트도 할 수 있어야 한다는 것 입니다. 컨테이너의 결합성이 없다면 관리하거나 관리하지 않는 클래스를 사용할 수 있고, 심지어 다른 DI 컨테이너로 전환할 수 있습니다. 하지만, Field Injection을 사용하면 필요한 의존성을 가진 클래스를 곧바로 인스턴스화 시킬 수 없기 때문에 테스트 등의 상황에서 불편함이 있습니다.

<br/>

#### 의존성이 가려짐
Hidden dependencies

의존성 주입을 통해서 개발을 하는 경우에 특정 클래스가 생성되어 사용된다는 것은 그 안에 주입된 의존성들에 대한 부분도 책임을 가지고 동작해야 한다는 점도 포함합니다. 그런데 Field 의존성을 사용하면 이러한 부분들이 가려져 있기 때문에 의존성에 대한 완벽한 파악이 힘들게 됩니다.

<br/>

## Setter Injection 과 Constructor Injection 

<br/>

#### Setter Injection

선택적인 의존성을 선언할 때에 유용한 방식입니다. 과거 Spring Framework 3 버전 정도까지 추천되던 방식으로, 상황에 따라 의존성 선언이 가능하다고 이야기한다.

<br/>

#### Constructor Injection

필수 의존성 주입에 유용합니다. 또한 final 선언을 통해 불변성을 유지시킬 수 있습니다(위 설명). 상대적으로 순환 의존성[^1]을 확인하기 쉽기 때문에 이런 문제를 방지할 수 있습니다. Spring Framework 4 부터 권장된 방식이고, 하나의 인자만을 포함하는 경우에 한해서 `@Autowired` annotation의 생략도 가능합니다(권하지는 않습니다).

<br/>

## @RequiredArgsConstructor 

이런저런 이유로 생성자 주입 방식(Constructor Injection)이 더 좋은 방식으로 추천되고 있다는 것을 알았습니다(적어도 현재까지는). 그런데 생성자 주입 방식을 사용하려면 멤버로 사용되는, 즉 의존성 주입을 하려는 대상이 늘어날 때 마다 매번 생성자에 인자를 추가하고 인자를 매핑해주는 것으로 변경개발 해야 합니다. 인자가 적을 때에는 쉽게 느낄 수 있지만, 반복될 수록 이는 복잡한 과정이 아닐 수 없습니다.

lombok 라이브러리를 사용하면 이 부분을 조금 더 쉽게 처리할 수 있습니다. 생성자를 빈번하게 변경할 필요도 없고 코드 가독성도 많이 높아집니다.

<br/>

#### 기존 방식

우선 원래 Field 주입 방식을 사용하는 경우에 어떻게 사용했었는지를 살펴보겠습니다.

```java
@Service
    public class BannerServiceImpl implements BannerService {
    
        @Autowired
        private BannerRepository bannerRepository;
    
        @Autowired
        private CommonFileUtils commonFileUtils;
```

<br/>

#### 변경한 방법

위 예와 같은 형태의 Class를 생성자 주입 방식으로 변경하면 다음과 같은 형태가 됩니다.

```java
    @Service
    @RequiredArgsConstructor
    public class BannerServiceImpl implements BannerService {
    
        private final BannerRepository bannerRepository;
    
        private final CommonFileUtils commonFileUtils;
```

앞서 설명한 것 처럼 멤버 객체들을 `final` 로 선언합니다. 불변성을 가질 수 있습니다. 그리고 class 선언부 앞에 `@RequiredArgsConstructor`를 넣어줍니다. API 문서를 보면 final로 선언된 멤버이면서 `@NotNull`등의 제한자를 넣어준 것이 대상이라고 합니다.

만약 lombok을 사용하지 않고 생성자 주입 방식을 원래대로 사용하면 다음과 같습니다.

```java
    @Service
    public class BannerServiceImpl implements BannerService {
    
        private BannerRepository bannerRepository;
    
        private CommonFileUtils commonFileUtils;
    
        @Autowired
        public BannerServiceImpl(BannerRepository bannerRepository, CommonFileUtils commonFileUtils) {
            this.bannerRepository = bannerRepository;
            this.commonFileUtils = commonFileUtils;
        }
```

<br/>

## 참고자료 및 출처

- <https://velog.io/@aidenshin/필드주입-생성자-주입방식으로-변경>
- <https://zorba91.tistory.com/238>
- <https://blog.marcnuri.com/field-injection-is-not-recommended/>

[^1]: Constructor Injection에서 순환 의존성을 가질 경우 `BeanCurrentlyCreationExeption`을 발생시킴으로써 순환 의존성을 알 수 있습니다. 
    First Class가 Second Class를 참조하는데 Second Class가 다시 First Class를 참조할 경우 혹은 First Class가 Second Class를 참조하고, Second Class가 Third Class를 참조하고 Third Class가 First Class를 참조하는 경우 이를 순환 의존성이라고 부릅니다. (혹은 순환 참조)