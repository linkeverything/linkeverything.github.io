---
title: "사용중인 네트워크 정보(port), pid 확인"
categories: [linux]
tag: [network, port]
last_modified_at: 2020-07-10
published: false
---
이 내용은 상당 부분이 linux 의 환경에서와 동일합니다. mac의 terminal 에서 사용하는 기능(명령어) 들이 거의 linux(ubuntu) 에서의 명령어들이니까요. 참고용으로만 봐주시고 혹시 잘못된 내용은 댓글 달아주세요. 
{: .notice--info}

맥이나 리눅스 환경에서 어떤 프로그램이 어떤 포트를 사용하는지를 확인하는 일이 빈번합니다. 프로그램이 제대로 종료되지 않아, 어떤 포트를 점유하고 있는 경우도 있고, 혹은 일반적으로 자주 사용하는 포트, 예를 들어 8080, 9000 등과 같은 경우에는 다른 프로그램이 점유하고 있는 경우가 많습니다. 이런 경우에 다음의 방법을 이용하여 사용중인 포트들과 그 포트들을 사용하는 프로그램 id 를 확인할 수 있습니다. 

#### lsof (list open files) 

```bash
 jonghunpark@Jonghun-mbpw  ~  sudo lsof -iTCP -sTCP:LISTEN -n -P
COMMAND    PID        USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
UserEvent  115        root   75u  IPv6 0x36efe985d6956f91      0t0  TCP [fe80:8::aede:48ff:fe00:1122]:49161 (LISTEN)
UserEvent  115        root   76u  IPv6 0x36efe985d6956351      0t0  TCP [fe80:8::aede:48ff:fe00:1122]:49162 (LISTEN)
UserEvent  115        root   77u  IPv6 0x36efe985d6955d31      0t0  TCP [fe80:8::aede:48ff:fe00:1122]:49163 (LISTEN)
UserEvent  115        root   78u  IPv6 0x36efe985d69575b1      0t0  TCP [fe80:8::aede:48ff:fe00:1122]:49164 (LISTEN)
AnySign.e  126        root    9u  IPv4 0x36efe985d73e13c1      0t0  TCP 127.0.0.1:31026 (LISTEN)
AnySign.e  126        root   13u  IPv4 0x36efe985d73dcea1      0t0  TCP 127.0.0.1:31027 (LISTEN)
edpa       136        root    7u  IPv4 0x36efe985d88f6c41      0t0  TCP 127.0.0.1:56999 (LISTEN)
rapportd   459 jonghunpark    4u  IPv4 0x36efe985d88f6261      0t0  TCP *:49224 (LISTEN)
rapportd   459 jonghunpark    5u  IPv6 0x36efe985d6958e31      0t0  TCP *:49224 (LISTEN)
LogiMgrDa  689 jonghunpark    4u  IPv4 0x36efe985d73e0001      0t0  TCP *:59866 (LISTEN)
delfino    709 jonghunpark    9u  IPv4 0x36efe985dfb14881      0t0  TCP 127.0.0.1:16107 (LISTEN)
UniCRSLoc  959 jonghunpark    3u  IPv4 0x36efe985d9cd9621      0t0  TCP *:15018 (LISTEN)
Adguard    980 jonghunpark   46u  IPv4 0x36efe985dfb15c41      0t0  TCP 127.0.0.1:49388 (LISTEN)
Adguard    980 jonghunpark   48u  IPv6 0x36efe985da63b351      0t0  TCP *:49389 (LISTEN)
com.docke 1086 jonghunpark   10u  IPv4 0x36efe985da0cdc41      0t0  TCP 127.0.0.1:49336 (LISTEN)
```

#### netstat

