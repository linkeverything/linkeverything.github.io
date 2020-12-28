---
title: "Gradle 프로젝트에 라이브러리 추가하기"
categories: [study, springboot]
tag: [add library]
last_modified_at: 2020-12-28
---
프로그래밍을 하는 데 있어서 **라이브러리** 라는 용어를 자주 사용하게 됩니다. 라이브러리는 "도서관" 이라는 의미겠지만, coding 에서 의미는 조금 상이합니다. 이번 내용은 Spring boot에 국한되는 내용은 아니고 최근 java프로젝트의 추세대로 설명을 하였습니다.

[https://github.com/linkeverything/studySpringBoot/tree/0020-add-library](https://github.com/linkeverything/studySpringBoot/tree/0020-add-library){:target="_blank" .btn .btn--primary}

> 위 경로에서 다운로드 받아서 진행하는 경우, branch 를 반드시 확인하시고 다운로드(checkout) 받으시기 바랍니다. 각 게시글에 맞는 branch 로 생성되어 있으므로 해당 branch를 받는 것이 이해해 도움이 됩니다.

이번 포스트의 내용은 위 github repository 에 추가한 내용이 없습니다. 개념 설명 위주이니 그냥 내용 자체만 보아도 무방합니다.
{: .notice--warning}

## 라이브러리?

라이브러리란, <mark style='background-color: #dcffe4'>특정 프로그램의 확장된 기능을 사용하기 위해서, 개발 편의성을 향상시켜주거나 공개하고 싶지 않은 모듈의 캠슐화를 위해서 사용하는, 몇몇 객체이거나 함수들의 집합을 의미</mark>합니다. 개발 편의성을 위해서 사용하는 경우도 많고, 라이브러리 없이는 새롭게 모두 개발해야 한다는 점에 있어서는 개발 생산성을 향상시켜줍니다. 하지만 라이브러리 자체에 존재하는 오류나 버그(bug)요소 때문에 프로그램에 부정적인 영향을 줄 수도 있기 때문에 특정한 라이브러리를 도입하기 위해서는 충분히 확인 및 검증 절차를 거쳐서 사용해야 합니다.

라이브러리는 일반적으로 자주 사용되는 몇몇의 라이브러리들이 있고, 개인 개발자들이 이를 확장해 놓은 또 다른 라이브러리들도 존재합니다. 또한 facebook, twitter 등과 같은 open API 를 제공하는 업체에서도 java등의 프로그램과 연동하기 위해서 라이브러리를 제공하는 경우도 있습니다.

오래 전, Java 언어로 된 프로그래밍을 하는 데 있어서, 특정 라이브러리를 사용하려면 `lib` 혹은 `libs` 폴더를 만들고, 이를 class path / build path에 추가해 주고 사용해 왔습니다. 이는 특정한 IDE에 국한되는 방식이기도 할 뿐 아니라, 개발자들이 모두 동일한 환경을 구축해야만 동일한 결과를 얻을 수 있다는 점에서 한계점이 있었습니다. 또한 프로그램 자체를 공유해야 하는 경우에 이 라이브러리들까지 모두 다 압축해서 전달해야 하는 등으로 불편함도 있었습니다.

이러한 불편함을 해소하기 위해서 도입된 방식이 Maven 그리고 Gradle 이라고 볼 수 있습니다. 두 빌드 방식간에 차이점이나 우열관계는 없지만, 개발하려는 프로그램의 형태에 따라서 특정 방식을 선호하는 경우도 있으니 참고하시기 바랍니다. 

최근에 제 경험상으로는 android 계열은 gradle빌드를 선호하고 android studio 의 기본값도 gradle 빌드인 것으로 알고 있습니다. Spring Boot 계열은 gradle이 우세한 것 같지만, spring 쪽에서는 비등비등하게 사용되는 것 같습니다.

## 기본사항

라이브러리 사용에 있어서의 기본 사항에 대해서 조금 설명하겠습니다. 시간이 지나서 조금 사용하다 보면 자연스럽게 얻어지는 정보들이지만 미리 알고 있으면 조금 더 편리할 수 있습니다.

#### 어디서 찾지

보통의 경우에 라이브러리를 검색하는 것이 선행되지는 않을 겁니다. 특정 기능 등을 검색하고, 그 기능을 구현하는 방법을 찾다 보니, 이미 유사한 기능을 구현해놓은 라이브러리를 찾게 될 것입니다. 그렇다면 그 라이브러리 자체에 대한 이름을 알게 되었을 분, 막상 라이브러리를 도입하려면 난감한 상황이 됩니다. 

저는 이렇게 알게 된 라이브러리에 대해서 [MVN Repository](https://mvnrepository.com/)라는 곳에서 찾습니다. 이 곳이 실제 Maven / Grdle이 다운로드 받는 경로가 될 것입니다(mvncentral). 이 페이지에 접속하면 다음과 같은 화면이 나타납니다.

![](/assets/images/posts/study/springboot/2020-12-28-add-library/capture 2020-12-28 PM 2.45.57.png)

여기서 위 검색창에 찾고자 하는 라이브러리를 검색합니다. 저는 예시로 'apache common' 을 검색하였습니다. 

![](/assets/images/posts/study/springboot/2020-12-28-add-library/capture 2020-12-28 PM 2.47.46.png)

그리고 아래 검색된 결과 중에서 'Apache Commons IO' 를 선택합니다. 그리고 나면 아래처럼 버전이 쭉 나오게 됩니다. 본인이 사용하고자 하는 특정 버전이 있다면 그 버전을 선택하고 최신 버전을 사용한다면 가장 위에 있는 버전을 눌러줍니다.

![](/assets/images/posts/study/springboot/2020-12-28-add-library/capture 2020-12-28 PM 2.49.48.png)

이제 아래처럼 Maven 인지, Gradle인지를 구분하여 어떻게 문구를 넣어줘야 하는지를 판단합니다.

![](/assets/images/posts/study/springboot/2020-12-28-add-library/capture 2020-12-28 PM 2.50.02.png)

![](/assets/images/posts/study/springboot/2020-12-28-add-library/capture 2020-12-28 PM 2.50.06.png)

이제 본인의 소스 코드를 돌아와서 build.gradle에 해당 내용을 추가해 줍니다. 추가하고나서는 Load Gradle Changes(⇧⌘I)를 해주어야 프로젝트 코드 내에서 사용이 가능합니다.

```gradle
...
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compile group: 'commons-io', name: 'commons-io', version: '2.8.0'

	implementation 'org.springframework.boot:spring-boot-starter'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
...
```

#### 설치 위치



## 사용법

라이브러리를 사용하는 방법에 대해서 조금 더 깊이있게 살펴보도록 하겠습니다. 가장 많이 사용되는 build 방식이 gradle과 maven이므로 이 두 가지에 대해서 기술합니다.

#### Gradle

#### Maven

## Maven 과 grdle 전환

## 참고자료 및 출처

- N/A

[^1]: 