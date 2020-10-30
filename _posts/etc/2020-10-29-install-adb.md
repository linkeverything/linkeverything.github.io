---
title: "Windows, MacOS 에 adb 설치하기 (+Homebrew)"
categories: [Etc]
tag: [adb]
last_modified_at: 2020-10-29
---
기존 블로그에서 adb 설치하기 글이 도움이 많이 되었는지 꾸준히 조회가 있어, 조금 더 최신에 맞게, 여러 OS에 대해, 다양한 방법으로 설명을 다시 하겠습니다. 

adb는 Android Debug Bridge 의 약자로, Android 앱 개발을 하는 분들이라면 반드시 깔아놓아야 하는 것 중 하나입니다. 이 툴은 Android 기반 기기를 PC에 연결했을 경우, 특정 포트를 이용해서 서로 통신할 수 있게 해주는 도구입니다. 

만약 앱 개발을 하여 앱이 하나 만들어졌을 떄(*.apk 파일), 이 파일을 에뮬레이터(emulator)가 아닌 실제 폰에서 테스트해보고 싶다면 adb를 이용하여 다음 처럼 입력하게 됩니다(단순한 예일 뿐입니다).

```sh
adb install aaa.apk
```

아마도 단말 개발을 하는 분들이거나, 유사한 커널을 사용하는 기기들(소형 전자기기 류: 스마트워치, raspberry pi, IoT장비 등)을 개발하는 분들이 여전히 많기 때문인 것 같습니다. 

각설하고 각 OS별로 설치법에 대해서 설명해 보겠습니다. 

<br/>

## 원래는 어떻게 쓰는건데?

Android 개발에 대해서 기본적으로 <mark style='background-color: #fff5b1'>Android Studio</mark>를 사용합니다. Android Studio는 IntelliJ[^1] 라는 프로그램을 기반으로 제작된 Android App을 개발하기 위한 IDE(Integrated Development Environment, 통합 개발 환경)입니다.

Android Studio를 설치하려고 하면 설치 도중에 SDK[^2] Manager를 설치/설정 하는 화면이 나타나고 특정 경로[^3]에 설치하게 됩니다. 이렇게 설치된 SDK Manager는 SDK가 직접 포함되어 있지는 않고, 설치하고 실행할 수 있는, 그리고 SDK의 버전관리 등을 할 수 있는 그야말로 Manager 프로그램입니다. 

이 SDK Manager를 통해서 아래와 같은 것들을 설치할 수 있습니다(제 개인적으로 정리한 것입니다. 전부를 나타내진 않습니다).

- 각 Android의 버전 별 API Library
- 에뮬레이터용 이미지들
- Google play Store 관련 라이브러리
- 하드웨어 가속 관련 부분
- 각종 Tool

#### SDK Manager

![](/assets/images/2020-10-29-install-adb/capture 2020-10-30 PM 05.53.43.png)

![](/assets/images/2020-10-29-install-adb/capture 2020-10-30 PM 05.53.50.png)

![](/assets/images/2020-10-29-install-adb/capture 2020-10-30 PM 05.53.57.png)

우리가 이야기하는 adb 는 두 번째 스크린샷에서 보이는 `Android SDK Platform Tools` 패키지에 포함되어 있습니다. Android 에서는 당연히 Android Studio 를 통한 설치를 권장하겠지만, 루팅이나, Device연결만 되면 되는 가벼운 이유로 Android Studio 까지 설치할 이유는 없습니다. 

<br/>

## adb 다운로드

다운로드는 Windows나 MacOS나 동일합니다. 공식 홈페이지의 [다운로드 페이지](https://developer.android.com/studio/releases/platform-tools?hl=ko)에서 각 플랫폼에 맞는 버전을 다운로드 합니다. 다운로드를 시작하면 약관 동의를 하게 하고, 설치 프로그램이 다운로드 됩니다. 어느 일반적인 프로그램들 처럼 실행하고 설치하면 됩니다.

<br/>

#### (Windows) 환경 변수 설정하기

<br/>

#### (MacOS) PATH에 추가하기

<br/>

## MacOS - Homebrew 이용하기

<br/>

## 출처 및 참고자료

- <https://developer.android.com/studio/command-line/adb?hl=ko>
- <https://www.ntu.edu.sg/home/ehchua/programming/android/Android_HowTo.html>

[^1]: 과거 Android 개발 환경을 갖추려면 Eclipse 기반에 Plug-in 형태로 설치한 적도 있었습니다. 기능에 한계가 있고, 다른 plug-in 과의 충돌 등으로 인해 2016년 초반에 Eclipse 에 대한 개발 툴 지원 중단을 발표하였습니다. 

[^2]: Software Development Kit, 개발도구

[^3]: 맥 OS 의 경우, ~/Library/Android/sdk 경로에, Windows 의 경우 c:\Users\\{사용자명}\AppData\Local\Android\Sdk 경로가 기본값입니다.