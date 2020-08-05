---
title: "이메일 설정 시 팁 모음"
excerpt: "Tips for setting email POP3 and 2FA authentication"
categories:
  - "etc"
tag:
  - "email"
  - "tips"
last_modified_at: 2020-08-05
---

> 이메일을 업무나 개인적으로 많이 사용하게 되면서 다음의 설정들을 많이 활용하였습니다. 메모 목적도 있으나, 찾는데 시간을 많이 소요하게 되는 부분이라 공유 차원에서 작성하는 글입니다. 

<br/>

## 여러 메일을 한 곳에서 받기

저는 지금 학교 메일(ac.kr), 지메일(gmail.com), 네이버 메일(naver.com) 등을 주로 사용하고 있습니다. 사실 gmail 이 주력이긴 한데, gmail 주소가 20자가 넘어가다 보니 간혹 입력되지 않는 웹페이지 등이 있어 네이버도 사용학 있습니다. 이런 경우 각각의 앱을 설치하거나 혹은 웹에서 확인해도 되지만, 저는 pop3 설정으로 gmail 에서 모든 메일을 확인하고 있습니다.

<br/>

#### IMAP or POP server settings

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

#### pop3 설정 시 2FA 를 설정중인 경우

만약, pop3로 메일을 가져오려는 계정이 이중인증 설정이 되어 있는 경우에는 id, password 를 이용한 로그인 방법으로는 로그인이 되지 않습니다. 이런 경우에는 다음과 같이 `앱 비밀번호` 라는 것을 설정하여 이를 통해서 로그인해야 합니다. 

여기서 이야기하는 `앱 비밀번호`라는 것은, 다른 플랫폼에서도 유사하게 사용됩니다. 예를 들어 GitHub 의 경우에도 2FA 를 설정한 경우에는 Git push 등의 명령을 하기 위해서는 이러한 형태의 별도의 비밀번호를 발급받아 사용해야 합니다. 
{: .notice--warning}

1. Google 계정으로 로그인 된 상태에서, `내 계정` - `보안` - `비밀번호 및 로그인 방식` - `앱 비밀번호` 로 이동합니다. 

2. 앱 비밀번호 항목에 본인이 원하는 항목을 입력한 뒤 생성 버튼을 눌러 생성합니다. 

3. 생성된 앱 비밀번호(**16자리**)를 복사하여 로그인 시에 id / password 대신 id / {16자리} 를 이용하여 로그인합니다.

이렇게 하면 보안은 유지하면서, pop3를 설정할 수 있습니다.

<br/>

## 참고

- https://support.microsoft.com/en-us/office/pop-and-imap-email-settings-for-outlook-8361e398-8af4-4e97-b147-6c6c4ac95353
- https://pdi-mz-support.zendesk.com/hc/ko/articles/115002387172--Gmail-2%EB%8B%A8%EA%B3%84-%EC%9D%B8%EC%A6%9D-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EC%95%B1-%EB%B9%84%EB%B0%80%EB%B2%88%ED%98%B8-%EC%83%9D%EC%84%B1%ED%95%98%EC%97%AC-%ED%83%80-%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8-%EB%B3%B4%EC%95%88%EC%88%98%EC%A4%80%EC%9D%B4-%EB%82%AE%EC%9D%80-%EC%95%B1-%EC%97%B0%EA%B2%B0%ED%95%98%EA%B8%B0