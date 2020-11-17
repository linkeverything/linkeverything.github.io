---
title: "Spring Boot 개발을 위한 IDE 설치 가이드"
categories: [study, springboot]
tag: [intellij, eclipse, ide]
last_modified_at: 2020-11-17
---
이제 IDE를 설치해 볼 차례입니다. IDE는 Integrated Development Environment 의 약자입니다. 통합 개발 환경 이라고 부르는데, 그냥 개발 툴이라고 쉽게 생각해도 무방합니다. 

지금까지 수많은 IDE들이 출시되었고, 출시될 준비를 하고 있을겁니다. 그 중에서도 살아남는 것들이 존재하고 그렇지 못하고 없어지는 것들도 존재합니다. 개발 툴 이므로 로컬에 설치해서 개발 과정의 편의성(생산성이라는 단어는 싫군요!)을 높여준다고 생각하면 편하긴 하지만, 이러한 수식어 중에 '로컬' 이라는 단어는 이제 조금씩 밀려날지도 모르겠습니다. 

IDE는 물론 속도나 기타 여러 측면 때문에라도 로컬에 설치하는 것이 맞긴 합니다만, 최근 <mark style='background-color: #dcffe4'>Cloud IDE</mark>라고 하여 온라인에서 개발하고 빌드하고 배포하여 테스트하는 도구들도 더러 출시되어 있습니다. 이러한 것들의 성능에 대해서 의문을 제기하는 목소리가 많고, 저 역시 아직은 회의적이긴 합니다만, 만약 현재 불편한 점들이 해소된다면, 개발 장비에 고가의 비용을 지불하는 것이 조금 줄어들면서 그야말로 개발 편의성을 높여줄 것 같다는 기대는 하고 있습니다. (그게 제가 은퇴하기 전일지는 모르겠습니다만..)

각설하고 여기서는 여러 개발환경, 즉 IDE 중에서 Eclipse 와 IntelliJ에 대해서 설치 방법을 간단히 살펴보고, 툴을 조금 비교해 보겠습니다. 더불어 VSCode에 대해서도 조금 살펴보고 글을 마무리해 보려고 합니다. 

미리 말씀드리지만, 저는 Eclipse 와 IntelliJ를 모두 사용하고 있고, 지금 이 글을 적는 툴은 VSCode입니다. 이유는 아래에서 상세하게 설명하겠습니다.

본 글의 내용은 지극히 주관적임을 밝힙니다.
{: .notice--warning}

## Ecilpse

![](/assets/images/posts/study/springboot/2020-11-17-install-ide/EF_GRY-OR_png_cropped.png)

첫 번째 소개할 IDE는 '장유유서' 원칙에 따라(?) Eclipse 입니다. Ecilpse는 '일식(달이 해를 가림)'이라는 뜻인데, 그 의미에 맞게 아이콘도 일식과 관련한 아이콘 입니다. 

Eclipse 는 Java 로 구동되는, AWT, SWING 등과 같은 UI 도구들로 만들어진 개발 도구입니다. 엄청나게 오래된 역사를 가지고 있고, 현재는 버전을 2020-09 처럼 매 분기마다 새로운 버전을 빌드하는 정책을 가져가고 있습니다만, 과거에는 4.X 버전까지 출시하면서 꽤 오랫동안 개발자들에게 사랑받아온 툴입니다. 

#### OSGi

![](/assets/images/posts/study/springboot/2020-11-17-install-ide/OSGi-website-header-logo.png)

OSGi 방식을 도입했다고 하는데, 쉽게 설명하면 jar 파일로 압축된 플러그인을 특정 폴더에 넣어 두면, Eclipse 가 실행되면서 그 jar의 내용을 읽어 특정 기능을 하는 plug-in으로 활용해 준다는 것입니다. 현재는 워낙 다양한 툴들이 이러한 방식을 사용하고 있고, 플러그인이라는 것이 활성화 되어 있지만, eclipse 가 한창 최고의 개발도구로 자리매김할 당시에는 이러한 방식이 꽤나 획기적이었고, 일반 개발자들이 이러한 플러그인을 자유롭게 개발할 수 있다는 점만 하더라도 매력있는 도구였습니다. 

#### 오래된 만큼 쌓인 노하우

워낙 오래되어서 그런지 현재는 많은 부분에서 다듬어져 있고, 큰 버그 없이 잘 구동되긴 하는 것 같습니다. 다만, 역사가 오래되어 어쩔 수 없이 코드가 길어진 탓인지, 아니면 <mark style='background-color: #ffdce0'>무료</mark>정책을 가져가고 있는 툴의 한계인지는 모르겠으나, 여전히 조금 무거운 감이 있습니다. 그래도 여전히 좋은 툴임엔 틀림없습니다.

#### 뜻밖에 찾아온 위기

