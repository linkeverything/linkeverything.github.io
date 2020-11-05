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

![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/capture 2020-10-30 PM 05.53.43.png)

![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/capture 2020-10-30 PM 05.53.50.png)

![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/capture 2020-10-30 PM 05.53.57.png)

우리가 이야기하는 adb 는 두 번째 스크린샷에서 보이는 `Android SDK Platform Tools` 패키지에 포함되어 있습니다. Android 에서는 당연히 Android Studio 를 통한 설치를 권장하겠지만, 루팅이나, Device연결만 되면 되는 가벼운 이유로 Android Studio 까지 설치할 이유는 없습니다. 

## adb 다운로드

다운로드는 Windows나 MacOS나 동일합니다. 공식 홈페이지의 [다운로드 페이지](https://developer.android.com/studio/releases/platform-tools?hl=ko)에서 각 플랫폼에 맞는 버전을 다운로드 합니다. 다운로드를 시작하면 약관 동의를 하게 하고, 설치 프로그램이 다운로드 됩니다. 어느 일반적인 프로그램들 처럼 실행하고 설치하면 됩니다.[^4]

![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/capture 2020-10-30 PM 05.53.57.png)

#### (Windows) 환경 변수 설정하기

Windows 에서 환경 변수 설정을 하는 것은 `cmd`창, 그러니까 `명령 프롬프트`에서 자유롭게 adb 명령어를 사용하기 위함입니다.[^5]

1. 우선 제어판 > 시스템에 진입합니다. (win키를 눌러 시작 메뉴가 올라온 상태에서 '제어판'을 입력하면 됩니다) 그리고 고급 시스템 설정을 누릅니다. 
    ![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/win1.png)

2. 아래 버튼 중 '환경 변수'를 누릅니다. 

    ![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/win2.png)

3. 위 아래 두 개의 영역 중에서 아래쪽에 '시스템 변수'부분에서 새로만들기를 클릭합니다. 

    ![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/win3.png)

4. 새로만들기 창이 나타나면 아래와 같이 입력합니다. <mark style='background-color: #ffdce0'>여기서 변수 값 이라고 되어 있는 부분에는 본인이 설치한 SDK 위치를 입력</mark>해 줍니다. 만약 기본 설정 그대로 다음, 다음을 눌러 진행했다면 `c:\Users\\{사용자명}\AppData\Local\Android\Sdk\platform-tools` 가 될 것입니다. '확인'을 눌러 빠져나옵니다. 

    ![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/win4.png)

5. 이제 '시스템 변수' 영역에서 Path 라는 값을 찾습니다(99.99%확률로 있습니다. 만약 없다면 만들어주면 되지만, 잘 찾아보세요.) 그리고 '편집'을 눌러줍니다.

    ![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/win5.png)

6. `Path` 변수에 대한 편집 창이 나타나면 아래 그림과 같이 아까 설정한 변수의 앞, 뒤에 `%`를 붙여서 추가해 줍니다.[^6]

    ![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/win6.png)

7. '확인' 버튼을 계속 눌러 빠져나옵니다. 

이제 '명령 프롬프트'를 실행하고 `adb --version` 를 입력해 봅니다. 명령어가 주르륵 나오면 정상 설치된 것입니다. 

#### (MacOS) PATH에 추가하기

맥에서의 방법은 Linux 계열과 동일합니다. 저는 .zshrc 파일에 수정하게 되겠지만 .bash_profile 등 적절한 파일에 추가해 주면 됩니다. 

우선 sdk 가 설치된 폴더의 경로를 알아냅니다. 설치 과정에서 지정하여 주었을 수 있고 지정하지 않았다면 아마도 아래와 같은 경로에 설치되어 있을 것입니다. 

![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/capture 2020-10-30 PM 06.35.42.png)

그러면 본인의 상황에 맞게(저의 경우 .zshrc) 파일을 수정해 주면 됩니다. 아래 그림처럼 PATH에 해당 경로를 추가해주는 식으로 넣어주면 됩니다. 

```
export PATH="$HOME/Library/Android/sdk/platform-tools:$PATH"
```

![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/capture 2020-10-30 PM 06.37.28.png)

다시 해당 파일을 로드하고 `adb --version` 명령어를 입력하여 제대로 설정되었는지를 확인합니다. 

![](/assets/images/posts/dev/mobile/2020-10-29-install-adb/capture 2020-10-30 PM 06.43.10.png)

## MacOS - Homebrew 이용하기[^7]

homebrew 를 사용한다면 다음 명령어로 손쉽게 설치가 가능합니다. 제 기억에 homebrew를 이용해서 설치하면 자동으로 `PATH`에도 잡아주기 때문에 여러모로 편리합니다. 

```sh
brew install android-platform-tools
```

## 출처 및 참고자료

- <https://developer.android.com/studio/command-line/adb?hl=ko>
- <https://www.ntu.edu.sg/home/ehchua/programming/android/Android_HowTo.html>

[^1]: 과거 Android 개발 환경을 갖추려면 Eclipse 기반에 Plug-in 형태로 설치한 적도 있었습니다. 기능에 한계가 있고, 다른 plug-in 과의 충돌 등으로 인해 2016년 초반에 Eclipse 에 대한 개발 툴 지원 중단을 발표하였습니다. 

[^2]: Software Development Kit, 개발도구

[^3]: 맥 OS 의 경우, ~/Library/Android/sdk 경로에, Windows 의 경우 c:\Users\\{사용자명}\AppData\Local\Android\Sdk 경로가 기본값입니다.

[^4]: 설치 방법은 따로 설명하지 않습니다. 그냥 다음, 다음만 눌러도 설치가 잘 됩니다.

[^5]: 프로그램이 설치되면서 자동으로 설정되는 경우도 있으므로, 이 부분이 생략 가능한지는 해당 설정화면에서 확인하고 진행하시기 바랍니다.

[^6]: 명령어 우선순위를 주려면 스크린샷처럼 아래가 아닌, 맨 위에 주는 것이 맞을 겁니다. 그렇지만 명령어가 겹칠 일이 거의 없다는 가정으로 맨 아래에 추가하였습니다.

[^7]: homebrew 설치법에 대해서는 [제 티스토리블로그](https://4urdev.tistory.com/112)에 정리해 두었습니다.