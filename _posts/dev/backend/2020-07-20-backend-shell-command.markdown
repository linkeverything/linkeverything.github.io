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

> Java 에서 원격 서버 명령어를 실행하는 데에는 apache-commons 에 있는 라이브러리를 사용하면 편리합니다. maven / gradle 환경에서 apache-commons 을 빈번하게 사용하기 때문에 그래도 사용하면 됩니다.

Java 에서 원격 서버에 명령을 날리는데 있어, 보안에 대한 부분을 확보하기 위해서는 ssh / sftp 를 이용해야 하며, 이를 도와주는 라이브러리가 jsch 라이브러리 입니다. 그 사용법을 간단히 정리해 보겠습니다. 

#### maven dependency 설정

maven / gradle 에서 다음과 같이 라이브러리를 추가해 줍니다. 

```xml
<!-- https://mvnrepository.com/artifact/com.jcraft/jsch -->
<dependency>
    <groupId>com.jcraft</groupId>
    <artifactId>jsch</artifactId>
    <version>0.1.54</version>
</dependency>
```

> gradle 환경에서도 크게 다르지 않습니다.

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

#### 참고자료 및 출처

https://www.leafcats.com/177