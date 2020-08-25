---
title: "여러 파일 grep 하기"
excerpt: "How to grep multiple files"
categories:
  - "infra"
  - "linux"
tag:
  - "grep"
  - "files"
last_modified_at: 2020-08-05
---

> 개발을 하다 보면 특정 문자열이 포함된 파일을 찾게 되는 경우가 더러 있습니다. 개발했던 파일들 - 예를 들어 `.java`, `.properties`, `.xml`, `.json` 등 - 은 결국 다 text 파일들이고, 따라서 문자열 검색을 할 수 있습니다. class 파일이나 pptx 파일 등과 같이 텍스트 형태가 아닌 컴파일된 파일들은 문자열 검색이 용이 하지 않으나, 개발 대상 파일들은 문자열 검색이 가능하고 편리합니다.

<br/>

## 언제 사용할까?

프로젝트 전체에 대해서 common 이라는 문구를 모두 다 cmn 으로 교체한다고 가정하겠습니다. 프로젝트에서 명명규칙이 변경되었기 때문인데, 파일을 하나하나 찾기 보다는 IDE의 막강한 기능을 이용해서 <mark style='background-color: #ffdce0'>찾아 바꾸기</mark>할 것입니다. 

개발 프로젝트에서는 이런 부분이 가능한데, 설정 파일인 xml, properties 파일들도 수정향 한다고 생각해 봅니다. 물론 로컬에서 모두 변경하여 서버에 업로드 하는 것도 가능하지만, 서버에 있는 설정 파일에서 우선 변경작업을 한다고 하면 FTP 로 받아서 수정하고 다시 올려줘야 하는 번거로움이 있습니다. 

이럴 때에는 서버에서 바로 수정하고, 로컬 개발환경에서는 별도로 작업해줘도 무방할 것입니다. 

특정 내용이 있는 파일을 찾기 위해서, 특히 로그 파일 등을 찾아보는 경우에 <mark style='background-color: #fff5b1'>grep</mark>을 사용하는 것이 일반적입니다. 다음의 옵션값들을 이용하여 검색이 가능합니다.

- `H` 파일명 포함하여 출력하기

  폴더나 프로젝트 전체에 대해서 검색을 하는 경우에 어떤 파일에 어떤 내용이 남아있는지를 찾아야 하는 경우가 있습니다. 예를 들어, `"debug"`라는 문자열로 검색을 하는데 `"dev"` 프로파일로 구동되는 경우에는 맞게 설정되어 잇는 것이므로 `application-dev.properties` 에는 설정으로 남아 있어도 무방합니다. 그런데 운영 설정값인 `application.properties`파일에는 남아있으면 안되겠죠. 이런 경우에 파일명도 함께 출력하여 더욱 정확한 검색이 가능합니다.

- `n` 라인 넘버 포함하기

  검색한 결과가 어디에 포함되어 있는 것인지 정확하게 알기 위함입니다. 아무래도 같은 내용이 반복되는 파일(properties등 설정 파일)인 경우에는 라인 번호가 유용하게 활용될 수 있습니다.

- `i` 대소문자 구분 없이 찾기

  대소문자를 구분하느냐 구분하지 않느냐는 검색하는 대상에 따라 다릅니다. 앞선 예시와 같이 프로젝트 내부적으로 용어사전에서 common 을 모두 cmn 으로 처리하기로 결정했다면 DefaultCommonController 같은 class 명 역시 검색하여 변경해주는 것이 맞습니다(카멜, camel표기법). 각 상황에 맞게 대소문자를 구분할지여부를 결정해주면 됩니다. 


<br/>

## grep 이용하기

grep 자체가 명령어이고 대상 폴더를 줄 수 있기 때문에 이를 활용하면 됩니다. 이 검색기능을 알기 전 까지는 grep 자체만으로 사용하지는 않았고, `ps -ef | grep java` 처럼 다른 명령어의 출력문에 대해서 한정적으로 검색하는 용도였습니다. 하지만 당연하게도 단독 명령어로 사용이 가능하며, 앞서 설명한 옵션을 활용하면 원하는 결과를 얻을 수 있습니다.

```sh
grep -Hni Error ./server*
```

- 파일명 포함하여 출력하기, 라인 넘버 포함하기, 대소문자 구분 없이 찾기
- "Error" 라는 문구 찾기
- 현재 위치 하위에 "server" 로 시작하는 파일에 대해 검색하기

그 결과는 아래와 같습니다. 대소문자 구분없이 "error"라는 문자열이 포함된 부분이 검색되었음을 알 수 있습니다. 

```sh
...
./server.log.2020-08-04:2058:2020-08-04 18:44:39,113 WARN  [org.keycloak.events] (default task-144) type=LOGIN_ERROR, realmId=DOTS, clientId=ontology_client, userId=null, ipAddress=10.14.21.222, error=user_not_found, auth_method=openid-connect, auth_type=code, redirect_uri=http://localhost:8080/, code_id=e0359358-c963-4536-b137-e3e159b196c4, authSessionParentId=e0359358-c963-4536-b137-e3e159b196c4, authSessionTabId=OHoZCuEYTXo
./server.log.2020-08-04:2059:2020-08-04 18:44:41,018 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-144) Received user event of type LOGIN_ERROR in realm DOTS
./server.log.2020-08-04:2060:2020-08-04 18:44:41,018 WARN  [org.keycloak.events] (default task-144) type=LOGIN_ERROR, realmId=DOTS, clientId=ontology_client, userId=null, ipAddress=10.14.21.222, error=user_not_found, auth_method=openid-connect, auth_type=code, redirect_uri=http://localhost:8080/, code_id=e0359358-c963-4536-b137-e3e159b196c4, authSessionParentId=e0359358-c963-4536-b137-e3e159b196c4, authSessionTabId=OHoZCuEYTXo
...
```

