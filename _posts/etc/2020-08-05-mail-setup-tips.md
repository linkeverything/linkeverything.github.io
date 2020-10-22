---
title: "이메일 설정 시 팁 모음 - 2FA 인증 포함"
excerpt: "Tips for setting email POP3 and 2FA authentication"
categories:
  - "etc"
tag:
  - "email"
  - "tips"
last_modified_at: 2020-08-05
---

> 이메일을 업무나 개인적으로 많이 사용하게 되면서 다음의 설정들을 많이 활용하였습니다. 메모 목적도 있으나, 찾는데 시간을 많이 소요하게 되는 부분이라 공유 차원에서 작성하는 글입니다. 

저처럼 다양한 메일을 한 곳에서 보려고 애쓰는 분들이 있을지는 모르겠습니다. 처음에 빌드업을 잘못한 탓에, 하나의 이메일로 사용하는데에는 실패한 상황입니다. 

- 현재 주력 이메일 : gmail 1
- Apple Id 로 사용중인 이메일 : gmail 2
- 학교 이메일
- 네이버 메일

이렇게 네 가지 정도를 사용하고 있습니다. 여기에 추가적으로 업무용, 업무 테스트용으로 사용중인 gmail 아이디가 2개 정도 되는 것 같습니다. (다 테스트 용도의 이메일들이고, 주 이메일로 등록한 GCP, Azure, AWS 등의 인스턴스와 꼬이는 것을 방지하기 위해서 별도의 이메일로 업무용을 사용중입니다.)

<br/>

## 여러 메일을 한 곳에서 받기

저는 현재 학교 메일(ac.kr), 지메일(gmail.com), 네이버 메일(naver.com) 등을 주로 사용하고 있습니다. 사실 gmail 이 주력이긴 한데, gmail 주소가 20자가 넘어가다 보니 간혹 입력되지 않는 웹페이지 등이 있어 네이버도 사용학 있습니다.(현재는 다수의 웹에서 수정된 사항이지만 여전히 20자 이상의 이메일 주소를 허용하지 않는 사이트들이 있습니다.) 이런 경우 각각의 앱을 설치하거나 혹은 웹에서 확인해도 되지만, 저는 pop3 설정으로 gmail 에서 모든 메일을 확인하고 있습니다.

수신하는 모든 메일에 대해서 <mark style='background-color: #ffdce0'>전달</mark> 기능을 사용할 수도 있습니다. 하지만 이렇게 되면 <mark style='background-color: #fff5b1'>FW</mark> 같은 문구가 제목 앞에 포시되기도 하고, 회신 할 경우 본 메일이 아닌 다른 메일로 하게 되는 격이 되므로 권장하는 방법은 아닙니다. 물론 "보기만" 하는 경우에는 쓸수있는 기능이긴 합니다.

<br/>

#### IMAP or POP server settings

pop3 는 복잡한 기술적인 설명을 배제하면 단순히 해당 메일 서버에 접속해서 신규 메일을 가져오는 기능입니다. 직접 여러 메일 서비스에 로그인하지 않아도 한 곳에 모아서 볼 수 있다는 장점이 있습니다. 

또한, pop3 를 설정하는 과정에서 SMTP 서버도 설정하게 되는데, 이를 이용해서 해당 메일로 회신을 하는 것도 가능합니다. pop3를 설정하기 위해서는 각 메일 프로바이더들이 제공하는 서버 정보를 알아야 하고, 거의 대부분의 메일 프로바이더들이 이 정보를 공개하고 있습니다. 

당연하게도, 어떠한 이유로든 지나치게 빈번하게 pop3 요청이 발생하는 경우, 해당 메일 프로바이더가 요청을 거부할 수 있음을 밝히고 있습니다. 따라서 pop3를 이용하는 주 메일 계정은 믿을 수 있는 서비스에서 하기를 권장합니다. 

아래는 pop3 를 설정하면서 필요한 각 메일 provider의 서버 정보입니다. 

