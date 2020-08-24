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

## Check network ports

TCP 중에서(t), Listening 상태인 것들만(l), 상세정보까지 포함하여(p), 10진수 숫자로 표현(n)

```bash
$ netstat -tnlp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
tcp6       0      0 :::22                   :::*                    LISTEN      -
```

이 명령어는 Mac 에서는 정상적으로 구동되지 않습니디. 대신 아래 명령어로 확인 가능합니다.
{: .notice--warning}

```bash
netstat -v
```

만약 netstat 명령어를 찾지 못한다고 나오면, **yum** 이나 **apt-get** 명령어로 설치하여 실행하세요.
{: .notice--info}

## target 의 포트가 열려 있는지 확인

#### nc -z {hostname} {port}

특정 포트에 대해서 확인할 경우에 사용합니다. ping 을 해보는 것과는 다릅니다. ping 은 해당 서버(target)에서 ping 을 막아놓는 경우도 있으므로, 그것만으로 확인하기에는 한계가 있습니다. 아래 결과에서도 알 수 있듯이, 어떤 목적으로 열려있는 포트인지도 확인이 가능합니다.

```bash
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

#### telnet {hostname} {port}

아래와 같이 'connected to...' 가 나타나면 정상적으로 연결된 것입니다. 

```bash
$ telnet 1xx.1xx.2xx.1xx 22
Trying 1xx.1xx.2xx.1xx..
Connected to 1xx.1xx.2xx.1xx.
Escape character is '^]'.
SSH-2.0-OpenSSH_7.4
```

## Open firewall

netstat 으로 확인했을 때에 상태가 listen 으로 보여진다고 하더라도, 방화벽에서 막혀 있다면 정상적으로 접근할 수 없습니다. (가장 많이 하는 실수) 내부적으로는 localhost 로 확인해서 정상 동작하는 줄 알고 있다가 외부 시스템에서는 접근이 안되는 경우, 방화벽 문제가 아닌지를 반드시 확인해 보아야 합니다.
{: .notice--danger}

#### 방화벽에 허용 포트 추가/확인/삭제

아래 명령어로 포트를 열어줄 수 있습니다. 

```bash
firewall-cmd --permanent --zone=public --add-port=22/tcp
```

포트 열렸는지 확인은 다음 명령어로 합니다.

```bash
firewall-cmd --list-all
```

그리고 방화벽 정보를 바탕으로 서비스를 재시작합니다. (이 과정을 빼먹으면 서버를 재부팅하는 시점에 반영됩니다.)

```bash
firewall-cmd --reload
```

만약 추가했던 포트를 다시 닫아주려면, 즉 방화벽 규칙에서 삭제하려면 다음 명령어를 입력합니다. 

```bash
firewall-cmd --permanent --zone=public --remove-port=22/tcp
```

#### 참고 및 출처

https://suzxc2468.tistory.com/m/165