```bash
 jonghunpark@Jonghun-mbpw  ~   netstat -anv | grep LISTEN
tcp4       0      0  127.0.0.1.16107        *.*                    LISTEN      131072 131072    709      0 0x0100 0x00000006
tcp46      0      0  *.49389                *.*                    LISTEN      131072 131072    980      0 0x0100 0x0000000e
tcp4       0      0  127.0.0.1.49388        *.*                    LISTEN      131072 131072    980      0 0x0100 0x0000000e
tcp4       0      0  *.59866                *.*                    LISTEN      131072 131072    689      0 0x0100 0x00000006
tcp4       0      0  127.0.0.1.49336        *.*                    LISTEN      131072 131072   1086      0 0x0100 0x00000026
tcp4       0      0  127.0.0.1.56999        *.*                    LISTEN      131072 131072    136      0 0x0080 0x00000086
tcp4       0      0  *.15018                *.*                    LISTEN      131072 131072    959      0 0x0000 0x00000006
tcp4       0      0  127.0.0.1.31027        *.*                    LISTEN      131072 131072    126      0 0x0180 0x00000006
tcp4       0      0  127.0.0.1.31026        *.*                    LISTEN      131072 131072    126      0 0x0180 0x00000006
tcp6       0      0  *.49224                *.*                    LISTEN      131072 131072    459      0 0x0100 0x00000006
tcp4       0      0  *.49224                *.*                    LISTEN      131072 131072    459      0 0x0100 0x00000006
tcp6       0      0  fe80::aede:48ff:.49164 *.*                    LISTEN      131072 131072    115      0 0x0180 0x00000006
tcp6       0      0  fe80::aede:48ff:.49163 *.*                    LISTEN      131072 131072    115      0 0x0180 0x00000006
tcp6       0      0  fe80::aede:48ff:.49162 *.*                    LISTEN      131072 131072    115      0 0x0180 0x00000006
tcp6       0      0  fe80::aede:48ff:.49161 *.*                    LISTEN      131072 131072    115      0 0x0180 0x00000006
```

#### Listen port & process, tcp session