이 결과에서 보면 결과는 다음과 같은 형태로 <mark style='background-color: #dcffe4'>콜론(:)</mark>으로 구분되어 있습니다. 

```
파일명:라인번호:검색된문장
```

<br/>

## xargs + grep 활용하기

조금 다른 방법으로, `xargs`를 함께 사용하는 방법이 있습니다. xargs 는 앞선 명령에 대한 결과를 가지고 그 다음 명령을 실행하는 것이라고 볼 수 있습니다. 

```sh
ls -al ./server* | awk '{print $NF}' | xargs grep -Hni Error
```

- 현재 폴더 하위에 있는 "server" 로 시작하는 모든 것들을 list-up 합니다.
- 그 결과에 대해서 이름 부분만 뽑아 출력합니다. 
- 그 결과 <mark style='background-color: #ffdce0'>각각에 대해서</mark> grep 명령어를 수행한다.

여기서 xargs 가 포함된 마지막 명령 부분이 의미하는 것이 중요합니다. xargs는 <mark style='background-color: #fff5b1'>앞선 명령의 결과를 이 명령의 인자로 활용</mark> 하겠다는 의미로 보아야 합니다. 다라서 앞서 나온 결과 파일명이 리스트 형태로 출력될 것이고, 이 각각의 이름을 `grep` 으로 시작하는 명령의 인자로 넣어주게 되는 것입니다. 

**2020. 08. 25. 추가**
만약, xargs 로 인자의 위치를 맨 뒤가 아닌 명령의 중간에 들어가야 한다면 `-I` 옵션을 활용해야 합니다. 다음 예제로 보겠습니다. 

```sh
xargs -I{} cp {} ./bak_files
```

위에서 보면 앞선 인자를 받아와서 넣어주긴 할 것인데, <mark style='background-color: #ffdce0'>-I 뒤에 있는 `{}`이런 부분에 넣어줄 것이라고 선언</mark>하는 것입니다. 따라서 cp 명령 다음에 xargs 로 받아온 인자가 들어가게 되고, 그 것을 ./bak_files 라는 폴더로 복사를 하게 됩니다. 

과거에는 `-i` 로 소문자로 옵션을 넣고 기본 대체제인 `{}` 를 원하는 위치에 넣었었지만, 현재는 없어질 옵션이고, 사용을 지양한다고 합니다.

명령의 결과는 다음과 같습니다. 앞서 grep 만 사용한 결과와 동일합니다.

```sh
...
./server.log.2020-08-04:2059:2020-08-04 18:44:41,018 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-144) Received user event of type LOGIN_ERROR in realm DOTS
./server.log.2020-08-04:2060:2020-08-04 18:44:41,018 WARN  [org.keycloak.events] (default task-144) type=LOGIN_ERROR, realmId=DOTS, clientId=ontology_client, userId=null, ipAddress=10.14.21.222, error=user_not_found, auth_method=openid-connect, auth_type=code, redirect_uri=http://localhost:8080/, code_id=e0359358-c963-4536-b137-e3e159b196c4, authSessionParentId=e0359358-c963-4536-b137-e3e159b196c4, authSessionTabId=OHoZCuEYTXo
./server.log.2020-08-04:2159:2020-08-04 20:07:26,528 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-144) Received user event of type LOGIN_ERROR in realm DOTS
./server.log.2020-08-04:2160:2020-08-04 20:07:26,528 WARN  [org.keycloak.events] (default task-144) type=LOGIN_ERROR, realmId=DOTS, clientId=null, userId=null, ipAddress=10.14.21.222, error=invalid_request
...
```

<br/>

## find + grep 활용하기

다음으로는 `find`명령어를 사용하는 방법입니다. 앞선 방법들과 유사하지만 적어놓고 보면 옵션명들이 명확하긴 합니다. 

```sh
find ./ -maxdepth 1 -type f -name 'server*' -exec grep -Hni Error {} \;
```

명령어를 분석해 보면,
- 현재 위치에서 찾을 것이고,
- 최대로 하위로 내려가는 depth 값는 1이며 (현재 폴더에서만 검색)
- 형태는 `f` 즉, 파일형태만 찾을 것이며,
- 이름은 "server" 로 시작하고
- 그 결과를 {} 위치에 넣으면서 grep -Hni Error {} 명령어를 실행하시오

라고 분석할 수 있습니다.

```sh
...
./server.log.2020-06-16:1877:2020-06-16 19:02:44,682 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-2211) Received user event of type REFRESH_TOKEN_ERROR in realm DOTS
./server.log.2020-06-16:1878:2020-06-16 19:02:44,682 WARN  [org.keycloak.events] (default task-2211) type=REFRESH_TOKEN_ERROR, realmId=DOTS, clientId=dots_client, userId=null, ipAddress=10.14.11.103, error=invalid_token, grant_type=refresh_token, client_auth_method=client-secret
./server.log.2020-06-16:1975:2020-06-16 19:46:28,505 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-2203) Received user event of type REFRESH_TOKEN_ERROR in realm DOTS
...
```

다양한 방법, 더 많은 방법이 있을 것이지만, 본인의 개발 방법 등에 걸맞는 방법을 찾아두고 진행하는 것을 권장합니다. 개인적으로는 grep 만 사용하는 것이 가장 편합니다^^

<br/>

## 참고자료 및 출처

- https://zetawiki.com/wiki/여러_파일_grep_하기_+_파일명_보기