---
title: "맥에서 특정 포트를 사용하는 프로세스 확인"
categories: [mac]
tag: [process, port, lsof, netstat]
last_modified_at: 2021-07-15
---

프로그램을 사용할 때는 거의 그런 일들이 없지만, 개발을 하다 보면 특정 포트가 겹쳐서 실행이 되지 않는 경우가 있습니다. 이럴 때에는 해당 프로세스를 찾아서 kill 하고, 내가 원하는 프로그램을 다시 실행시키면 됩니다. 

그런데, 이 포트라는 것이, 한번 사용할 때에 OS 에서 할당받는 개념으로 되어 있고, 그래서 프로그램이 정상 종료되면 반환하게 되지만, 그렇지 않고 강제 종료되는 등의 일이 발생하면 포트가 사용중인 것 처럼 더 이상 사용할 수 없게 됩니다. 물론 이 경우 재부팅을 하고 나면 자연스럽게 사라지는 현상이지만, 개발을 하다가 재부팅을 빈번하게 할 수도 없는 상황이므로 아래 내용을 참고하면 좋습니다. 

## 특정 포트를 사용하는 프로세스 찾기

아마도 서버단 개발을 많이 진행한 분들이라면 `netstat` 이라는 명령어를 아주 잘 알고 계실겁니다. 최근에는 container 형태의 배포 형태가 많아졌기 때문에 그 중요성이 작아졌지만, 과거 하나의 머신(machine, VM)에 여러 개의 프로세스를 띄우는 경우 어떤 포트를 어느 프로세스가 사용하고 있는지 찾는 것은 매우 흔하고 중요한 일이었습니다. 

#### netstat 사용해보기

netstat 명령어는 리눅스 명령어로, 그야말로 네트워크(<mark style='background-color: #fff5b1'>net</mark>work)의 상태(<mark style='background-color: #fff5b1'>stat</mark>us) 를 체크하는 명령어입니다. 맥에서는 터미널이나 iterm같은 프로그램을 실행하고, 아래와 같이 입력합니다. 

```sh
$ netstat -anv | grep LISTEN 
```

```sh
tcp4       0      0  127.0.0.1.631          *.*                    LISTEN      131072 131072  26008      0 0x0080 0x00000006
tcp6       0      0  ::1.631                *.*                    LISTEN      131072 131072  26008      0 0x0080 0x00000006
tcp4       0      0  127.0.0.1.49315        *.*                    LISTEN      131072 131072    724      0 0x0100 0x00000002
tcp4       0      0  127.0.0.1.16107        *.*                    LISTEN      131072 131072    669      0 0x0100 0x00000006
tcp4       0      0  *.15018                *.*                    LISTEN      131072 131072    692      0 0x0000 0x00000006
tcp46      0      0  *.3283                 *.*                    LISTEN      131072 131072    528      0 0x0000 0x00000202
tcp6       0      0  *.49177                *.*                    LISTEN      131072 131072    397      0 0x0100 0x00000006
tcp4       0      0  *.49177                *.*                    LISTEN      131072 131072    397      0 0x0100 0x00000006
tcp4       0      0  127.0.0.1.2015         *.*                    LISTEN      131072 131072     70      0 0x0180 0x00000026
tcp4       0      0  *.88                   *.*                    LISTEN      131072 131072    116      0 0x0180 0x00000006
tcp6       0      0  *.88                   *.*                    LISTEN      131072 131072    116      0 0x0180 0x00000006
tcp6       0      0  fe80::aede:48ff:.49159 *.*                    LISTEN      131072 131072     77      0 0x0180 0x00000006
tcp6       0      0  fe80::aede:48ff:.49158 *.*                    LISTEN      131072 131072     77      0 0x0180 0x00000006
tcp6       0      0  fe80::aede:48ff:.49157 *.*                    LISTEN      131072 131072     77      0 0x0180 0x00000006
tcp6       0      0  fe80::aede:48ff:.49156 *.*                    LISTEN      131072 131072     77      0 0x0180 0x00000006
tcp6       0      0  fe80::aede:48ff:.49155 *.*                    LISTEN      131072 131072     77      0 0x0180 0x00000006
tcp6       0      0  fe80::aede:48ff:.49154 *.*                    LISTEN      131072 131072     77      0 0x0180 0x00000006
tcp4       0      0  *.5900                 *.*                    LISTEN      131072 131072      1      0 0x0180 0x00000006
tcp6       0      0  *.5900                 *.*                    LISTEN      131072 131072      1      0 0x0180 0x00000006
tcp4       0      0  *.22                   *.*                    LISTEN      131072 131072      1      0 0x0180 0x00000006
tcp6       0      0  *.22                   *.*                    LISTEN      131072 131072      1      0 0x0180 0x00000006
```

여기서 `-anv` 부분은 옵션으로서, 필요에 따라서 다양한 옵션을 이용해서 표현할 수 있고, grep 부분은 그 결과 중에서 어떤 문구가 포함된 부분만을 발췌하겠다는 의미입니다.
{: .notice--info} 

만약 `5900` 포트에 대해서 확인을 해 보고 싶다면, grep 명령어를 이용해서 다음과 같이 응용할 수 있습니다. 

```sh
$ netstat -anv | grep LISTEN | grep 5900
```

```sh
tcp4       0      0  *.5900                 *.*                    LISTEN      131072 131072      1      0 0x0180 0x00000006
tcp6       0      0  *.5900                 *.*                    LISTEN      131072 131072      1      0 0x0180 0x00000006
```

