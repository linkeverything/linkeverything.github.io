---
title: "열려있는 포트 확인, 방화벽 오픈"
excerpt: "check opened network ports, open inbound at firewall"
categories:
  - "infra"
  - "linux"
tag:
  - "network"
  - "port"
  - "open firewall"
last_modified_at: 2020-07-10
---

몇 번을 찾아도 자꾸 까먹게 되는 명령어들이라 메모 형태로 적어서 보관합니다. 출처의 내용에서도 알 수 있듯이, Cent-os 기반으로 작성되었으나, 대부분의 linux 기반 시스템에서 유사하게 사용할 수 있으므로 참고용으로 활용할 수 있습니다. 
{: .notice--info}

<br/>

## netstat 이용하기

리눅스 시스템에서 네트워크 관련사항을 확인하는 가장 보편화된 명령어는 `netstat` 명령어입니다. 기본적으로 설치되어 있지 않은 시스템도 쉽게 설치가 가능하고, 사용법 역시 간단한 편입니다.

<br/>

## Check network ports

netstat 명령어는 그냥 명령어만 넣어도 모든 상태들을 다 보여줍니다. 대부분의 경우에는 다음 옵션을 주어 원하는 정보만을 보여줄 수 있습니다. 

- TCP 중에서(t)
- Listening 상태인 것들만(l)
- 상세정보까지 포함하여(p)
- 10진수 숫자로 표현(n)

```sh
$ netstat -tnlp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
tcp6       0      0 :::22                   :::*                    LISTEN      -
```

이 명령어는 MacOS 에서는 정상적으로 구동되지 않습니디. 대신 아래 명령어로 확인 가능합니다. 혹은 -tnl 까지만 입력하면 어느 정도의 정보를 볼 수 있습니다. 

```bash
netstat -v
```

만약 netstat 명령어를 찾지 못한다고 나오면, **yum** 이나 **apt-get** 명령어로 설치하여 실행하세요. (저는 별도 설치한 것인지 잘 기억이 나지 않아 확인이 불가합니다.)
{: .notice--info}

**2020. 08. 28. 추가**

MacOS 에서는 아래 명령어로 확인하는 것이 더 가독성 있게 보여주는 것으로 확인하였습니다. ([관련 글:StackOverflow](https://stackoverflow.com/questions/4421633/who-is-listening-on-a-given-tcp-port-on-mac-os-x))

```sh
lsof -iTCP -sTCP:LISTEN -n -P
```

당연히 이러한 명령어들에 `sudo` 를 붙여서 최상위 권한으로 실행하면 시스템에서 관리하는 프로세스에 대한 정보들까지 같이 보입니다. 
{: .notice--info}

<br/>

## 원격 서버의 포트가 열려 있는지 확인

<br/>

#### nc -z {hostname} {port}

원격 서버의 특정 포트에 대해서 확인할 경우에 사용하는 방법입니다. 일반적으로 `ping` 명령어를 이용하여 해당 서버에 접근 가능한지를 봅니다만, 이는 해당 ip(hostname)가 접근 가능한지 수준에서 보는 것에 가까우므로 이 내용과는 조금 다릅니다. 

ping 은 해당 서버(target)에서 ping 을 막아놓는 경우도 있으므로, 그것만으로 확인하기에는 한계가 있습니다. 아래 결과에서도 알 수 있듯이, 어떤 목적으로 열려있는 포트인지까지도  확인이 가능합니다.

```sh
// 열린포트
$ nc 1xx.1xx.2xx.1xx 873
@RSYNCD: 30.0
 

// 열린포트
$ nc 1xx.1xx.2xx.1xx 22
SSH-2.0-OpenSSH_7.4
 

// 안열린포트
$ nc 1xx.1xx.2xx.1xx 43
Ncat: Connection timed out.
```

<br/>

#### telnet {hostname} {port} 명령어 이용하기

telnet 을 이용하는 방법도 있습니다. 열려있는 경우라면 telnet으로는 거의 접속이 가능하므로 아래와 같이 'connected to...' 가 나타나면 정상적으로 연결된 것으로 확인할 수 있습니다. 

```bash
$ telnet 1xx.1xx.2xx.1xx 22
Trying 1xx.1xx.2xx.1xx..
Connected to 1xx.1xx.2xx.1xx.
Escape character is '^]'.
SSH-2.0-OpenSSH_7.4
```

<br/>

## 방화벽 열기

netstat 으로 확인했을 때에 상태가 listen 으로 보여진다고 하더라도, 방화벽에서 막혀 있다면 정상적으로 접근할 수 없습니다. (가장 많이 하는 실수) 내부적으로는 localhost 로 확인해서 정상 동작하는 줄 알고 있다가 외부 시스템에서는 접근이 안되는 경우, 방화벽 문제가 아닌지를 반드시 확인해 보아야 합니다.

보통의 시스템은 방화벽에서 대부분의 포트를 막고 있습니다. docker 등 container환경을 이용하는 경우에도 host의 특정 포트에 맵핑이 잘 되었는지도 확인해야겠지만, host의 방화벽에서 막고 있는 것이 아닌지에 대해서도 확인해야 합니다. 

참고로 대부분의 docker 이미지에서는 그 이미지가 사용하는 기본 포트 - 예를 들어 웹서버의 8080등 - 들에 대해서는 열어둔 채로 이미지가 배포되어 있습니다. 반대로 이야기하자면, 이미지를 linux 등 OS부터 빌드업해서 구성하는 경우 <mark style='background-color: #fff5b1'>포트를 오픈하는 것 까지 `Dockerfile` 에 명시</mark>해서 이미지를 생성해 주어야 한다는 것입니다. 

<br/>

#### 방화벽에 허용 포트 추가/확인/삭제

방화벽을 다루는 것은 `firewall-cmd`를 주로 사용합니다. 아마도 이 명령어는 기본적으로 설치되어 있지 않을 것입니다. 아래 명령어를 입력하여 방화벽 관리 프로그램을 설치합니다. (apt 명령어 이용)

```sh
$ sudo apt update && sudo apt install firewalld -y
```

제대로 설치되었는지 확인합니다.

```sh
$ sudo firewall-cmd --version
0.4.4.5
```

이제 아래 명령어로 포트를 열어줄 수 있습니다. <mark style='background-color: #fff5b1'>public zone</mark> 에, <mark style='background-color: #fff5b1'>영구적</mark>으로, <mark style='background-color: #fff5b1'>22</mark>번 <mark style='background-color: #fff5b1'>tcp</mark> 포트를 오픈하는 명령어입니다.

```sh
$ sudo firewall-cmd --permanent --zone=public --add-port=22/tcp
```

포트 열렸는지 확인은 다음 명령어로 합니다.

```sh
$ sudo firewall-cmd --list-all
```

그리고 <mark style='background-color: #dcffe4'>새로 저장한 방화벽 정보를 바탕으로 서비스를 재시작</mark>합니다. (이 과정을 빼먹으면 서버를 재부팅하는 시점에 반영됩니다.)

```bash
firewall-cmd --reload
```

만약 추가했던 포트를 다시 닫아주려면, 즉 방화벽 규칙에서 삭제하려면 다음 명령어를 입력합니다. 앞서 `--add-port` 를 사용했던 옵션이 `--remove-port` 로 변경해서 명령어를 사용합니다.

```bash
firewall-cmd --permanent --zone=public --remove-port=22/tcp
```

<br/>

## 출처 및 참고자료

https://suzxc2468.tistory.com/m/165