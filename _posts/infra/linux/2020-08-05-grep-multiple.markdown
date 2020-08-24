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

> 특정 내용이 있는 파일을 찾기 위해서, 특히 로그 파일 등을 찾아보는 경우에 grep 을 사용하게 됩니다. 아래 방법으로 조금 더 자세한 내용을 파악할 수 있습니다.

- `H` 파일명 포함하여 출력하기

- `n` 라인 넘버 포함하기

- `i` 대소문자 구분 없이 찾기

<br/>

#### grep

```sh
grep -Hni Error ./server*
```

```sh
...
./server.log.2020-08-04:2058:2020-08-04 18:44:39,113 WARN  [org.keycloak.events] (default task-144) type=LOGIN_ERROR, realmId=DOTS, clientId=ontology_client, userId=null, ipAddress=10.14.21.222, error=user_not_found, auth_method=openid-connect, auth_type=code, redirect_uri=http://localhost:8080/, code_id=e0359358-c963-4536-b137-e3e159b196c4, authSessionParentId=e0359358-c963-4536-b137-e3e159b196c4, authSessionTabId=OHoZCuEYTXo
./server.log.2020-08-04:2059:2020-08-04 18:44:41,018 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-144) Received user event of type LOGIN_ERROR in realm DOTS
./server.log.2020-08-04:2060:2020-08-04 18:44:41,018 WARN  [org.keycloak.events] (default task-144) type=LOGIN_ERROR, realmId=DOTS, clientId=ontology_client, userId=null, ipAddress=10.14.21.222, error=user_not_found, auth_method=openid-connect, auth_type=code, redirect_uri=http://localhost:8080/, code_id=e0359358-c963-4536-b137-e3e159b196c4, authSessionParentId=e0359358-c963-4536-b137-e3e159b196c4, authSessionTabId=OHoZCuEYTXo
...
```

<br/>

#### xargs + grep 

```sh
ls -al ./server* | awk '{print $NF}' | xargs grep -Hni Error
```

```sh
...
./server.log.2020-08-04:2059:2020-08-04 18:44:41,018 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-144) Received user event of type LOGIN_ERROR in realm DOTS
./server.log.2020-08-04:2060:2020-08-04 18:44:41,018 WARN  [org.keycloak.events] (default task-144) type=LOGIN_ERROR, realmId=DOTS, clientId=ontology_client, userId=null, ipAddress=10.14.21.222, error=user_not_found, auth_method=openid-connect, auth_type=code, redirect_uri=http://localhost:8080/, code_id=e0359358-c963-4536-b137-e3e159b196c4, authSessionParentId=e0359358-c963-4536-b137-e3e159b196c4, authSessionTabId=OHoZCuEYTXo
./server.log.2020-08-04:2159:2020-08-04 20:07:26,528 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-144) Received user event of type LOGIN_ERROR in realm DOTS
./server.log.2020-08-04:2160:2020-08-04 20:07:26,528 WARN  [org.keycloak.events] (default task-144) type=LOGIN_ERROR, realmId=DOTS, clientId=null, userId=null, ipAddress=10.14.21.222, error=invalid_request
...
```

<br/>

#### find + grep

```sh
find ./ -maxdepth 1 -type f -name 'server*' -exec grep -Hni Error {} \;
```

```sh
...
./server.log.2020-06-16:1877:2020-06-16 19:02:44,682 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-2211) Received user event of type REFRESH_TOKEN_ERROR in realm DOTS
./server.log.2020-06-16:1878:2020-06-16 19:02:44,682 WARN  [org.keycloak.events] (default task-2211) type=REFRESH_TOKEN_ERROR, realmId=DOTS, clientId=dots_client, userId=null, ipAddress=10.14.11.103, error=invalid_token, grant_type=refresh_token, client_auth_method=client-secret
./server.log.2020-06-16:1975:2020-06-16 19:46:28,505 INFO  [org.jboss.aerogear.keycloak.metrics.MetricsEventListener] (default task-2203) Received user event of type REFRESH_TOKEN_ERROR in realm DOTS
...
```

<br/>

## 참고자료 및 출처

- https://zetawiki.com/wiki/여러_파일_grep_하기_+_파일명_보기