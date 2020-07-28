---
title: "우분투 서버 원격으로 깨우기"
excerpt: "How to set up Wake on Lan on Ubuntu Server"
categories:
  - "dev"
  - "infra"
tag:
  - "ubuntu"
  - "wol"
  - "wake on lan"
last_modified_at: 2020-07-24
---

> 윈도우와 마찬가지로 우분투 역시 WOL을 지원합니다. WOL은 원격지에서 Magic Packet 이라는 특정 패킷을 전송해서 Lan 카드를 통해 전원을 켜는 것을 말하는데, 전제 조건은 하드웨어 중 네트워크 카드가 이를 지원해야 합니다. (근래에 나온 거의 대부분의 랜카드가 이를 지원합니다.) 또한 BIOS 셋업에서도 이 설정을 On 으로 해 주어야 한다는 점 꼭 인지하고 진행하셔야 합니다.

Ubuntu 20.04 LTS 버전에서 테스트하고 확인한 내용입니다.

<br/>

## 준비 작업

다음 명령어로 관련 패키지들을 설치해 줍니다. 

```sh
$ sudo apt-get install net-tools ethtool wakeonlan
```

<br/>

## 설정하기

<br/>

#### 네트워크 ID 확인

ifconfig 명령으로 네트워크 카드의 고유 ID를 확인합니다.

```sh
$ ifconfig
```

```sh
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

enp2s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.143  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::5893:dcb:8646:7699  prefixlen 64  scopeid 0x20<link>
        ether b4:2e:99:24:fb:0b  txqueuelen 1000  (Ethernet)
        RX packets 130262  bytes 116557327 (116.5 MB)
        RX errors 0  dropped 31  overruns 0  frame 0
        TX packets 34235  bytes 2794367 (2.7 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        ine
```

위 명령어 실행 결과 보여지는 화면에서 각 네트워크 아답터의 고유 ID를 알아낼 수 있습니다. (ip 등 정보들을 보고 잘 찾으시길 바랍니다) 저 값을 잘 기억해 둡니다.

<br/>

#### ethtool 명령어 실행하기

앞서 설치한 ethtool 명령어를 이용해서 해당 아답터(인터페이스)의 wol 설정 상태를 확인하고, 해당 값을 **g** 로 변경합니다.

```sh
$ sudo ethtool {network_id}
```

```sh
Settings for enp2s0:
	Supported ports: [ TP MII ]
	Supported link modes:   10baseT/Half 10baseT/Full
	                        100baseT/Half 100baseT/Full
	                        1000baseT/Full
	Supported pause frame use: Symmetric Receive-only
	Supports auto-negotiation: Yes
	Supported FEC modes: Not reported
	Advertised link modes:  10baseT/Half 10baseT/Full
	                        100baseT/Half 100baseT/Full
	                        1000baseT/Full
	Advertised pause frame use: Symmetric Receive-only
	Advertised auto-negotiation: Yes
	Advertised FEC modes: Not reported
	Link partner advertised link modes:  10baseT/Half 10baseT/Full
	                                     100baseT/Half 100baseT/Full
	                                     1000baseT/Half 1000baseT/Full
	Link partner advertised pause frame use: Symmetric Receive-only
	Link partner advertised auto-negotiation: Yes
	Link partner advertised FEC modes: Not reported
	Speed: 1000Mb/s
	Duplex: Full
	Port: MII
	PHYAD: 0
	Transceiver: internal
	Auto-negotiation: on
	Supports Wake-on: pumbg
	Wake-on: g
	Current message level: 0x00000033 (51)
			       drv probe ifdown ifup
	Link detected: yes
```

```sh
$ sudo ethtool -s {network_id} wol g
```

<br/>

#### 자동 재실행 되도록 설정하기

이 설정은 재부팅 이후에는 제대로 다시 기본값으로 바뀌게 됩니다. 따라서 init script 쪽 등에 아래와 같은 내용을 추가합니다.

```sh
post-up /sbin/ethtool -s 인터페이스명 wol g
post-down /sbin/ethtool -s 인터페이스명 wol g
```

**2020-07-28 추가**

> 위 설정을 어디에 넣어야 하는지 고민하다가, 차라리 그냥 init.d script 에 넣어버리기로 마음 먹었습니다. 더 좋은 방법들 많을 겁니다. 그런데, 저 처럼 진행해도 잘 동작하는 것을 확인하였으니, 이대로 진행해도 좋을 듯 합니다.

우선 wakeonlanconfig.sh 파일을 하나 만들고, 아래처럼 내용을 넣었습니다. 

```sh
#!/bin/bash
ethtool -s enp2s0 wol g
exit
```

그리고 이 파일을 **/etc/init.d** 경로에 복사 해 주었습니다. 권한은 해당 폴더의 다른 파일들처럼 755 로 주었습니다. 

<br/>

## 기타

컴퓨터의 랜 단자 주변에 보면, 초록색, 빨간색의 두 개의 LED가 있는 것이 보통입니다. 정상적으로 전원을 OFF 한 경우, Wake on Lan 이 동작하는 중이라면 전원이 꺼져있는 상태에서도 이 두개의 LED가 깜빡거리고 있어야 맞습니다(저의 경우). 전원 버튼을 오래 눌러서 전원을 끄는 경우에는 Wake on lan 은 정상 동작하지 않습니다. 

<br/>

#### 참고자료 및 출처

https://matomo.org/faq/how-to-install/faq_23484/