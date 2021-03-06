---
title: "Java 설치에 대한 이야기"
categories: [study, springboot]
tag: [install, java]
last_modified_at: 2020-11-17
---

Java 는 사실 다른 어떤 작업보다도 우선시 회어야 하는 작업입니다. Spirng Boot 자체가 Java 로 된 프레임워크이고 따라서 Java가 없이는 전혀 구동할 수 없습니다. 또한 IDE로 사용하는 각종 도구들, Ecilpse / IntelliJ 와 같은 것들은 모두 Java를 이용하여 구동되는 IDE입니다. 물론 vscode라는 프로그램을 사용한다면 Java없이 구동할 수 있을지는 모르겠지만, Java기반의 프레임워크를 개발하는데 아무런 의미가 없습니다. 

Java는 아주 기본적이고 간단한 방법을 이용하여 설치 할 수 있습니다. Windows에 하나의 프로그램으로서 설치하거나 MacOS에 명령어와 같은 형태로 설치가 가능합니다. 그러나 여기서는 더욱 깊숙하게 가면 너무 이야기가 길어지고, 요점에서 멀어지기 때문에 다음과 같이 간단히 적는 것을 기본으로 하겠습니다. 

Java는 개발 언어의 한 종류이고, 따라서 설치해도 별도의 화면 등은 제공하지 않습니다. 다만 Java의 JVM에서 특정 프로그램을 구동하게 되고 이 것이 우리가 원하는 Spring Boot 의 형태가 될 것이므로 반드시 설치합니다

사실 설치 자체는 크게 어렵지 않고 다양한 방법으로 설치가 가능합니다. 

- Oracle Java JDK 다운로드 및 설치
- Open JDK 다운로드 및 설치
- zip 파일을 이용한 설치
- Homebrew 이용하기(MacOS 전용)

여기서 잠시 짚고 넘어가야 할 사항이 있어 아래와 같이 정리합니다. 

#### JRE (Java Runtme Environment)

흔히 <mark style='background-color: #dcffe4'>Java 실행 환경</mark>이라고 부르는 것이 JRE 입니다. Java 프로그램을 만든 뒤, 이를 실행하고자 하는 경우, 흔히 'JVM 위에서 동작한다.'라고 이야기하며, 여기서 이야기하는 JVM은 Java Virtual Machine 입니다. Java 실행 환경에는 Java 로 된 프로그램을 '빌드(Build)' 하는 도구는 포함되어 있지 않기 때문에 프로그램을 수정할 수 없습니다. 또한 Eclipse 나 IntelliJ 와 같은 IDE 툴이 정상적으로 구동되지 않습니다.

#### JDK (Java Development Kit)

일반적으로 <mark style='background-color: #fff5b1'>Java 개발 도구</mark>라고 부르는 것입니다. JDK는 Java로 개발을 하고 이를 '빌드(Build)' 하는 도구들이 포함되어 있습니다. 또한 각종 툴들이 포함되어 있고, 위에서 설명한 JRE 역시 포함하고 있습니다. 따라서 JDK를 설치하면 JRE가 기본적으로 포함되어 있어, Java로 된 프로그램들을 실행할 수 있습니다. 

Java로 개발을 하는 데 있어 `JAVA_HOME` 이르는 이야기를 많이 하게 되는데, 개발에 있어서 `JAVA_HOME` 은 다음과 같은 형태로 되어 있습니다. 

- (Windows) C:\Program Files\Java\jdk1.8.0_131
- (MacOS) /Library/Java/JavaVirtualMachines/oracleJdk-1.8/Contents/Home

이렇게 지정해 주는 곳이 `JAVA_HOME` 이고, 이 아래에 각종 필요한 실행 프로그램들이 위치하고 있습니다.

## Oracke Java JDK 다운로드 및 설치

#### JDK 다운로드

