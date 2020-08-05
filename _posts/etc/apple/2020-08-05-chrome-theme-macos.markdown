---
title: "맥 환경에서 크롬 테마설정 강제하기"
excerpt: "How to change chrome theme on MacOS"
categories:
  - "etc"
  - "apple"
tag:
  - "chrome"
  - "theme"
last_modified_at: 2020-08-05
---

> OS 들에 어두운 테마(Dark theme) 가 나오기 시작한 이후, 별도의 설정 없이 **시스템 설정에 따라** 강제로 dark theme 가 활성화되는 경우가 있습니다. Google chrome 역시 이런 프로그램 중 하나인데, 강제로 변경하는 방법을 소개합니다.

<br/>

#### 구글 크롬의 다크모드 해제하기

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

## 참고

- https://medium.com/@keysoodev/macos-%ED%81%AC%EB%A1%AC-google-chrome-%EC%97%90%EC%84%9C-%EB%8B%A4%ED%81%AC%EB%AA%A8%EB%93%9C-%EA%B0%95%EC%A0%9C-%ED%95%B4%EC%A0%9C%ED%95%98%EA%B8%B0-4a08070797c7