```bash
 jonghunpark@Jonghun-mbpw  ~  sudo lsof -i -n -P | grep TCP
UserEvent  115            root   74u  IPv6 0x36efe985d6956971      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49160->[fe80:8::aede:48ff:fe33:4455]:59602 (ESTABLISHED)
UserEvent  115            root   75u  IPv6 0x36efe985d6956f91      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49161 (LISTEN)
UserEvent  115            root   76u  IPv6 0x36efe985d6956351      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49162 (LISTEN)
UserEvent  115            root   77u  IPv6 0x36efe985d6955d31      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49163 (LISTEN)
UserEvent  115            root   78u  IPv6 0x36efe985d69575b1      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49164 (LISTEN)
AnySign.e  126            root    9u  IPv4 0x36efe985d73e13c1      0t0    TCP 127.0.0.1:31026 (LISTEN)
AnySign.e  126            root   13u  IPv4 0x36efe985d73dcea1      0t0    TCP 127.0.0.1:31027 (LISTEN)
edpa       136            root    7u  IPv4 0x36efe985d88f6c41      0t0    TCP 127.0.0.1:56999 (LISTEN)
edpa       136            root   41u  IPv4 0x36efe985da0ce621      0t0    TCP 10.250.46.84:51533->203.235.210.169:10443 (ESTABLISHED)
logd       140            root   12u  IPv6 0x36efe985d6957bd1      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49165->[fe80:8::aede:48ff:fe33:4455]:50274 (ESTABLISHED)
bosUpdate  398 _softwareupdate    3u  IPv6 0x36efe985e0efcad1      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49881->[fe80:8::aede:48ff:fe33:4455]:50273 (ESTABLISHED)
SubmitDia  399            root    3u  IPv6 0x36efe985d6955711      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49162->[fe80:8::aede:48ff:fe33:4455]:50276 (ESTABLISHED)
SubmitDia  399            root    5u  IPv6 0x36efe985d6958811      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49209->[fe80:8::aede:48ff:fe33:4455]:50262 (ESTABLISHED)
biometric  416            root    3u  IPv6 0x36efe985d69581f1      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49177->[fe80:8::aede:48ff:fe33:4455]:50255 (ESTABLISHED)
rapportd   459     jonghunpark    4u  IPv4 0x36efe985d88f6261      0t0    TCP *:49224 (LISTEN)
rapportd   459     jonghunpark    5u  IPv6 0x36efe985d6958e31      0t0    TCP *:49224 (LISTEN)
findmydev  547            root    4u  IPv6 0x36efe985d69550f1      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49243->[fe80:8::aede:48ff:fe33:4455]:50252 (ESTABLISHED)
Google     631     jonghunpark   21u  IPv4 0x36efe985dfa4d881      0t0    TCP 127.0.0.1:50763->127.0.0.1:49389 (CLOSED)
Google     631     jonghunpark   24u  IPv4 0x36efe985e05ae9e1      0t0    TCP 127.0.0.1:51531->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   25u  IPv4 0x36efe985e16c5621      0t0    TCP 127.0.0.1:49579->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   26u  IPv4 0x36efe985e158f001      0t0    TCP 127.0.0.1:51491->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   27u  IPv4 0x36efe985e0bd6261      0t0    TCP 127.0.0.1:51535->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   28u  IPv4 0x36efe985e09709e1      0t0    TCP 127.0.0.1:51506->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   29u  IPv4 0x36efe985e0cf3881      0t0    TCP 127.0.0.1:49647->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   30u  IPv4 0x36efe985e09a5c41      0t0    TCP 127.0.0.1:51515->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   31u  IPv4 0x36efe985d6f77c41      0t0    TCP 127.0.0.1:51397->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   32u  IPv4 0x36efe985e0cf6001      0t0    TCP 127.0.0.1:51487->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   33u  IPv4 0x36efe985e08fb621      0t0    TCP 127.0.0.1:49641->127.0.0.1:49389 (ESTABLISHED)
Google     631     jonghunpark   34u  IPv4 0x36efe985dfe7bc41      0t0    TCP 127.0.0.1:51529->127.0.0.1:49389 (ESTABLISHED)
corespeec  676     jonghunpark    3u  IPv6 0x36efe985d6954ad1      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49292->[fe80:8::aede:48ff:fe33:4455]:50263 (ESTABLISHED)
LogiMgrDa  689     jonghunpark    4u  IPv4 0x36efe985d73e0001      0t0    TCP *:59866 (LISTEN)
delfino    709     jonghunpark    9u  IPv4 0x36efe985dfb14881      0t0    TCP 127.0.0.1:16107 (LISTEN)
UniCRSLoc  959     jonghunpark    3u  IPv4 0x36efe985d9cd9621      0t0    TCP *:15018 (LISTEN)
Adguard    980     jonghunpark   13u  IPv6 0x36efe985da63a711      0t0    TCP 127.0.0.1:49389->127.0.0.1:49641 (ESTABLISHED)
Adguard    980     jonghunpark   15u  IPv6 0x36efe985dfd60351      0t0    TCP 127.0.0.1:49389->127.0.0.1:51535 (ESTABLISHED)
Adguard    980     jonghunpark   16u  IPv4 0x36efe985e08fc001      0t0    TCP 10.250.46.84:51536->140.82.112.26:443 (ESTABLISHED)
Adguard    980     jonghunpark   19u  IPv6 0x36efe985dfd5f0f1      0t0    TCP 127.0.0.1:49389->127.0.0.1:51491 (ESTABLISHED)
Adguard    980     jonghunpark   46u  IPv4 0x36efe985dfb15c41      0t0    TCP 127.0.0.1:49388 (LISTEN)
Adguard    980     jonghunpark   48u  IPv6 0x36efe985da63b351      0t0    TCP *:49389 (LISTEN)
Adguard    980     jonghunpark   49u  IPv4 0x36efe985d7760001      0t0    TCP 10.250.46.84:51492->54.210.120.58:443 (ESTABLISHED)
Adguard    980     jonghunpark   52u  IPv6 0x36efe985dfd63451      0t0    TCP 127.0.0.1:49389->127.0.0.1:51531 (ESTABLISHED)
Adguard    980     jonghunpark   53u  IPv4 0x36efe985d9cda9e1      0t0    TCP 10.250.46.84:51532->13.225.112.122:443 (ESTABLISHED)
Adguard    980     jonghunpark   54u  IPv6 0x36efe985dfd60971      0t0    TCP 127.0.0.1:49389->127.0.0.1:51529 (ESTABLISHED)
Adguard    980     jonghunpark   55u  IPv4 0x36efe985e0bd5881      0t0    TCP 10.250.46.84:51530->172.217.24.142:443 (ESTABLISHED)
Adguard    980     jonghunpark   56u  IPv6 0x36efe985e16bff91      0t0    TCP 127.0.0.1:49389->127.0.0.1:49579 (ESTABLISHED)
Adguard    980     jonghunpark   57u  IPv4 0x36efe985e16c69e1      0t0    TCP 10.250.46.84:49580->172.217.175.99:443 (ESTABLISHED)
Adguard    980     jonghunpark   59u  IPv6 0x36efe985dfd5ead1      0t0    TCP 127.0.0.1:49389->127.0.0.1:51487 (ESTABLISHED)
Adguard    980     jonghunpark   60u  IPv4 0x36efe985e08f9881      0t0    TCP 10.250.46.84:51488->172.217.175.74:443 (ESTABLISHED)
Adguard    980     jonghunpark   61u  IPv6 0x36efe985dfd615b1      0t0    TCP 127.0.0.1:49389->127.0.0.1:51506 (ESTABLISHED)
Adguard    980     jonghunpark   62u  IPv6 0x36efe985da639ad1      0t0    TCP 127.0.0.1:49389->127.0.0.1:49647 (ESTABLISHED)
Adguard    980     jonghunpark   63u  IPv4 0x36efe985e0cf2ea1      0t0    TCP 10.250.46.84:49648->108.177.125.189:443 (ESTABLISHED)
Adguard    980     jonghunpark   64u  IPv4 0x36efe985d775cea1      0t0    TCP 10.250.46.84:51507->172.217.31.165:443 (ESTABLISHED)
Adguard    980     jonghunpark   65u  IPv6 0x36efe985e0efd0f1      0t0    TCP 127.0.0.1:49389->127.0.0.1:51397 (ESTABLISHED)
Adguard    980     jonghunpark   66u  IPv4 0x36efe985da0d03c1      0t0    TCP 10.250.46.84:51398->64.233.189.188:5228 (ESTABLISHED)
Adguard    980     jonghunpark   67u  IPv6 0x36efe985dfd5f711      0t0    TCP 127.0.0.1:49389->127.0.0.1:51515 (ESTABLISHED)
Adguard    980     jonghunpark   68u  IPv4 0x36efe985dfa4ec41      0t0    TCP 10.250.46.84:51516->216.58.197.142:443 (ESTABLISHED)
Adguard    980     jonghunpark   73u  IPv4 0x36efe985e0bd7621      0t0    TCP 10.250.46.84:49642->108.177.125.189:443 (ESTABLISHED)
aex-plugi 1028            root    8u  IPv4 0x36efe985e05ad621      0t0    TCP 10.250.46.84:49472->203.235.211.150:50124 (ESTABLISHED)
mobileact 1033            root    3u  IPv6 0x36efe985d6959451      0t0    TCP [fe80:8::aede:48ff:fe00:1122]:49315->[fe80:8::aede:48ff:fe33:4455]:50257 (ESTABLISHED)
com.docke 1086     jonghunpark   10u  IPv4 0x36efe985da0cdc41      0t0    TCP 127.0.0.1:49336 (LISTEN)
Slack\x20 1759     jonghunpark   21u  IPv4 0x36efe985d775f621      0t0    TCP 10.250.46.84:49678->18.178.165.242:443 (ESTABLISHED)
Slack\x20 1759     jonghunpark   25u  IPv4 0x36efe985d7d49c41      0t0    TCP 10.250.46.84:49682->18.178.165.242:443 (ESTABLISHED)
Slack\x20 1759     jonghunpark   26u  IPv4 0x36efe985d7d4b001      0t0    TCP 10.250.46.84:49683->18.178.165.242:443 (ESTABLISHED)
Citrix    8873     jonghunpark   17u  IPv4 0x36efe985dfed8c41      0t0    TCP 10.250.46.84:50638->10.179.80.39:2598 (ESTABLISHED)
```

