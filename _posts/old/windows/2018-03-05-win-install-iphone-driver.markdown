---
title:      "How to install Apple USB driver without iTunes"
categories: [old]
last_modified_at:       2018-03-05
tags:
  - Windows
  - iPhone
  - driver
  - itunes
---

## 들어가며

아이튠즈를 설치하면 아이폰 드라이버는 자동으로 설치가 이루어집니다. 하지만 아이튠즈를 설치할 수 없는 환경이라면 부득이하게 드라이버를 별도로 설치해야 할 필요가 있습니다. 드라이버만을 설치해 봐야 별다른 조치를 할 수 없다는 점에서 무의미한 행동이라고 할 수는 있겠지만, 애플 제품의 특성 상, 드라이버가 정상적으로 설치되지 않으면 USB를 이용한 충전조차 되지 않는 것 같아, 검색한 결과를 포스팅합니다. 


## 해결방법

##### iTunes 최신 버전 다운로드

Apple 홈페이지(https://www.apple.com/kr/itunes/)에서 최신 아이튠즈 설치 파일을 다운로드합니다. 

![](/assets/images/posts/old/img/post/2018-03-05-win-install-iphone-driver/win-install-iphone-driver-00001.png)

##### 다운로드 받은 파일을 반디집 등으로 엽니다. 

![](/assets/images/posts/old/img/post/2018-03-05-win-install-iphone-driver/win-install-iphone-driver-00002.png)

##### AppleMobileDeviceSupportXXXX.msi 를 압축풀고 설치한다.

중간에 오휴 메시지가 나타나면 무시(Ignore)를 누르고 진행합니다. 

(만약 그래도 잘 설치가 안된다면, AppleApplicationSupportXX.msi 도 설치하고 진행하라고 하네요..)



위 상황까지 진행한 뒤에 폰을 연결해 보면 드라이버가 정상적으로 잡히면서 연결되는 것을 확인할 수 있습니다. 

부득이하게 드라이버를 설치해야 하는 상황에서 활용하면 될 것 같습니다..



출처: https://4urdev.tistory.com/37 [Simplify]