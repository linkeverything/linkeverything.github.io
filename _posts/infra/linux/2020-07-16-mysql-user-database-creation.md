---
title: "MySQL에 데이터베이스 및 사용자 추가하기"
categories: [linux]
tag: [mysql, create database, create user]
last_modified_at: 2020-07-16
---
MySQL(mariadb) 에서 데이터베이스 생성, 사용자 생성 방법에 대해서 자꾸 잊어버려서, 백업 목적으로 작성해 둡니다. 다양한 mysql client 프로그램(Heidi SQL, Sequel Pro 등)을 이용하여 데이터베이스나 사용자를 생성해도 되지만, 쿼리문을 활용하는 것이 더욱 직관적이고 군더더기 없이 생성되는 것 같아 가급적이면 아래처럼 명령어를 활용하는 편입니다. 

> 당연히, <mark style='background-color: #ffdce0'>root 사용자에 대한 접속 정보</mark>를 알아야 합니다. 더욱 정확하게는 데이터베이스 및 사용자를 추가할 수 있는 권한이 있는 사용자를 하나 알고 있어야 합니다. 

다음 명령어를 입력하여 데이터베이스에 접속합니다. root 권한(사용자 및 데이터베이스 생성권한)을 가진 사용자를 `-u` 옵션 뒤에 넣어줍니다.

```bash
mysql -u root -p
```

암호를 입력하라는 문구가 나타나면 암호를 입력하고 입력 프롬프트가 나타나는 것을 확인합니다.

```sh
root@3c0c733e2dc6:/# mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 11
Server version: 10.5.3-MariaDB-1:10.5.3+maria~focal mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

저는 docker 환경에서 접속했기 때문에 위 화면에서 `3c0c733e2dc6`와 같은 외계어인 Hostname 이 보입니다. 각자 설치된 환경에 맞는 내용이 보이면 됩니다.
{: .notice--info}

#### 데이터베이스 생성

데이터베이스 생성 Query 문은 `CREATE DATABASE` 입니다. 그 다음에 생성하고자 하는 데이터베이스의 이름을 넣어 다음과 같이 데이터베이스를 생성합니다. 

```sql
CREATE DATABASE {db_name};
```

잘 생성되었는지를 확인하려면 `show databases;`를 입력하면 됩니다.

```sql
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sample_database    |
| sample_database2   |
+--------------------+
5 rows in set (0.000 sec)

MariaDB [(none)]>
```

위 항목에서 보면 mariadb 의 경우에 기본적으로 생성되는 3개의 데이터베이스가 보입니다. 
- information_schema
- mysql
- performance_schema

이 세 가지는 mariadb 가 사용하는 기본 데이터베이스이므로 별도 작업을 하지 말고 그대로 두시면 됩니다. (root 권한을 가진 사용자로 로그인하였을 때에만 보여지는 데이터베이스입니다.)

#### 사용자 생성

다음 명령어로 사용자를 생성합니다.

```sql
mysql> CREATE USER '{user_name}'@'localhost' IDENTIFIED WITH mysql_native_password BY '{user_password}';
```

저는 docker 환경에서 최신 버전으로 구동했음에도 위 명령어가 동작하지 않아 아래 명령어로 성공하였습니다. 
{: .notice}

만약 위 명령어가 제대로 동작하지 않아 에러가 나거나, MySQL 5.1, 5.5, 5.6 버전 정도라면 다음 명령어를 입력합니다. 

```sql
mysql> CREATE USER '{user_name}'@'localhost' IDENTIFIED BY '{user_password}';
```

맨 마지막에 있는 `'localhost'` 부분은 해당 시스템 내에서만 접근을 허용하겠다는 의미입니다. 보안 등의 목적으로 저렇게 활용하곤 하지만, 일반적인 개발환경에서는 어디서나 접근 가능하게 하기 위해서 `'%'` 로 처리하였습니다. (이렇게 해도 id/password는 넣어야 하기 때문에 보안에 아주 취약한 것은 아닙니다.)
{: .notice--warning}

사용자 생성이 올바르게 되었는지를 확인하려면 `USER` 테이블을 조회하면 됩니다. 이 테이블은 앞서 보았던 `mysql` 데이터베이스에 존재하기 때문에 우선 `use` 명령어로 데이터베이스를 선택하고 사용자를 조회하면 됩니다.

```sql
MariaDB [(none)]> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> select user from user;
+-------------+
| User        |
+-------------+
| root        |
| sample      |
| mariadb.sys |
| root        |
+-------------+
4 rows in set (0.001 sec)

MariaDB [mysql]>
```

```sql
MariaDB [mysql]> select host, user from user;
+-----------+-------------+
| Host      | User        |
+-----------+-------------+
| %         | root        |
| %         | sample      |
| localhost | mariadb.sys |
| localhost | root        |
+-----------+-------------+
4 rows in set (0.001 sec)

MariaDB [mysql]>
```

#### 사용자 권한 설정

이렇게 생성한 사용자는 아무런 권한이 없습니다. 단순히 `SELECT` 명령 조차 실행할 수 없습니다. 따라서 아래 명령어로 권한을 줍니다. 

```sql
mysql> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, INDEX, DROP, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES ON {db_name}.* TO '{user_name}'@'localhost';
```

만약 `FILE`권한을 주고 싶다면 다음 명령어를 추가합니다. (성능적인 이득이 있다고 하는데 정확한 부분은 모르겠습니다.)

```sql
mysql> GRANT FILE ON {db_name}.* TO '{user_name}'@'localhost';
```

사용자에게 권한이 올바르게 들어갔는지 확인하려면 `SHOW GRANTS` 를 이용하면 됩니다. 사용자 이름과 host 정보를 이용하여 함께 조회하면 됩니다. 

```sql
MariaDB [mysql]> show grants for 'sample'@'%';
+------------------------------------------------------------------------------------------------------------------------------------------------+
| Grants for sample@%                                                                                                                            |
+------------------------------------------------------------------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO `sample`@`%` IDENTIFIED BY PASSWORD '*E6CEC7E0A7E36A110423A463C6A13FAD97798442'                                          |
| GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES ON `sample_database2`.* TO `sample`@`%` |
| GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES ON `sample_database`.* TO `sample`@`%`  |
+------------------------------------------------------------------------------------------------------------------------------------------------+
3 rows in set (0.000 sec)

MariaDB [mysql]>
```

잘 기억하게 되지도 않지만, 버전이 올라갈 수록 조금씩 변경되기도 하니 테이블 등을 잘 조회해 가면서 적용해 보면 됩니다.

## 출처 및 참고자료

- <https://matomo.org/faq/how-to-install/faq_23484/>