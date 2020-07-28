---
title: "맥에서 스크린샷 파일 명 변경하기"
excerpt: "How to change screenshot file name on mac"
categories:
  - "etc"
  - "apple"
tag:
  - "screenshot"
  - "com.apple.screencapture"
last_modified_at: 2020-07-28
---

> 윈도우 기반에서는 `printscreen key` 를 누르거나, `alt` + `printscreen` 으로 작업하고 있는 PC의 화면을 캡쳐합니다. 다양한 방법도 있고 프로그램들도 있지만 기본 기능을 이용해도 충분합니다. 맥에서는 `command` + `shift` + `3` ~ `5` 키를 이용하여 화면을 캡쳐할 수 있습니다. 여기서는 캡쳐 방법은 간단히 용약하고 캡쳐된 파일의 파일명을 변경하는 방법을 공유합니다.

<br/>

## 기본적인 화면 캡쳐 방법

다음과 같은 방법으로 화면을 캡쳐할 수 있습니다. 저는 아무래도 `command` + `shift` + `4` 를 가장 많이 사용하는 것 같습니다. 

- `command` + `shift` + `3`

  현재 보고 있는 화면의 **전체화면** 캡쳐입니다. 맥 기본 기능 중에서 데스크톱을 여러 개 사용하고 있는 경우는 현재 보고 있는 데스크톱을 전체 캡쳐해 줍니다. 

- `command` + `shift` + `4`

  영역 캡쳐라고 보면 됩니다. 이 단축키를 누르게 되면 커서가 십자가 모양으로 바뀌고, 원하는 영역을 드레그해서 선택하면 선택된 부분이 캡쳐 파일로 저장됩니다.
  
  이 단축키를 이용하여 **창 캡쳐**가 가능합니다. 캡쳐하려는 창 위에 십자가로 된 커서를 둔 상태에서 `spacebar` 를 누르면 그 창이 선택되어지고, 거기서 다시 `return` 키를 눌러 캡쳐할 수 있습니다.

- `command` + `shift` + `5`

  영역 캡쳐입니다. 쉽게 영역을 조절하고 캡쳐할 수 있습니다. 위에서 이야기한 **4** 를 이용해도 되지만, 마우스를 사용하지 않는 경우에는 이 방법이 더 정확하게 캡쳐가 가능합니다. 

<br/>

## 파일명 변경하기 

일반적으로 캡쳐 기능을 사용하게 되면 다음 그림과 같이 **스크린샷 YYYY-MM-DD ㅇ{오전/오후} HH.mm.ss.png** 파일로 저장됩니다. 

![](/assets/images/2020-07-28-screenshot-name/old.png){: .align-center}

#### 파일명 변경하기

이제 터미널을 띄우고, 아래 명령어를 입력하여 파일명 규칙을 변경합니다.

```sh
$ defaults write com.apple.screencapture name {파일명};killall SystemUIServer
```

저는 새로운 파일명을 **screenCapture** 라고 지정해 보았습니다. 

```sh
$ defaults write com.apple.screencapture name screenCapture;killall SystemUIServer
```

그 결과는 다음과 같습니다.

![](/assets/images/2020-07-28-screenshot-name/new.png){: .align-center}



## 참고

https://macread.tistory.com/3