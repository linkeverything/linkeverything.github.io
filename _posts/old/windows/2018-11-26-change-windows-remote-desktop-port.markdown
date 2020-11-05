---
title:      "How to change port for Remote Desktop"
categories: [old]
last_modified_at:       2018-11-26 15:56:00 
tags:
  - Windows
  - RDP
  - Port
---

## 들어가며

추후에 아마도 설명하겠지만.. 저는 Windows 10 pro 위에 Hyper-V 기능을 이용해서 Ubuntu 18.04 와 Windows 8.1 을 올려놓고 사용중입니다. Hyper-V 의 기능으로, 위에서 말한 세 개의 OS는 공유기 하위에 별도의 IP를 할당받고 있습니다. (NAT기능, 추후 설명 예정)

이렇게 해 놓으면 한 개의 물리적인 머신 한 개만으로도 3개의 OS를 각각 돌릴 수 있고, 원격 데스크톱 기능을 이용해서 각 OS에 직접 접근할 수 있습니다. 집 안이 아닌, 외부에서도 가능하다는 점이 가장 큰 장점입니다. (저는 Windows 8.1 을 각종 은행 및 공공기관 Active-X 대응용으로 사용하고 있습니다.)

![](/assets/images/posts/old/img/post/2018-11-26-change-rdp-port/post-change-rdp-port-00001.png)

공유기의 설정 화면에서 위와 같이 각각의 IP 주소를 할당받은 것을 확인하면, 각각의 OS에 원격으로 접근할 수 있어야 합니다. 저의 경우, Ubuntu와 Windows 8.1 에 대해서 각각 40000번, 50000번대의 IP를 그대로 내부에 포트포워딩 되도록 설정 해 두었고, 외부에서 접근 가능하도록 해 두었습니다. 이렇게 되면 원격 데스크톱 연결 과정에서 50000번대의 숫자에 접근했을 때 Windows 의 내부 50000번대로 진입하게 됩니다. 물론 이를 포트포워딩 기능을 이용하여 직접 저 IP의 3389에 할당해 줄 수도있지만, 그렇게 하면 계속 어떤 숫자에 어떻게 들어가고 있는지 헷갈려서 내부 포트번호도 변경해야 할 필요성을 느끼게 되었습니다. 

여기서는 윈도우에서 기본적으로 할당되어 있는 3389 포트를 다른 포트로 변경해서 외부에서 접근 시도가 일관성 있게 내부로 이동되로록 해 보겠습니다. 레지스트리 수정 과정이 필요하므로, 이러한 방법이 싫은 분들은 다른 방안을 모색해 보시기 바랍니다.



## 수정하기

##### 시작 > 실행 > regedit 를 입력하여 레지스트리 편집기에 진입합니다. 

![](/assets/images/posts/old/img/post/2018-11-26-change-rdp-port/post-change-rdp-port-00002.png)

원격 데스크톱 연결의 포트 번호는 레지스트리에 등록되어 있고, Windows 의 설정 등에서 변경하는 것이 불가능하므로 레지스트리 편집기로 직접 수정해 주는 것으로 하겠습니다. 



##### port 번호를 지정하는 레지스트리를 찾아가 수정합니다. 

HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp

(위 내용을 복사해서 윗쪽의 경로에 붙여넣기 하시면 됩니다.)

![](/assets/images/posts/old/img/post/2018-11-26-change-rdp-port/post-change-rdp-port-00003.png)

우측에서 PortNumber 값을 찾아서 더블클릭하면 값을 편집할 수 있게 되고, 10진수 값으로 원하는 포트 번호를 입력해 줍니다.

![](/assets/images/posts/old/img/post/2018-11-26-change-rdp-port/post-change-rdp-port-00004.png)



##### 외부에 오픈하는 포트를 방화벽에 추가해 줍니다. 

제어판 > 시스템 및 보안 > Windows 방화벽 > 고급 설정 으로 이동하여, 인바운드 규칙 부분에서 앞서 사용하던 3389에 해당하는 인바운드 규칙을 삭제합니다. 사실 그대로 삭제하지 않아도 무방하나 깔끔한 설정을 원하신다면 삭제하세요.

그리고 다시 아래 조건들을 넣어 인바운드 규칙을 하나 신규로 생성합니다. 

- TCP

- (신규 생성한 포트번호)

![](/assets/images/posts/old/img/post/2018-11-26-change-rdp-port/post-change-rdp-port-00005.png)




##### 원격 데스크톱 연결 서비스를 중지시키고 다시 실행합니다. 

![](/assets/images/posts/old/img/post/2018-11-26-change-rdp-port/post-change-rdp-port-00006.png)



여기까지 진행하면 이제 변경된 포트로 원격 데스크톱 접속이 가능합니다. 다른 포트들도 그렇겠지만, 저는 이렇게 해서 원격 데스크톱을, 우분투의 22번포트도 변경해서 둘 다를 외부에서도 편하게 (포트 번호 헷갈리는 것 없이) 접근해서 잘 사용하고 있습니다.



출처: https://4urdev.tistory.com/59 [Simplify]