원래 Eclipse 는 Java, C, C++, 등 많은 언어를 지원하는 개발도구였고, 모바일, 즉 스마트폰 환경이 활성화되는 시기에는 Android 개발 툴(ADT)이 Eclipse의 플러그인 형태로 들어가 있었습니다. 저 역시 Android 개발 당시 Eclipse 를 사용했었으니까요. 그런데 2016년 초, 갑자기 Eclipse 내의 Android 개발 도구에 대한 지원을 중단한다는 발표가 있었고, 이와 더불어 아래 설명할 IntelliJ가 신흥 강자로 떠오르게 됩니다.

그 이후에도 Spring 등은 여전히 eclipse 와의 유대 관계를 가져가고 있지만, Android 는 다시는 돌아오지 않는 형태가 되었습니다.

#### 설치 방법

1. 홈페이지에 접속하여 다운로드를 받습니다. 
   
   installer 형태의 다운로드를 받지 않으시길 권장합니다. [패키지 다운로드 링크](https://www.eclipse.org/downloads/packages/)로 이동하여 온라인 다운로드 툴이 아닌, 완전체 버전을 받으시기 바랍니다. 

2. Eclipse 는 별도의 설치 과정이 없습니다. 

   Windows용은 압축을 해제하면 되고, MacOS 는 .app 패키지를 실행하면 그만입니다. 다만 더 다양한 기능을 활용하고자 하는 경우에는 설치 이후에도 여러가지 Plug-in을 다운로드 받고 설정을 잡아줘야 합니다만, 이는 어떤 IDE도 공통입니다.

## IntelliJ IDEA

![](/assets/images/posts/study/springboot/2020-11-17-install-ide/IntelliJ-Idea-logo1.png)

IntelliJ IDEA는 JetBrains 라는 회사에서 만든 프로그램입니다. 개발 도구들 중에서 몇 안되는 유료 툴 중 하나이지만, 전무 유료는 아니고 특정 기능을 활용하기 위해서는 유료 버전을 구매해야 합니다.

#### 혜성같이 나타난 IDE

Spring, Java 1.8, Android Studio.. 아마도 이러한 시기였던 것 같습니다. 갑자기 개발자들 사이에서 IntelliJ IDEA를 사용하는 사람들이 많아지기 시작했고, 처음 시작은 이뻐서, 혹은 깔끔하고 속도가 빨라서 였습니다. 

제 개인적으로는 Android Studio가 아얘 IntelliJ IDEA기반으로 빌드되어 출시되기 시작하면서부터 알게 되었고, 그 이후 Robot용 SDK 개발과제를 거치면서 Plugin을 개발하고 더 자주 사용하게 된 것 같습니다. 

#### Eclipse 와 차이점

그 전까지 많이 사용하던 Eclipse와 차이점, 우선은 깔끔한 UI와 빠른 실행 속도라고 볼 수 있습니다. 개발 툴 중에서도 UI가 깔끔하고 예쁜 편에 속하고 사용 중에 버벅인다거나 하는 점 없이 부드러운 구동력을 보여줍니다. 또한 Plug-in의 설치 과정이 조금 간편하고, 파일 검색 등 탐색 과정이 간편하다는 장점도 있습니다. 

또 다른 차이점은 개발 환경에 대한 View가 다릅니다. Eclipse의 경우, Workspace를 두고 이를 좌측 Explorer에 모두 띄워놓고 여러 프로젝트를 관리하는 느낌이라면, IntelliJ는 그러한 개념이 아니라 Module 단위의 개발을 하기 때문에 1개의 프로젝트가 하나의 IntelliJ IDEA Window 를 갖습니다. IntelliJ가 예쁘고 쓰기 편하다고 해서 무턱대호 eclipse에 있던 프로젝트를 import하면 낭패를 보는 까닭이 이것입니다. 

기존 eclipse에서 사용하던 프로젝트를 import하여 사용하려면 조금의 변형 과정이 필요합니다. 그래야 원하는 수준의 관리가 가능합니다.
{: notice--info}

#### 아쉬움

비용

## VSCode

![](/assets/images/posts/study/springboot/2020-11-17-install-ide/ikysur95osy0deokuuji.png)

#### 강력한 도구

#### 개발에는 어떨지

#### Jekyll 라이브러리에 최적


## 참고자료 및 출처

- JetBrains 홈페이지 : <https://www.jetbrains.com/>
- IntelliJ 다운로드 페이지 : <https://www.jetbrains.com/idea/download>
  (자동으로 OS에 맞는 버전으로 redirect됩니다)
- IntelliJ IDEA 공식 문서 : <https://www.jetbrains.com/help/idea/discover-intellij-idea.html>


- Eclipse 홈페이지 : <https://www.eclipse.org/>
- Eclipse 패키지 다운로드 : <https://www.eclipse.org/downloads/packages/>

- VSCode : <https://code.visualstudio.com/>