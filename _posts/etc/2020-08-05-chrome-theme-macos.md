---
title: "MacOS/Windows 환경에서 크롬 테마설정 강제하기"
excerpt: "How to force dark/light theme setting on MacOS and Windows (currently not in settings page)"
categories:
  - "etc"
tag:
  - "chrome"
  - "theme"
last_modified_at: 2020-08-05
published: false
---

> OS 들에 어두운 테마(Dark theme) 가 나오기 시작한 이후, 별도의 설정 없이 **시스템 설정에 따라** 강제로 dark theme 가 활성화되는 경우가 있습니다. Google chrome 역시 이런 프로그램 중 하나인데, 강제로 변경하는 방법을 소개합니다.

저 역시 다크 모드를 사용하고 있고, 눈에 피로감을 덜 준다는 점에서 환영하는 상황이지만, 경우에 따라서는 굉장히 불편할 수 있습니다. 특히나 제대로 구현되지 않은 웹 페이지 등은 다크 모드에서 오히려 내용이 잘 보이지 않고, 브라우저 자체 기능으로 즐겨찾기 등 설정하는 경우에도 검정색 아이콘들은 잘 보이지 않는 등 문제가 좀 있을 수 있습니다. 

<br/>

## 크롬 테마 

예전에는 어두운(dark)/밝은(light) 테마 설정이 설정 페이지 내에 있었다고는 합니다. (당시 써보지 않아서 정확하게는 모르겠습니다.) 하지만 현재 크롬에서는 이 설정을 없애버렸고, 수동으로 조정할 수 없이 시스템 설정을 그대로 따라가게 되어 있습니다. 조금 손봐야 하는 설정이지만, 그래도 바꾸고자 하는 사용자를 위해서 변경하는 방법을 공유하겠습니다. 

<br/>

#### MacOS 에서 구글 크롬의 테마 강제하기

터미널을 실행하고 아래와 같이 입력합니다. 

```sh
defaults write com.google.Chrome NSRequiresAquaSystemAppearance -bool yes
```

만약 기본설정(시스템 설정에 따름)으로 되돌아가고 싶은 경우 아래와 같이 입력합니다. 

```sh
defaults delete com.google.Chrome NSRequiresAquaSystemAppearance
```

모든설정은 터미널에서 입력하고 난 뒤에 크롬을 완전 종료하고 재실행 해야 정상적으로 적용됩니다. 

만약 아이콘이 검은 색이라서 구분이 잘 안된다거나, 시크릿 모드와 구분이 잘 안되어 해제하고 싶은신 분들은 이 설정을 이용하시면 됩니다.

<br/>

#### Windows 에서 구글 크롬의 테마 강제하기

부득이 스크린샷을 할 수 없는 상황이라 우선 글로만 적어 두고 향후 추가할 수 있도록 하겠습니다. 이 방법의 요지는, 실행 파일에서 파라미터로 조정함으로써 다크 모드를 해제/활성화 하는 것입니다.

1. 구글 크롬을 실행하는 바로가기가 필요합니다. 

  시작 메뉴에 있든, 작업표시줄에 있든, 바탕화면에 있는 무관합니다. 대부분의 사용자들이 크롬을 `program files` 아래에서 직접 실행하지 않기 때문에 본인이 사용하는 바로가기를 찾으면 됩니다.

2. 바로가기를 우클릭하고 구성/환경설정 으로 이동합니다. 

3. 설정 창이 나타나면 **바로가기** 항목에서 **대상** 항목을 찾습니다. 
  아마도 대상 항목은 `chrome.exe` 로 끝나는 어떠한 경로를 가리키고 있을 것입니다.

4. 아래 내용을 삽입하여 줍니다. 기존에 `chrome.exe`로 끝나는 부분에 <mark style='background-color: #ffdce0'>추가</mark>해 줍니다.

  ```
  --disable-features=DarkMode
  ```

  결국 아래와 비슷한 형태가 됩니다.

  ```
  C:\Users\{계정 명}\AppData\Local\Google\Chrome SxS\Application\chrome.exe" --disable-features=DarkMode
  ```

5. **적용** 및 **확인** 을 눌러 창을 닫습니다.

6. 해당 바로가기를 더블 클릭하면 밝은 테마로 실행됩니다.

거꾸로 시스템이 밝은 테마인데 크롬을 다크 테마로 사용하고자 하는 경우도 있을 것입니다. 이 경우에는 위와 같이 바로가기에 대한 설정을 연 다음, `4.` 번 과정에서 아래와 같이 추가합니다.

  ```
  --force-dark-mode
  ```

이렇게 하면 강제로 다크 모드가 동작하게 됩니다.

<br/>

## 출처 및 참고자료

- <https://medium.com/@keysoodev/macos-크롬-google-chrome-에서-다크모드-강제-해제하기-4a08070797c7>
- <https://pureinfotech.com/disable-dark-mode-google-chrome-windows-10/>