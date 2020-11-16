---
title: "Homebrew 를 이용한 패키지 관리"
categories: [study, springboot]
tag: [homebrew]
last_modified_at: 2020-11-16
---

맥(Mac)을 이용해서 개발을 하고 있다면, 여기서 소개하는 brew 가 많은 도움이 될 수 있습니다. 물론, 개발이 아닌 목적으로도 맥을 사용하는 사람이라면 마찬가지겠죠. (하지만 개발을 하고 있는 사람이 그 효과를 크게 느낄 것입니다) [^1]

[Homebrew](https://brew.sh/index_ko)는 Ruby 기반으로 만들어진 패키지 관리 프로그램 입니다. 더 상세한 내용이나 기술적인 내용을 다루기 보다는 간단한 사용법을 정리해 둠으로서 개인적인 정리 목적도 있고, 향후에 찾아보기 편하기 위함도 있습니다. 원본 출처는 아래 출처 항목에서 다시 다루었고, 모든 저작권은 해당 페이지에 있음을 밝힙니다. 저는 자주 사용하는 것 위주로, 어떤 것들이 있는지 위주로 해서 설명을 더해 놓겠습니다만, 모든 내용은 해당 출처에 자세하게 설명되어 있습니다. 

Homebrew는 'brew' 라는 명령어로 시작하여 설치 관련한 여러 작업들을 할 수 있게 합니다. 당연히 해당 프로그램이 설치될 것이고, shell 에 PATH 로서 해당 명령어가 매핑되어 있을 겁니다. 저는 개인적으로는, 동일한 프로그램을 여러 버전으로 사용하기 위해서 접하게 된 것도 있고, 화면을 통한 설치가 아니라는 점에서 매우 매력적으로 느꼈습니다. 

## 설치

다른 작업에 앞서서 우선 Homebrew 자체부터 설치를 진행해야 합니다. 한번 설치해 두면 그다음부터는 크게 변경할 사항이 없으니 최초 1회만 실행하게 된다고 생각하시면 됩니다. Homebrew 는 다음 명령어를 통해 설치할 수 있습니다. 터미널을 실행하거나 iterm2 등 터미널 명령어가 가능한 프로그램을 실행시키고, 아래 명령어를 입력합니다. 

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

사실 위 명령어를 실행하기 위해서는 xcode 라는 맥용 개발 툴의 특정 명령어가 필요합니다. 다음을 입력해서 xcode command line tools  라는 명령어를 설치합니다. [^2]

```sh
xcode-select --install
```

다시 위의 명령어를 입력하면 homebrew 설치가 완료됩니다. 아래 버전을 확인하는 명령어로 제대로 설치되었는지 확인합니다. (예시)

```sh
$ brew -v
Homebrew 2.5.8
Homebrew/homebrew-core (git revision 3394c; last commit 2020-11-09)
Homebrew/homebrew-cask (git revision 12d3f3; last commit 2020-11-09)
```

## Homebrew 사용하기

앞서 설명한 것 처럼 Homebrew 는 `brew` 라는 명령어로 실행합니다. 다음 몇 가지를 익히고 있으면 사용하는 데 어려움이 전혀 없습니다.

#### 검색하기

Homebrew 에서는 설치하는 프로그램들을 Formula 라고 부릅니다. 다음 예시에서 처럼 `brew search <formula 이름>` 으로 검색해서 어떤 것들을 설치할 수 있고, 어떤 버전을 설치할 수 있는지 확인할 수 있습니다. 

```sh
brew search java
```

```sh
brew search chrome
```

경험상 이 명령어(`search`)를 직접 사용하는 경우는 극히 드문 것 같습니다. 그저 필요한 프로그램에 대해서 brew를 지원하는지를 검색하다 보면 brew 명령어로 어떻게 설치해야 하는지가 나와 있습니다. 이 명령어를 그대로 옮겨 적는 것이 편리합니다.

위 예시에서 볼 수 있듯, 설치되는 프로그램 (formula) 이 화면이 있는 것인지 아니면 어떤 명령어 집합인지 등은 아무런 문제가 되지 않습니다. 그저 검색해서 원하는 formula 가 있다면 설치/실행할 수 있습니다. 심지어는 intelliJ, Eclipse 같은 화면이 있는 프로그램도 Homebrew 로 설치하고 관리하는 것이 가능한것으로 알고 있습니다.
{: .notice--info}

#### 설치하기

formula 의 설치는 `brew install <formula 이름>`으로 할 수 있습니다. 상세한 설치 과정에 대한 설명이 보여지고 만약 iterm 등과 같은 환경을 갖추어 두었다면 맥주잔 모양의 아이콘이 보여지면서 설치가 진행됩니다. 

```sh
brew install rbenv
```

만약 특정 명령어에 대한 formula 를 설치했다면, 자동으로 PATH에 추가해 줍니다. 따라서 다른 패키지 설치 과정보다 조금 더 편리하게 구성이 가능합니다.

#### 업그레이드(업데이트)하기

설치된 formula 를 업데이트 하기 위해서는 우선 `brew outdated` 를 입력하여 오래된(구형인) formula 를 검색합니다. 그리고 `brew upgrade <formula 이름>`  를 입력하여 업데이트를 진행합니다. 

```sh
# 버전업된 패키지 확인하기
brew outdated

# 패키지 업그레이드
brew upgrade rbenv

# 모든 패키지 업그레이드하기
brew upgrade
```

모든 패키지를 업데이트 하는 경우, 중간에 설치 과정에서 사용자 암호를 물어보는 경우가 있습니다. 현재, 암호가 틀리면 재차 질문하는 것이 아니라, 그대로 종료되는 것 같으니, 특히 한영전환이 되어 있지 않은지를 확인하고 나서 암호를 입력해 보시기 바랍니다. 다시 `brew upgrade` 를 실행하기는 조금 귀찮습니다.

#### 삭제하기

Homebrew 에서는 특정 버전으로 업그레이드 하더라도 이전 버전을 삭제하지는 않기 때문에 깔끔한 정리를 위해서는 삭제를 해줘야 합니다. 특정 formula 를 삭제하려면, `brew cleanup <formula 이름>` 를 입력합니다. 

```sh
brew cleanup rbenv
```

이렇게 입력하면 해당 formula 에 구형 버전들을 모두 정리하게 되고, 만약 해당 formula 자체를 삭제하고 싶다면 다음과 같이 `brew uninstall <formula 이름>`를 입력합니다. 

```sh
brew uninstall rbenv
```

#### 관리하기

만약 사용중인 formula 목록을 확인하고 싶다면 `brew list` 를, 특정 formula 의 상세 정보를 보고 싶다면 `brew info <formula 이름>` 를 입력합니다. 

```sh
# 설치한 패키지 목록보기
brew list

# 패키지의 정보 보기
brew info rbenv
```

## Homebrew 관리

계속해서 사용을 하다 보면 Homebrew 자체를 업데이트/삭제 해야 하는 경우도 있습니다. 

#### 업데이트 하기

```sh
brew update
```

즉 brew 가 설치한 formula(패키지, 프로그램)를 업데이트 하는 것은 `upgrade` sub-command 를 입력하면 되고, brew 자체를 업데이트 하는 것은 `update` 를 입력하면 됩니다.

#### 삭제하기

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall.sh)"
```

과거에는 설치/삭제에 대해서 ruby 관련 명령어가 있었는데, 지금 보니 해당 부분은 사라진 것 같습니다. 더 간편하게 설치가 가능한 것 같습니다.

## 참고

위에서 설명한 명령어들로 모든 작업들을 할 수 있지만, 보기 좋게 나열해 놓고 확인하고 싶은 경우에는 <mark style='background-color: #fff5b1'>Cakebrew</mark> 라는 것을 검색해서 설치해 보시기를 바랍니다. 터미널 만으로 관리하는 것이 크게 와닿지 않아 어려운 분들에게는 도움이 많이 될 것입니다. (저는 조금 사용하다가, 최근에는 사용하고 있지 않습니다.)

![](/assets/images/posts/study/springboot/2020-11-16-01020-install-homebrew/cakebrew.png)


## 참고자료 및 출처

- <https://brew.sh/index_ko>
- <https://tutorialpost.apptilus.com/code/posts/tools/homebrew-for-mac/>

[^1]: Homebrew 는 홈페이지에 명시되어 있는 것 처럼, Mac 용 패키지 관리자 입니다. 다른 환경에 설치를 해보지도 않았고, 설치 되더라도 정상적으로 구동되지 않을 수 있습니다.

[^2]: 최근 이 모듈 설치 없이도 구동이 가능해진 것 같습니다. 아니면 별도 설치 과정없이 background 에서 알아서 진행해주는 것 같습니다. 따라서 크게 신경쓸 부분이 아닙니다.