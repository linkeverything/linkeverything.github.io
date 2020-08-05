---
title: "Homebrew 를 이용한 Java 설치 및 관리"
excerpt: "How to set up Java development environment with Homebrew"
categories:
  - "dev"
  - "infra"
tag:
  - "homebrew"
  - "java"
  - "jenv"
last_modified_at: 2020-07-28
---

> Mac 시스템을 사용하면서 Homebrew 의 이용은 거의 필수적입니다. 없다고 문제될 것은 없지만, 사용성 측면에서 확싫히 이점이 있으므로, Homebrew 를 꼭 이용해 보시기 바랍니다. 여기서는 Java, 특히 JDK 를 설치하고 관리하는 방법에 대해서 설명해 보려고 합니다. 

Homebrew 는 설치되어 있다는 전제하에 글을 적겠습니다. Homebrew의 설치 방법에 대해서는 따로 글을 적겠지만, 홈페이지에도 너무 쉽게 설명이 되어 있으므로, 그대로 따라하셔도 됩니다. 
{: .notice--info}

<br/>

## java(jdk) 설치하기

<br/>

#### Homebrew 를 이용한 openjdk 설치

일반적으로 homebrew 를 이용해서 프로그램을 설치하게 되면 **brew install {프로그램 명}** 과 같은 형태로 입력합니다. 그런데, openjdk 는 이런 명령어로 설치가 되지 않습니다. 3rd party 같이 보이는 다른 경로를 이용해서 설치해야 합니다. 

```sh
$ brew tap AdoptOpenJDK/openjdk
$ brew cask install <version>
```

위와 같은 형태로 AdoptOpenJDK 를 이용해서 설치해야 합니다. 

```sh
$ brew tap AdoptOpenJDK/openjdk
$ brew cask install adoptopenjdk8
```

저는 위와 같이 입력해서 1.8 버전을 설치했고, 그 이후에도 11, 14버전도 설치하였습니다. 

<br/>

#### oracle java 설치

oracle java 설치는 [공식 홈페이지](https://www.oracle.com/kr/java/technologies/oracle-java-archive-downloads.html)에서 jdk 버전을 검색하여 다운로드, 설치하시면 됩니다. oracle 공식 java(jdk) 는 별도로 설치하는 방법도 없을 뿐더러, 있다고 하더라도 권장하지 않습니다. 

이렇게 설치된 jdk 는 아래 경로에 위치하게 됩니다. 

```
/Library/Java/JavaVirtualMachines/{jdk 버전 명}
```

<br/>

## 여러 버전의 java 설치 / 관리하기

개발 업무를 하고 있어 다양한 버전의 jdk 를 이용해야 하는 경우에는 매번 다른 버전의 jdk 를 설치하는 것은 아주 비효율적입니다. 특히나 특정 버전들은 uninstall 과정이 매우 복잡하기도 하고, linux 시스템 상 link 등이 깨지는 문제가 발생할 수 있기 때문에 권장하지 않는 방법입니다. 

Windows 시스템의 경우에는 시스템 설정의 환경변수 쪽에서만 변경해 주거나, java 설정 프로그램(공식)을 이용해서 현재 활성화된 java 버전을 관리할 수 있습니다만 Mac 시스템은 그게 쉽지 않아, `jenv` 라는 프로그램을 통해 설정해 보고자 합니다. 

<br/>

#### jenv 설치

jenv 역시 homebrew를 이용해서 설치합니다. 

```sh
$ brew install jenv
```

설치가 완료되고 나면, 다음 문구를 `.bash_profile` 이나 `.zshrc` 파일에 추가해 줍니다. 저는 `zsh` 을 기본 shell 로 사용하고 있기 때문에 **.zshrc** 파일에 추가해 주었습니다. 

```sh
if which jenv > /dev/null; then eval "$(jenv init -)"; fi
```

추가해 준 뒤에는 source 명령어로 다시 load 해 주거나, 터미널을 재실행합니다. 

<br/>

#### jenv 에 버전 관리하기

이제 jenv 에 앞서 설치한 jdk 버전들을 추가해 줍니다. **add** 옵션을 이용합니다.

```sh
jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home/
```

경로 중 **jdk1.8.0_162.jdk** 부분은 설치한 버전 별로 상이합니다. 
{: .notice--warning}

경로 중 **jdk1.8.0_162.jdk** 부분에 대해서는 adoptOpenJdk 쪽에 대해서도 변경해 주어야 합니다. 
{: .notice--warning}

다 추가해 주었으면 다음 명령어를 통해 다 등록되었는지 살펴봅니다. **versions** 를 이용합니다.

```sh
jonghunpark@Jonghun-mbp13  ~  jenv versions
  system
* 1.8 (set by /Users/jonghunpark/.jenv/version)
  1.8.0.251
  1.8.0.252
  11
  11.0
  11.0.7
  11.0.8
  14
  14.0
  14.0.1
  14.0.2
  openjdk64-1.8.0.252
  openjdk64-11.0.7
  openjdk64-14.0.1
  oracle64-1.8.0.251
  oracle64-11.0.8
  oracle64-14.0.2
```

저같은 경우에는 openjdk 를 8, 11, 14 설치하여 등록하였고, oracle jdk 역시 동일 버전들을 설치하고 등록하였습니다. 

이제 주로 사용할 jdk 를 등록해주어야 합니다. **global** 옵션을 이용합니다. 

```sh
$ jenv global oracle64-1.8.0.162
```

만약 특정 적업 디렉토리에 대해서는 특정 버전을 사용하고 싶다면 해당 디렉토리로 이동한 뒤, 다음과 같이 입력합니다. **local** 옵션을 이용합니다.

```sh
$ jenv local oracle64-10.0.2
```

이제 버전을 바꾸어 가며 사용하고 싶다면 **global** 옵션을 이용해서 설정하고 프로그램을 실행하면 됩니다. `java -version` 으로 확인해 보시기 바랍니다.

<br/>


## 참고자료 및 출처

https://findstar.pe.kr/2019/01/20/install-openjdk-by-homebrew/
https://jojoldu.tistory.com/329