#### 기타

특정 서버 포트(listen 포트) 상태를 보고 싶을 경우,
lsof 와 netstat 을 이용해서 아래와 같이 확인

```bash
 jonghunpark@Jonghun-mbpw  ~  sudo lsof -i :49389
COMMAND   PID        USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
Google    631 jonghunpark   21u  IPv4 0x36efe985e0db2c41      0t0  TCP localhost:51555->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   23u  IPv4 0x36efe985dac3c621      0t0  TCP localhost:51538->localhost:49389 (CLOSE_WAIT)
Google    631 jonghunpark   25u  IPv4 0x36efe985d7760001      0t0  TCP localhost:51606->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   26u  IPv4 0x36efe985e16c6001      0t0  TCP localhost:51592->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   27u  IPv4 0x36efe985d9cd8c41      0t0  TCP localhost:51565->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   29u  IPv4 0x36efe985e0cf3881      0t0  TCP localhost:49647->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   30u  IPv4 0x36efe985e09a5c41      0t0  TCP localhost:51515->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   32u  IPv4 0x36efe985e08f9881      0t0  TCP localhost:51597->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   33u  IPv4 0x36efe985e08fb621      0t0  TCP localhost:49641->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   34u  IPv4 0x36efe985dfe7bc41      0t0  TCP localhost:51529->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   35u  IPv4 0x36efe985e096f621      0t0  TCP localhost:51553->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   36u  IPv4 0x36efe985dac3d9e1      0t0  TCP localhost:51549->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   38u  IPv4 0x36efe985e0bd6261      0t0  TCP localhost:51563->localhost:49389 (ESTABLISHED)
Google    631 jonghunpark   39u  IPv4 0x36efe985d6f75ea1      0t0  TCP localhost:51580->localhost:49389 (ESTABLISHED)
Adguard   980 jonghunpark   13u  IPv6 0x36efe985da63a711      0t0  TCP localhost:49389->localhost:49641 (ESTABLISHED)
Adguard   980 jonghunpark   15u  IPv6 0x36efe985e16c0bd1      0t0  TCP localhost:49389->localhost:51606 (ESTABLISHED)
Adguard   980 jonghunpark   19u  IPv6 0x36efe985e16be0f1      0t0  TCP localhost:49389->localhost:51592 (ESTABLISHED)
Adguard   980 jonghunpark   48u  IPv6 0x36efe985da63b351      0t0  TCP *:49389 (LISTEN)
Adguard   980 jonghunpark   54u  IPv6 0x36efe985dfd60971      0t0  TCP localhost:49389->localhost:51529 (ESTABLISHED)
Adguard   980 jonghunpark   56u  IPv6 0x36efe985e16c2451      0t0  TCP localhost:49389->localhost:51597 (ESTABLISHED)
Adguard   980 jonghunpark   59u  IPv6 0x36efe985dfd62e31      0t0  TCP localhost:49389->localhost:51563 (ESTABLISHED)
Adguard   980 jonghunpark   62u  IPv6 0x36efe985da639ad1      0t0  TCP localhost:49389->localhost:49647 (ESTABLISHED)
Adguard   980 jonghunpark   65u  IPv6 0x36efe985e16c1811      0t0  TCP localhost:49389->localhost:51580 (ESTABLISHED)
Adguard   980 jonghunpark   67u  IPv6 0x36efe985dfd5f711      0t0  TCP localhost:49389->localhost:51515 (ESTABLISHED)
Adguard   980 jonghunpark   69u  IPv6 0x36efe985e16bdad1      0t0  TCP localhost:49389->localhost:51565 (ESTABLISHED)
Adguard   980 jonghunpark   71u  IPv6 0x36efe985dfd62811      0t0  TCP localhost:49389->localhost:51549 (ESTABLISHED)
Adguard   980 jonghunpark   74u  IPv6 0x36efe985dfd61bd1      0t0  TCP localhost:49389->localhost:51553 (ESTABLISHED)
Adguard   980 jonghunpark   76u  IPv6 0x36efe985dfd60f91      0t0  TCP localhost:49389->localhost:51555 (ESTABLISHED)
 jonghunpark@Jonghun-mbpw  ~  netstat -anv|grep '49389'
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51606        ESTABLISHED 406309 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51606        127.0.0.1.49389        ESTABLISHED 407614 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51597        ESTABLISHED 404574 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51597        127.0.0.1.49389        ESTABLISHED 405937 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51592        ESTABLISHED 406818 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51592        127.0.0.1.49389        ESTABLISHED 406547 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51580        ESTABLISHED 407174 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51580        127.0.0.1.49389        ESTABLISHED 405336 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51565        ESTABLISHED 405267 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51565        127.0.0.1.49389        ESTABLISHED 400339 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51563        ESTABLISHED 406218 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51563        127.0.0.1.49389        ESTABLISHED 400640 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51555        ESTABLISHED 404771 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51555        127.0.0.1.49389        ESTABLISHED 400450 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51553        ESTABLISHED 406221 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51553        127.0.0.1.49389        ESTABLISHED 400632 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51549        ESTABLISHED 405381 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51549        127.0.0.1.49389        ESTABLISHED 404889 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51538        FIN_WAIT_2  406870 146988    980      0 0x2131 0x0000000c
tcp4       0      0  127.0.0.1.51538        127.0.0.1.49389        CLOSE_WAIT  406492 146988      0      0 0x0122 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51529        ESTABLISHED 402107 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51529        127.0.0.1.49389        ESTABLISHED 401601 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51515        ESTABLISHED 402140 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.51515        127.0.0.1.49389        ESTABLISHED 402116 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.49647        ESTABLISHED 364787 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.49647        127.0.0.1.49389        ESTABLISHED 385238 146988      0      0 0x0102 0x00000008
tcp4       0      0  127.0.0.1.49389        127.0.0.1.49641        ESTABLISHED 366301 146988    980      0 0x0102 0x0000000c
tcp4       0      0  127.0.0.1.49641        127.0.0.1.49389        ESTABLISHED 381080 146988      0      0 0x0102 0x00000008
tcp46      0      0  *.49389                *.*                    LISTEN      131072 131072    980      0 0x0100 0x0000000e
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51599        TIME_WAIT   261312 146988    980      0 0x2131 0x0000000c
tcp4       0      0  127.0.0.1.49389        127.0.0.1.51603        TIME_WAIT   261312 146988    980      0 0x2131 0x0000000c
```

pid 만 찾는 경우

```bash
lsof -t -i:포트번호
```

IPv4만 (TCP & UDP)
```bash
lsof -i 4
```

IPv6만 (TCP & UDP)
```bash
lsof -i 6
```

## 출처 및 참고자료

- <https://woonizzooni.tistory.com/entry/Mac-listen-포트-pid-확인-방법-TCPUDP-세션-확인-방법>