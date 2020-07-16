---
title: "MySQL에 데이터베이스 및 사용자 추가하기"
excerpt: "How to create database and user on MySQL"
categories:
  - "dev"
  - "infra"
tag:
  - "mysql"
  - "create database"
  - "create user"
last_modified_at: 2020-07-16
---

MySQL(mariadb) 에서 데이터베이스 생성, 사용자 생성 방법에 대해서 자꾸 잊어버려서, 백업 목적으로 작성해 둡니다. 다양한 mysql client 프로그램(Heidi SQL, Sequel Pro 등)을 이용하여 데이터베이스나 사용자를 생성해도 되지만, 쿼리문을 활용하는 것이 더욱 직관적이고 군더더기 없이 생성되는 것 같아 가급적이면 아래처럼 명령어를 활용하는 편입니다. 

> 당연히, root 사용자에 대한 접속 정보를 알아야 합니다. 더욱 정확하게는 데이터베이스 및 사용자를 추가할 수 있는 권한이 있는 사용자를 하나 알고 있어야 합니다. 

데이터베이스에 접속합니다. 

```bash
mysql -u root -p
```

#### 데이터베이스 생성

다음 명령어로 데이터베이스를 생성합니다. 

```sql
CREATE DATABASE {db_name};
```

#### 사용자 생성

다음 명령어로 사용자를 생성합니다.

```sql
mysql> CREATE USER '{user_name}'@'localhost' IDENTIFIED WITH mysql_native_password BY '{user_password}';
```

만약 위 명령어가 제대로 동작하지 않아 에러가 나거나, MySQL 5.1, 5.5, 5.6 버전 정도라면 다음 명령어를 입력합니다. 

```sql
mysql> CREATE USER '{user_name}'@'localhost' IDENTIFIED BY '{user_password}';
```

> 저는 docker 환경에서 최신 버전으로 구동했음에도 위 명령어가 동작하지 않아 아래 명령어로 성공하였습니다. 

> 또한 맨 마지막에 있는 `'localhost'` 부분은 해당 시스템 내에서만 접근을 허용하겠다는 의미입니다. 보안 등의 목적으로 저렇게 활용하곤 하지만, 일반적인 개발환경에서는 어디서나 접근 가능하게 하기 위해서 `'%'` 로 처리하였습니다. (이렇게 해도 id/password는 넣어야 하기 때문에 보안에 아주 취약한 것은 아닙니다.)

#### 사용자 권한 설정

이렇게 생성한 사용자는 아무런 권한이 없습니다. 단순히 `SELECT` 명령 조차 실행할 수 없습니다. 따라서 아래 명령어로 권한을 줍니다. 

```sql
mysql> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, INDEX, DROP, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES ON {db_name}.* TO '{user_name}'@'localhost';

```

만약 `FILE`권한을 주고 싶다면 다음 명령어를 추가합니다. (성능적인 이득이 있다고 하는데 정확한 부분은 모르겠습니다.)

```sql
mysql> GRANT FILE ON {db_name}.* TO '{user_name}'@'localhost';
```

#### 참고자료 및 출처

https://matomo.org/faq/how-to-install/faq_23484/