여기서 pid 는 두 개의 131072 뒤에 나오는 1 입니다. (뒤쪽에서 활용합니다)


## 조금 다른 방향으로 찾아보자

또 다른 명령어로 `lsof` 라는 것이 있습니다. 저도 이번에 공부하면서 알게 된 명령어인데, 사용법은 아래와 같습니다. 

```sh
$ sudo lsof -iTCP -sTCP:LISTEN -n -P 
```

```sh
COMMAND     PID        USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
launchd       1        root    8u  IPv6 0xd7741fb83718b26b      0t0  TCP *:22 (LISTEN)
launchd       1        root    9u  IPv4 0xd7741fb8371919f3      0t0  TCP *:22 (LISTEN)
launchd       1        root   11u  IPv6 0xd7741fb83718b26b      0t0  TCP *:22 (LISTEN)
launchd       1        root   12u  IPv4 0xd7741fb8371919f3      0t0  TCP *:22 (LISTEN)
launchd       1        root   16u  IPv6 0xd7741fb83718b8cb      0t0  TCP *:5900 (LISTEN)
launchd       1        root   17u  IPv4 0xd7741fb83719241b      0t0  TCP *:5900 (LISTEN)
launchd       1        root   19u  IPv6 0xd7741fb83718b8cb      0t0  TCP *:5900 (LISTEN)
launchd       1        root   20u  IPv4 0xd7741fb83719241b      0t0  TCP *:5900 (LISTEN)
expressvp    70        root    5u  IPv4 0xd7741fb8371956e3      0t0  TCP 127.0.0.1:2015 (LISTEN)
remoted      77        root    4u  IPv6 0xd7741fb83718c58b      0t0  TCP [fe80:5::aede:48ff:fe00:1122]:49154 (LISTEN)
remoted      77        root    5u  IPv6 0xd7741fb83718ac0b      0t0  TCP [fe80:5::aede:48ff:fe00:1122]:49155 (LISTEN)
remoted      77        root    6u  IPv6 0xd7741fb8396c826b      0t0  TCP [fe80:5::aede:48ff:fe00:1122]:49156 (LISTEN)
remoted      77        root    7u  IPv6 0xd7741fb8396c88cb      0t0  TCP [fe80:5::aede:48ff:fe00:1122]:49157 (LISTEN)
remoted      77        root    8u  IPv6 0xd7741fb8396c8f2b      0t0  TCP [fe80:5::aede:48ff:fe00:1122]:49158 (LISTEN)
remoted      77        root    9u  IPv6 0xd7741fb8396c958b      0t0  TCP [fe80:5::aede:48ff:fe00:1122]:49159 (LISTEN)
kdc         116        root    5u  IPv6 0xd7741fb83975ac0b      0t0  TCP *:88 (LISTEN)
kdc         116        root    7u  IPv4 0xd7741fb837194293      0t0  TCP *:88 (LISTEN)
rapportd    397 jonghunpark    5u  IPv4 0xd7741fb837190fcb      0t0  TCP *:49177 (LISTEN)
rapportd    397 jonghunpark    6u  IPv6 0xd7741fb83975bf2b      0t0  TCP *:49177 (LISTEN)
ARDAgent    528 jonghunpark    9u  IPv6 0xd7741fb83ae4c58b      0t0  TCP *:3283 (LISTEN)
delfino     669 jonghunpark    9u  IPv4 0xd7741fb843fa986b      0t0  TCP 127.0.0.1:16107 (LISTEN)
UniCRSLoc   692 jonghunpark    3u  IPv4 0xd7741fb8421a6e43      0t0  TCP *:15018 (LISTEN)
Google      724 jonghunpark  103u  IPv4 0xd7741fb843fa79f3      0t0  TCP 127.0.0.1:49315 (LISTEN)
cupsd     26008        root    5u  IPv6 0xd7741fb8562e158b      0t0  TCP [::1]:631 (LISTEN)
cupsd     26008        root    6u  IPv4 0xd7741fb843fab6e3      0t0  TCP 127.0.0.1:631 (LISTEN)
```

sudo 명령어이기 때문에 관리자여야만 실행할 수 있습니다. 특정 포트에 대해서만 찾아보려면,

```sh
$ sudo lsof -i :5900 -n -P
```

```sh
COMMAND PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
launchd   1 root   16u  IPv6 0xd7741fb83718b8cb      0t0  TCP *:5900 (LISTEN)
launchd   1 root   17u  IPv4 0xd7741fb83719241b      0t0  TCP *:5900 (LISTEN)
launchd   1 root   19u  IPv6 0xd7741fb83718b8cb      0t0  TCP *:5900 (LISTEN)
launchd   1 root   20u  IPv4 0xd7741fb83719241b      0t0  TCP *:5900 (LISTEN)
```

## 프로세스 종료시키기

특정 프로세스를 종료시키는 명령어는 `kill` 입니다. 이 명령어는 프로그램의 terminate 동작에 해당하는 부분을 실행하지 않고, 즉시 종료시켜 버리기 때문에 특정 프로그램에는 문제를 일으킬 수 있습니다. 또한 UI 가 있는 프로그램의 경우에는 마지막 layout 등을 저장하고 종료되는데 그러한 과정 없이 종료되어 버리므로 다시 실행하면 처음 실행했던 모습으로 남아 있을 수도 있습니다. 

```
$ kill -9 {pid}
```

## 출처 및 참고자료

- <https://woonizzooni.tistory.com/entry/Mac-listen-포트-pid-확인-방법-TCPUDP-세션-확인-방법>
- <https://new93helloworld.tistory.com/138>