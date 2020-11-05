---
title: "Java에서 원격 서버에 명령 실행하기"
categories: [Java]
tag: [java, remote command]
last_modified_at: 2020-07-20
---
Java 에서 원격 서버 명령어를 실행하는 데에는 apache-commons 에 있는 라이브러리를 사용하면 편리합니다. maven / gradle 환경에서 apache-commons 을 빈번하게 사용하기 때문에 그대로 사용하면 됩니다.

Backend, 즉 서버측 프로그래밍을 하는데, 어떤 이유로든, 서버의 명령어를 직접 실행하는 것은 지양해야 하는 기능입니다. 하나의 서버 프로그램이 동작하는 데 있어서 서버의 특정 명령어를 그 자체로 실행하는 것은 흔치 않을 뿐 아니라 <mark style='background-color: #ffdce0'>보안</mark> 측면에서도 매우 위험한 기능이라고 할 수 있습니다. 따라서 가급적이면 서버 측의 여러 부분을 직접 다루는 프로그램을 별도로 구성하고, 이 프로그램에 <mark style='background-color: #ffdce0'>요청(request)</mark> 하여 그 프로그램만이 그 기능을 수행하도록 한정하는 것이 좋습니다. 가급적이면 보안적인 측면을 잘 고려하여 문제가 없도록 구성하기 바랍니다.

Java 에서 원격 서버에 명령을 보내는데 있어, <mark style='background-color: #fff5b1'>보안</mark>에 대한 부분을 확보하기 위해서는 <mark style='background-color: #fff5b1'>ssh / sftp</mark> 를 이용해야 하며, 이를 도와주는 라이브러리가 **<mark style='background-color: #ffdce0'>jsch 라이브러리</mark>** 입니다. 그 사용법을 간단히 정리해 보겠습니다. 

이 문서에서 사용한 소스는 다음의 gitHub 링크에 있습니다. 

