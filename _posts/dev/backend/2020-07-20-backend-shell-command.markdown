---
title: "Java에서 원격 서버에 명령 실행하기"
excerpt: "How to run command on remote server"
categories:
  - "dev"
  - "backend"
tag:
  - "java"
  - "remote command"
last_modified_at: 2020-07-20
---

> Java 에서 원격 서버 명령어를 실행하는 데에는 apache-commons 에 있는 라이브러리를 사용하면 편리합니다. maven / gradle 환경에서 apache-commons 을 빈번하게 사용하기 때문에 그대로 사용하면 됩니다.

Backend, 즉 서버측 프로그래밍을 하는데, 어떤 이유로든, 서버의 명령어를 직접 실행하는 것은 지양해야 하는 기능입니다. 하나의 서버 프로그램이 동작하는 데 있어서 서버의 특정 명령어를 그 자체로 실행하는 것은 흔치 않을 뿐 아니라 <mark style='background-color: #ffdce0'>보안</mark> 측면에서도 매우 위험한 기능이라고 할 수 있습니다. 따라서 가급적이면 서버 측의 여러 부분을 직접 다루는 프로그램을 별도로 구성하고, 이 프로그램에 <mark style='background-color: #ffdce0'>요청(request)</mark> 하여 그 프로그램만이 그 기능을 수행하도록 한정하는 것이 좋습니다. 가급적이면 보안적인 측면을 잘 고려하여 문제가 없도록 구성하기 바랍니다.

Java 에서 원격 서버에 명령을 보내는데 있어, <mark style='background-color: #fff5b1'>보안</mark>에 대한 부분을 확보하기 위해서는 <mark style='background-color: #fff5b1'>ssh / sftp</mark> 를 이용해야 하며, 이를 도와주는 라이브러리가 **<mark style='background-color: #ffdce0'>jsch 라이브러리</mark>** 입니다. 그 사용법을 간단히 정리해 보겠습니다. 

이 문서에서 사용한 소스는 다음의 gitHub 링크에 있습니다. 

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/jsch-sample){:target="_blank" .btn .btn--primary}

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

우선, maven / gradle 에서 다음과 같이 라이브러리를 추가해 줍니다. 

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

#### Java 샘플 소스 코드

다음과 같이 sample 소스를 작성합니다. 

```java
import com.jcraft.jsch.*;

public class JschSample {

    public static void main(String[] args) {

        String username = "root";
        String host = "192.168.56.1";
        int port = 22;
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

            System.out.println("==> Connected to" + host);

            channelExec.setCommand("touch ~/jschTest.txt");
            channelExec.connect();

            System.out.println("==> Connected to" + host);

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
==> Connected to192.168.56.1
==> Connected to192.168.56.1

Process finished with exit code 0
```

```sh
-rw-rw-r--  1 root root    0  7월 20 13:38 jschTest.txt
```

<br/>

## 참고자료 및 출처

https://www.leafcats.com/177