[Oracle 홈페이지](https://www.oracle.com/java/technologies/)에 들어가면 Oracle 에서 제공하는 많은 Java관련 프로그램들이 있습니다. 이 중에서 Java SE 하위의 특정 버전을 선택하여 다운로드를 받습니다. OS에 따라서 실행 가능한 형태, 혹은 이미지 형태로 저장됩니다. 

- (Windows) jdk........exe
- (MacOS) jdk.........dmg

두 OS에서 모두 root권한을 가진 사용자를 이용하는 것이 편리합니다. 위의 예시처럼 root path(/) 하위에 설치되기 때문입니다. 

#### 설치하기

Windows 는 exe를 실행하고, MacOS 는 dmg 를 마운트하고 그 안에 패키지 설치 파일(*.pkg)을 실행합니다. 일반적인 프로그램 설치와 동일하게 다음, 다음, .. 으로 진행하여 쉽게 설치할 수 있습니다. 

## Open JDK 다운로드 및 설치

Open JDK 는 [홈페이지](https://openjdk.java.net/) 에서 다운로드 받아서 설치하면 됩니다. open JDK는 Oracle JDK와 유사한 형태로 설치가 진행되고, 유사하게 사용 가능하다는 장점이 있습니다. 

## zip 파일을 이용한 설치

조금 고급 사용자에게 권장되는 방법입니다. 만약 Infra등에 대해서 크게 관심이 없다면 이 부분은 고려하지 않아도 됩니다.
{: .notice--warning}

oracle은 해당 홈페이지에 exe, dmg 등과 함께 zip / tar.gz 파일도 제공하고 있습니다. 이 파일을 다운로드 받아서 서버에 업로드하고 압축을 풀면 그대로 사용할 수 있습니다. 따라서 Installer 가 구동되기 힘든 환경이라거나, Linux 서버 등에 실제 운영환경을 구축하는 경우에는 많이 사용되는 방법입니다.

## Homebrew 이용하기(MacOS 전용)

Homebrew 를 사용하는 방법은 다음 두 가이드를 보고 따라하면 됩니다. 앞서 설명한 방법만 가지고도 사용하는 데 아무런 지장이 없고, 제대로 알지 못한다면 차라리 위 방법을 추천합니다만, 경우에 따라서 Java 버전을 자주 변경해야 한다거나, 다운로드 후 인스톨.. 등과 같은 귀찮은 작업을 지양한다면 권할 만 한 작업입니다.

- [Homebrew 를 이용한 패키지 관리](https://linkeverything.github.io/study/springboot/install-homebrew/)
- [Homebrew 를 이용한 Java 설치 및 관리](https://linkeverything.github.io/mac/install-java-with-brew/)

## 환경 변수 설정 (선택사항)

Windows 나 MacOS 나 구분할 것 없이 환경 변수를 설정하는 것이 편리할 수 있습니다. [^1] 

#### Windows 환경 변수

Windows 의 경우, `제어판 > 고급 시스템 설정 > 환경 변수` 항목에서 추가하고 삭제하고 우선순위를 부여할 수 있습니다. [^2] 해당 위치에서 <mark style='background-color: #ffdce0'>Path</mark>를 찾고, 해당 내용을 추가합니다. 주의해야 할 것은 jdk-XXXX 경로를 Path에 추가해야 합니다. 물론 그 하위로 해도 최근에는 동작한다는 이야기도 있습니다만, 원칙적으로 jdk 경로까지만 지정합니다. 

다 입력하고 나면 콘솔 창을 새로 열어 `java -version` 을 입력해 명령어가 정상적으로 실행디는지 확인합니다. 

#### MacOS 환경 변수

MacOS 의 경우에는 조금 다른 형태로 지정합니다. 일반적으로는 .bash_profile 파일에 넣어 주어서 사용자가 로그인을 하고 나면 자동적으로 PATH가 설정되도록 하고 있습니다. 또 다른 방법으로는 .profile 안에 넣어 주는 것도 방법이고, 만약 zsh 을 사용하고 있다면 .zshrc 파일 하위에 적어 주어야 합니다.

## 참고자료 및 출처

- [Homebrew 를 이용한 패키지 관리](https://linkeverything.github.io/study/springboot/install-homebrew/)
- [Homebrew 를 이용한 Java 설치 및 관리](https://linkeverything.github.io/mac/install-java-with-brew/)

[^1]: 반드시 편리한 것은 아닙니다. 여러 버전의 Java를 설치해 두고 변경해가면서 테스트해야 하는 경우 등, 오히려 환경변수에 선언되어 있지 않아야 편한 경우도 있습니다.

[^2]: Windows 10 기준입니다.