[Download Sample code from GitHub](https://github.com/simpl-ify/SampleProjects/tree/master/jsch-sample){:target="_blank" .btn .btn--primary}

<br/>

## 샘플 소스

<br/>

#### Java project 에 라이브러리 추가하기

만약 본인이 사용하는 프로그램이 순수한 Java 프로그램이라면 `.jar ` 파일을 다운로드 받아 `lib`폴더 등에 포함시켜야 합니다.

[홈페이지](http://www.jcraft.com/jsch/) 에서 정식 버전을 다운로드 받아 사용하여도 되고, [MavenRepository](https://mvnrepository.com/artifact/com.jcraft/jsch/0.1.54) 에서 수동으로 다운로드 받아도 무관합니다. 어떤 방식으로 다운로드 받든, 원하는 버전을 받아서 프로젝트에 포함시키고 `Import` 가 가능한 상태로만 로드해 주면 됩니다.

사용하고 있는 IDE(개발 툴)에 따라 프로젝트에 포함시키는 방법이 조금 상이할 수 있습니다. 각 IDE에 맞추어 적절한 방법으로 포함해주는 것이 좋습니다. 저는 주력으로 IntelliJ 를 사용하고 있지만, 거의 대부분의 프로젝트가 Maven / Gradle 기반이기 때문에 이에 대한 설명을 하겠습니다.
{: .notice--info}

<br/>

#### maven dependency 설정

maven 을 사용하면 일반적인 Java 프로그래밍을 할 때보다 조금 더 편리한 방법으로 라이브러리를 관리할 수 있습니다. 특정 라이브러리를 `jar` 파일 자체로 포함시키는 것이 아니라 어떤 버전의 어떤 라이브러리를 사용하겠다고 명시해 두는 것만 있기 때문에 프로젝트 자체에만 집중할 수 있고, 프로젝트를 공유하는 데 불필요한 공간 낭비를 방지해 줍니다.

maven 이나 gradle 에서 다음과 같이 라이브러리를 추가해 줍니다. 

```xml
<dependency>
    <groupId>com.jcraft</groupId>
    <artifactId>jsch</artifactId>
    <version>0.1.54</version>
</dependency>
```

```gradle
dependencies {
    ...
    implementation 'com.jcraft:jsch:0.1.55'
}
```

<br/>

## Java 샘플 소스 코드

<br/>

#### 필요한 정보

원격 서버에 접속하여 어떠한 명령을 수행하려면 다음의 정보가 필요합니다.
- 원격 서버 주소(IP)
- 원격 접속 포트(port) 번호
- 사용자 이름
- 비밀번호

당연한 정보들이지만, 이러한 정보를 미리 확보해 두고, 작업에 들어가는 것이 가장 편합니다. 접속을 원하는 시스템에 대한 `IP`와 `port` 를 알아내고 나면, 해당 시스템에 이 명령에 사용할 특정 사용자를 생성하고 비밀번호를 설정합니다.

이러한 작업에 사용되는 사용자 계정은 별도로 생성하여 활용하는 것이 좋습니다. 만약 이 프로그램에 문제가 발생하거나 혹은 권한 처리를 추가로 해야하는 경우, 다른 프로그램이나 시스템에 영향을 최소화하기 위해 별도의 계정으로 관리하기를 권장합니다. 
{: .notice--warning}

위에서 얻은 정보를 이용하여 putty(windows) 나 terminal(MacOS) 에서 접속을 시도해 봅니다. 정상적으로 접속되고 명령 프롬프트가 보인다면 제대로 설정된 것이라고 보면 됩니다.

이 정보들을 properties 파일이나 yml 파일, json 등의 설정 파일로 별도 관리하고 추후 변경사항 발생 시 유연하게 대처하는 것도 필요합니다만, 여기서는 그대로 `class` 파일 내의 변수로 처리하겠습니다. 

```java
        String host = "192.168.56.1";
        int    port = 22;
        String username = "root";
        String password = "root";
```

<br/>

#### JSch 객체 생성, Session 생성

우선 아래 명령으로 JSch 객체를 생성합니다. 

```java
JSch jsch = new JSch();
```

그리고 여기서 앞서 받아놓았던 서버 정보들을 이용해서 Host, port, username 을 설정하여 session 을 받아옵니다. 

```java
Session session = jsch.getSession(username, host, port);
```

이렇게 받아온 `Session`객체는 그저 java 객체입니다. 아직 서버에 접속하거나 하지 않은 Java 객체입니다. 여기에 비밀번호를 설정해 줍니다. 

```java
session.setPassword(password);
```

그리고 properties 객체를 하나 만들어서 `StrictHostKeyChecking` 이라는 설정값을 `no` 로 설정해준 뒤, 그 설정을 session 객체에 넣어줍니다.

```java
java.util.Properties config = new java.util.Properties();

config.put("StrictHostKeyChecking", "no");

session.setConfig(config);
```

만약 위에서 java.util.Properties 부분이 어색하면 Properties 만 넣어주면서 명시적으로 import 해 주면 됩니다. 이 예제에서는 편의상 jsch.* 로 import 하였기 때문에 그 패키지 안의 Properties 객체와 중첩되는 것을 방지하기 위해 이렇게 설정해 주었습니다. 
{: .notice--warning}

<br/>

#### 접속하기

이제 앞서 `host`, `port`, `username`, `password` 를 설정하고 설정 정보를 추가해준 `session` 객체를 이용해서 서버에 접속해 줍니다. 접속 방법은 `session`객체의 `connect()` 메소드를 이용하면 되는데, 이 메소드는 void 를 리턴하므로 아무런 객체도 반환하지 않습니다. 그 때문에 `try ~ catch` 로 감싸주고 접속에 문제가 생겼을 때에 대한 대비책을 마련해 두어야 합니다.

```java
session.connect();
```

<br/>

#### 명령 요청하기

여기까지 정상적으로 수행되었다면 <mark style='background-color: #dcffe4'>접속</mark>은 성공한 것입니다. 그렇다면 이제 그 접속정보(session)를 이용해서 명령을 요청해 보도록 하겠습니다. 

명령을 요청하기 위해서는 우선 접속정보인 `session`에서 channel 이라고 하는 것을 열어야 합니다. `session`에 있는 `openChannel()` 메소드를 이용하면 되는데, 파라미터로 **"exec"** 라는 문자열을 넘겨줍니다.

```java
session.openChannel("exec");
```

**"exec"** 라는 문자열을 파라미터로 넘겨주는 것은 명령줄에서 명령을 요청하기 위함입니다. 다른 형태의 채널을 열려면 다음 표를 참고합니다.

| 체널 타입(openChannel 의 파라미터) | 리턴 타입(채널 객체) |
|---------|-----------------|
|session|ChannelSession|
|shell|ChannelShell|
|exec|ChannelExec|
|x11|ChannelX11|
|auth-agent@openssh.com|ChannelAgentForwarding|
|direct-tcpip|ChannelDirectTCPIP|
|forwarded-tcpip|ChannelForwardedTCPIP|
|sftp|ChannelSftp|
|subsystem|ChannelSubsystem|

이제 이렇게 가져온 Channel 객체를 적절한 객체로 Type Casting 하여 아래와 같이 요청을 보냅니다.

```java
ChannelExec channelExec = (ChannelExec) channel;

channelExec.setCommand("touch ~/jschTest.txt");
channelExec.connect();
```

여기서의 명령은 사용자 홈 폴더(~)에 `jschTest.txt` 파일을 생성(`touch`) 하라는 의미입니다. 마지막에 `connect()` 함수를 호출해 주면 이 때에 원격지에 명령을 호출합니다.

<br/>

#### finally

마지막으로는 앞서 사용한 session 객체와 channel 객체를 닫아주는 것입니다. 닫아주지 않으면 앞서 접속안 정보가 불필요하게 남아 Memory leak 등의 원인이 될 수 있고, 서버에 접속한 상태(열린 상태)로 남아있을 수 있으므로 반드시 닫아줍니다.

```java
    if (channel != null) {
        channel.disconnect();
    }
    if (session != null) {
        session.disconnect();
    }
```

<br/>

## 전체 소스 코드

다음과 같이 전체 소스코드를 확인할 수 있습니다. 더욱 확실힌 정보는 GitHub Link 에 있습니다.

```java
import com.jcraft.jsch.*;

public class JschSample {

    public static void main(String[] args) {

        String host = "192.168.56.1";
        int    port = 22;
        String username = "root";
        String password = "root";
        
        System.out.println("==> Connecting to" + host);
        Session session = null;
        Channel channel = null;

        // 2. 세션 객체를 생성한다 (사용자 이름, 접속할 호스트, 포트를 인자로 준다.)
        try {
            // 1. JSch 객체를 생성한다.
            JSch jsch = new JSch();
            session = jsch.getSession(username, host, port);

            // 3. 패스워드를 설정한다.
            session.setPassword(password);

            // 4. 세션과 관련된 정보를 설정한다.
            java.util.Properties config = new java.util.Properties();
            // 4-1. 호스트 정보를 검사하지 않는다.
            config.put("StrictHostKeyChecking", "no");
            session.setConfig(config);

            // 5. 접속한다.
            session.connect();

            // 6. sftp 채널을 연다.
            channel = session.openChannel("exec");

            // 8. 채널을 SSH용 채널 객체로 캐스팅한다
            ChannelExec channelExec = (ChannelExec) channel;

            channelExec.setCommand("touch ~/jschTest.txt");
            channelExec.connect();

        } catch (JSchException e) {
            e.printStackTrace();
        } finally {
            if (channel != null) {
                channel.disconnect();
            }
            if (session != null) {
                session.disconnect();
            }
        }


    }

}
```

<br/>

#### 실행 및 확인

실행해 보면 다음과 같은 결과가 보이게 되며, 서버에서 파일을 확인할 수 있습니다.

```sh
==> Connecting to192.168.56.1

Process finished with exit code 0
```

해당 사용자의 홈 폴더에 가 보면 다음과 같이 파일이 생성되어 있는 것을 확인할 수 있습니다. 

```sh
-rw-rw-r--  1 root root    0  7월 20 13:38 jschTest.txt
```

<br/>

## 참고자료 및 출처

- <https://www.leafcats.com/177>