| Email Provider | IMAP Settings | POP Settings | SMTP Settings |
|----------------|---------------|--------------|---------------|
|AOL (including Verizon.net)|Server: imap.aol.com<br/>Port: 993<br/>Encryption: SSL/TLS|Server: pop.aol.com<br/>Port: 995<br/>Encryption: SSL/TLS|Server: smtp.aol.com<br/>Port: 465<br/>Encryption: SSL/TLS|
|Gmail|Server: imap.gmail.com<br/>Port: 993<br/>Encryption: SSL/TLS|Server: pop.gmail.com<br/>Port: 995<br/>Encryption: SSL/TLS|Server: smtp.gmail.com<br/>Port: 465<br/>Encryption: SSL/TLS|
|iCloud|Server: imap.mail.me.com<br/>Port: 993<br/>Encryption: SSL/TLS|Server:<br/>Port:<br/>Encryption:|Server: smtp.mail.me.com<br/>Port: 587<br/>Encryption: STARTTLS|
|MSN|Server: imap-mail.outlook.com<br/>Port: 993<br/>Encryption: SSL/TLS|Server: pop-mail.outlook.com<br/>Port: 995<br/>Encryption: SSL/TLS|Server: smtp-mail.outlook.com<br/>Port: 587<br/>Encryption: STARTTLS|
|Microsoft 365|Server: outlook.office365.com<br/>Port: 993<br/>Encryption: SSL/TLS|Server: outlook.office365.com<br/>Port: 995<br/>Encryption: SSL/TLS|Server: smtp.office365.com<br/>Port: 587<br/>Encryption: STARTTLS|
|Outlook.com<br/>Hotmail.com<br/>Live.com|Server: outlook.office365.com<br/>Port: 993<br/>Encryption: SSL/TLS|Server: outlook.office365.com<br/>Port: 995<br/>Encryption: SSL/TLS|Server: smtp.office365.com<br/>Port: 587<br/>Encryption: STARTTLS|
|Yahoo!|Server: imap.mail.yahoo.com <br/>Port: 993<br/>Encryption: SSL|Server: pop.mail.yahoo.com<br/>Port: 995<br/>Encryption: SSL|Server: smtp.mail.yahoo.com <br/>Port: 587 or 465<br/>Encryption: TLS/STARTTLS or SSL|

pop3 설정에서 유의해야 할 부분이 있습니다. 왜 그런지 모르겠으나 이 글을 작성하는 시점에는 다단계 pop3 가 지원되지 않습니다. 원래 그렇게 지원을 안해주는 것인지는 확인해봐야 하겠습니다.

현재 주 메일로 사용중인 gmail 이 pop3 를 5개까지만 허용하고 있습니다. 따라서 제가 가진 모든 메일을 한 곳에서 받기에는 약간 부족합니다. 이를 해소하기 위해서 두 번째 부모를 선정하고 그 부모에 pop3 를 연결해 보았습니다. 그리고 그 새로운 부모 계정을 다시 주 계정에서 pop3 로 연결해 보았으나, 메일이 넘어오지 않았습니다. 

아마도 다단계로 처리되는 것이 시스템에 부하를 줄 수도 있고, 이런식으로 꼬리에 꼬리를 물게 되면, 루프(loop) 를 돌며 시스템에 문제를 줄 수 있어서일 수도 있습니다. 

<br/>

#### pop3 설정 시 2FA 를 설정중인 경우

만약, pop3로 메일을 가져오려는 계정이 이중인증 설정이 되어 있는 경우에는 id, password 를 이용한 로그인 방법으로는 로그인이 되지 않습니다. 이런 경우에는 다음과 같이 <mark style='background-color: #ffdce0'>앱 비밀번호</mark> 라는 것을 설정하여 이를 통해서 로그인해야 합니다. (gmail 기준 용어입니다.)

여기서 이야기하는 <mark style='background-color: #ffdce0'>앱 비밀번호</mark>라는 것은, 다른 플랫폼에서도 유사하게 사용됩니다. 예를 들어 GitHub 의 경우에도 2FA 를 설정한 경우에는 Git push 등의 명령을 하기 위해서는 이러한 형태의 별도의 비밀번호를 발급받아 사용해야 합니다. 
{: .notice--warning}

앱 비밀번호라는 이름에서 "앱" 은 크게 신경쓰지 않을 대상입니다. 이 비밀번호의 역할은 사용자가 의도적으로 임시 비밀번호를 생성해서 특정 서비스에 제공해준 비밀번호로서, 만약 어떠한 식으로든 문제가 발생하는 경우 해당 비밀번호를 만료(삭제)시킴으로서 문제를 해결할 수 있게 도와줍니다. 

1. Google 계정으로 로그인 된 상태에서, `내 계정` - `보안` - `비밀번호 및 로그인 방식` - `앱 비밀번호` 로 이동합니다. 

2. 앱 비밀번호 항목에 본인이 원하는 항목을 입력한 뒤 생성 버튼을 눌러 생성합니다. 

3. 생성된 앱 비밀번호(**16자리**)를 복사하여 로그인 시에 id / password 대신 id / {16자리} 를 이용하여 로그인합니다.

이렇게 하면 보안은 유지하면서, pop3를 설정할 수 있습니다.

유사하게 gitHub 에서는 developer settings 에서 token 을 발급할 수 있는 기능을 제공하고 있습니다. 사용자는 id 와 password 대신에, id 와 이 token 으로 로그인을 하게 됩니다. 

token 발급 시, 이 토큰으로 로그인한 경우에 할 수 있는 동작을 설정할 수 잇어(권한 처리), 보안을 유지하고, 유지보수에 도움을 줍니다. 

<br/>

## 출처 및 참고자료

- <https://support.microsoft.com/en-us/office/pop-and-imap-email-settings-for-outlook-8361e398-8af4-4e97-b147-6c6c4ac95353>
- <https://pdi-mz-support.zendesk.com/hc/ko/articles/360050293051>