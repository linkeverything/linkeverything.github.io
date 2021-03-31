---
title: "Springboot에 datasource 사용하기 (jpa내용도 함께)"
categories: [study, springboot]
tag: [datasource]
last_modified_at: 2021-03-03
---

서버 측 개발 목적에 Datasource가 빠질 수 없습니다. 일반적으로 _디비_ 라고 부르면서 어떤 데이터들을 데이터베이스에 저장하는 것을 말하는데, 최근에는 IT분야에 대한 충분한 지식이 없어도 이런 이야기들을 많이 하는 것 같습니다. 

이 글에서는 가장 기본적인 형태로 Database, 즉 DB를 사용하는 방법을 살펴보고 더욱 확장된 개념에 대해서는 추후에 다른 포스팅에서 이어가도록 하겠습니다.

[https://github.com/linkeverything/studySpringBoot/tree/0030-datasource](https://github.com/linkeverything/studySpringBoot/tree/0030-datasource){:target="_blank" .btn .btn--primary}

> 위 경로에서 다운로드 받아서 진행하는 경우, branch 를 반드시 확인하시고 다운로드(checkout) 받으시기 바랍니다. 각 게시글에 맞는 branch 로 생성되어 있으므로 해당 branch를 받는 것이 이해해 도움이 됩니다.

database 와 프로그램인 springboot 를 연동하여 사용하는 것은, jdbc 드라이버라고 부르는 database에 접속하는 모듈과 함께, 그 모듈을 통해서 query 를 보내고 결과를 받아, java 객체에 담아주는 ORM(Object-Relational Mapping) 기능을 갖춘 jpa 에 대한 이야기를 함께 해야 합니다. 아래 그 내용까지 포함하여 기술합니다.

## Datasource

이 글을 테스트, 학습하기 위해서는 Database를 하나 설치해야 합니다. Mac 이나 Windows나 동일하게 진행하면 됩니다. 서버가 있고, 해당 서버에 대한 접근이 가능한 상황이라면 이 파트는 넘어가도 됩니다. 저는 가장 간단하게 [mariadb](https://www.heidisql.com/)를 이용할 예정이고, db client 로는 [Sequel Pro](https://www.sequelpro.com/) 라는 프로그램을 사용할 예정입니다. 

- mariadb 다운로드 페이지: <https://www.heidisql.com/>
- sequal pro 다운로드 페이지: <https://www.sequelpro.com/>
- heidi sql 다운로드 페이지: <https://www.heidisql.com/>

윈도우 사용자의 경우에는 HeidiSQL이라는 프로그램을 사용하면 간단히 사용하기 좋습니다. Sequel Pro 는 윈도우 버전이 없는 것으로 알고 있습니다. 
{: .notice--info}

#### Database 설치

Database는 다양한 형태로 설치가 가능하지만, 일반적인 인스톨러 형태로 설치하는 방법과 docker 로 설치하는 방법이 있습니다. 인스톨러 형태로 설치하는 것은 설명이 필요없을 수준으로 간단하기 때문에 이 글에서 추가로 설명하지는 않겠습니다. 저는 docker를 주로 사용하고 있고, 따라서 mariadb 역시 docker를 이용하여 구동합니다. 

docker 에서 mariadb 를 실행하는 것은 매우 간단하지만, yaml 파일의 사용, 그것을 통한 향후 kubernetes에서의 이용에까지 활용하기 위해서 docker-compose를 활용합니다.[^1] 아래에 docker-compose.yml 파일을 공유합니다.

**.env 파일**

```properties
ONTAINER_NAME_1=maria_db_1
MARIADB_ROOT_PASSWORD=?????
```

**docker-compose.yml 파일**

```yml
version: '3.1'
services:
  maria_db_1:
    image: mariadb:10.5.3
    container_name: ${CONTAINER_NAME_1}
    restart: always
    ports:
      - "53306:3306"
    volumes:
      - ${PWD}/${CONTAINER_NAME_1}/volumes/etc/mysql/conf.d:/etc/mysql/conf.d:ro
      - ${PWD}/${CONTAINER_NAME_1}/volumes/var/lib/mysql:/var/lib/mysql
      - ${PWD}/${CONTAINER_NAME_1}/volumes/var/log/maria:/var/log/maria
    environment:
      - MYSQL_ROOT_PASSWORD=${MARIADB_ROOT_PASSWORD}
      - TZ="Asia/Seoul"
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

위와 같이 파일을 저장한 뒤, 아래 명령어를 이용하여 실행해 줍니다.

```sh
docker-compose up -d
```

#### 접속, 테이블 생성하기

이제 예제를 확인해보기 위해서 database에 접속하고, 테이블을 하나 생성하겠습니다. 단순이 예제 목적이기 때문에 심플하게 생성하겠습니다. 

우선 앞서 다운로드 받고 실행해 두었던 docker 형태의 mariadb 에 접속해 보겠습니다. 아래 그림처럼 해당 기기에 대한 정보들을 넣고 접속해 봅니다. 초기 id는  `root` 이고, 패스워드는 위 docker-compose.yml 파일에 설정한 것이 됩니다. 물론 여기서는 .env 파일을 두고 그 안에 환경변수로 설정하였습니다.

![](/assets/images/posts/study/springboot/2021-03-03-datasource/screenshot 2021-03-05 오후 5.32.40.png)

우선 위에 언급한 client 프로그램들을 이용하여 database에 접속합니다. mariadb 와 mysql 은 거의 유사하게 생각하도 되기 때문에, mysql workbench 라는 프로그램을 이용해도 됩니다. (mariadb 는 mysql을 개량하여 만든 형태라고 봐도 무방합니다.)

접속을 하고 나면 아래 명령어를 통해 database를 생성하겠습니다. 이미 만들어져 있는 database에 접속하려면 이 과정은 하지 않아도 됩니다. 이 과정은 `root`계정으로 실행하여야 합니다.

```sql
create database sample_database;
```

다음으로는 아래 명령어를 입력하여 사용자를 하나 생성합니다.

```sql
CREATE USER '{user_name}'@'localhost' IDENTIFIED BY '{user_password}';
```

```sql
CREATE USER 'sample'@'%' IDENTIFIED BY 'sample';
```

사용자 암호는 노출되지 않도록 주의합니다. sample 이라는 이름으로 사용자를 생성하는데, 위와 같이 `%` 를 주는 것은 <mark style='background-color: #ffdce0'>이 계정으로 접속하는 위치에 제한을 두지 않고, 모든 경로에서의 접근을 다 허용한다는 의미</mark>입니다. 다음과 같이 기본 데이터베이스 중 mysql 을 선택하고 user 테이블에서 user를 검색하면 다음과 같이 확인할 수 있습니다.

```sql
MariaDB [mysql]> use mysql;
Database changed
MariaDB [mysql]> select host, user from user;
+-----------+-------------+
| Host      | User        |
+-----------+-------------+
| %         | root        |
| %         | sample      |
| localhost | mariadb.sys |
| localhost | root        |
+-----------+-------------+
4 rows in set (0.002 sec)

MariaDB [mysql]>
```

이제 데이터베이스(sample_database)와 사용자(sample)를 생성하였으므로, 그 사용자에게 데이터베이스에 대한 권한을 부여합니다.

```sql
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, INDEX, DROP, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES ON {db_name}.* TO '{user_name}'@'{host}';
```

```sql
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, INDEX, DROP, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES ON sample_database.* TO 'sample'@'%';
```

```sql
MariaDB [mysql]> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, INDEX, DROP, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES ON sample_database.* TO 'sample'@'%';
Query OK, 0 rows affected (0.004 sec)

MariaDB [mysql]>
```

이렇게 되면 

1. 데이터베이스 생성
2. 사용자 생성
3. 1, 2에서 각각 생성한 것 연결 및 권한 처리

가 완료된 것입니다.

위 과정은 제 블로그 내의, [MySQL에 데이터베이스 및 사용자 추가하기](https://linkeverything.github.io/linux/mysql-user-database-creation/) 글에서 확인 가능합니다.
{: .notice--info}

#### mysql의 데이터베이스 선택

mysql 이나 mariadb를 사용할 때에 데이터베이스를 `사용할거다` 라고 명시해 줍니다. 아래 처럼 특정 database를 선택하지 않으면 선택되지 않았다 라고 에러를 내보냅니다.

```sql
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sample_database    |
+--------------------+
4 rows in set (0.001 sec)

MariaDB [(none)]> show tables;
ERROR 1046 (3D000): No database selected
MariaDB [(none)]> use sample_database;
Database changed
MariaDB [sample_database]>
```

앞서 이야기한 sequel pro와 같은 프로그램을 사용하면 명령어 대신에 database를 선택함으로써 저 과정을 대체할 수 있습니다.

![](/assets/images/posts/study/springboot/2021-03-03-datasource/capture 2021-03-08 PM 1.53.18.png)

가장 간단한 테이블을 하나 생성합니다.

```sql
CREATE TABLE IF NOT EXISTS SAMPLE_TABLE ( 
	SAMPLE_NO BIGINT NOT NULL AUTO_INCREMENT, 
	NAME VARCHAR(200), 
	PRIMARY KEY(SAMPLE_NO)
);
```

sample_table이라는 이름으로 테이블을 생성하고 id라는 숫자 형태의 primary key를, name 이라는 컬럼을 문자열 10글자로 생성하였습니다.

추가로, 데이터도 하나 넣어 두겠습니다. 뒤에서 Springboot 를 이용하여 데이터를 넣는 것을 구현하겠지만, 조회를 우선 구현할 것이기 때문에 우선 데이터를 하나만 넣겠습니다. 두 개의 컬럼을 생성하면서 하나는 숫자 타입으로, 다른 하나는 문자 타입으로 생성하였기 때문에 아래와 같은 Query 를 이용하여 데이터를 넣을 수 있습니다.

```sql
INSERT INTO SAMPLE_TABLE VALUES (1, "sample name");
```

## SpringBoot 에서 연결하고 사용하기

이제 드디어, SpringBoot 에서 접속하고 데이터를 가져오는 것을 연습해 보겠습니다.

편의상 업무적인 흐름을 무시하고, api 2개를 생성하여 하나는 데이터 삽입을, 다른하나는 데이터 조회를 하도록 하겠습니다. 

#### build.gradle에 dependency 추가

SpringBoot에서 mysql(mariadb)에 `접속` 하기 위해서는 접속할 수 있는 도구로서 <mark style='background-color: #ffdce0'>드라이버</mark> 가 필요합니다. 또한 여기서 추가로 설명하게 될 <mark style='background-color: #ffdce0'>JPA</mark> 기술을 사용하기 위한 라이브러리가 필요하고, 이를 더욱 편리하게 사용하고자 <mark style='background-color: #ffdce0'>lombok</mark> 라이브러리를 포함하려고 합니다.

build.gradle 파일을 열어, 아래 내용을 <mark style='background-color: #fff5b1'>dependencies {}</mark> 안에 추가해 줍니다.

```gradle
	compile 'org.mariadb.jdbc:mariadb-java-client:2.7.2'
	compile 'org.springframework.boot:spring-boot-starter-data-jpa'
	compile 'org.projectlombok:lombok'
```

`compile` 로 선언된 것에 대한 설명은 추후 gradle에 대한 학습이 조금 진행되면 그때 별도의 포스트로 정리하겠습니다.
{: .notice--warning}

- compile 'org.mariadb.jdbc:mariadb-java-client:2.7.2'

  java 프로그램에서 mariadb 에 접속하기 위한 client 라이브러리 입니다.

- compile 'org.springframework.boot:spring-boot-starter-data-jpa'

  JPA 기능을 사용하기 위한 라이브러리 입니다.

- compile 'org.projectlombok:lombok'

  lombok 기능을 이용하기 위한 라이브러리입니다. lombok에 대한 상세한 설명은 향후 advanced 부분에서 더욱 자세하게 다뤄 볼 예정입니다. 

#### entity 생성

Table 에서 JPA 기능을 이용하여 데이터를 가져오려면, Table의 하나하나의 행에 해당하는, 즉 데이터 하나하나에 해당하는 객체를 정의해야 합니다. 단순히 쿼리문을 이용해서 가져오는 것이 아니라, 저 객체를 이용하여 <mark style='background-color: #ffdce0'>자동으로 SELECT 문을 구성</mark>하고, 데이터를 가져오게 됩니다. 

앞서 만든 Table의 Column은 숫자 타입하나와 문자 타입 하나로 이루어져 있습니다. 따라서 아래와 같은 형태로 Entity를 생성해 줍니다.

```java
package com.simplify.studySpringBoot.entity;

import lombok.*;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Data
@AllArgsConstructor
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Entity(name = "SAMPLE_TABLE")
public class Sample {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long sampleNo;

    private String name;

    @Builder
    public Sample(String name) {
        this.name = name;
    }

}
```

- @Id, @GeneratedValue(..) : 일반적으로 Table 생성 시, Primary key, Autoincrement 로 설정한 값을 지정합니다. 데이터를 넣을 때, 자동 생성되는 값으로서, 실제 db 에는 Notnull & primary key 로 되어 있어도 이 annotaion 으로 인해 자동으로 생성하고 넣어줍니다. db와 쌍으로 맞지 않으면 제대로 실행되지 않습니다. 
- strategy = GenerationType.IDENTITY : unique 값을 지정하기 위한 것으로 지정합니다. 
- private Long sampleNo; : table에서 지정한 컬럼 명을 camel 표기법으로 변경하여 변수 명을 생성합니다. 일반적으로 숫자의 경우에는 Long 타입으로 생성하고, <mark style='background-color: #dcffe4'>long 이 아닌 Long 을 써야 함</mark>에 주의합니다. 
- @Entity(name = "SAMPLE_TABLE") : entity 입을 명시하면서 대상이 되는 테이블의 이름을 적어줍니다. 변수명이 컬럼에 mapping 되는 것은 camel 표기법에 의해 강제로 되지만, table 명은 클래스 명을 따라가기 보다는 이처럼 entity의 name 값으로 지정해주는 것이 좋습니다. 
- @Builder : 이 부분은 lombok 을 더 상세하게 설명할 때 적겠지만, 데이터를 삽입하고자 할 때에 `new` 키워드를 사용하지 않고 이처럼 builder 를 이용하여 구현하게 됩니다.

#### JpaRepository 생성

이제 위에서 생성한 entity 를 이용하여 database와 연결시켜주는 `repository` 인터페이스를 생성하겠습니다. 더욱 깊이있게 들어가면 복잡하지만 여기서는 단순히 조회와 삽입을 할 것이므로, <mark style='background-color: #ffdce0'>JpaRepository를 상속받는 repository 를 하나 생성</mark>하겟습니다.

```java
package com.simplify.studySpringBoot.repository;

import com.simplify.studySpringBoot.entity.Sample;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface SampleRepository extends JpaRepository<Sample, Long> {

}
```

Repository 를 생성하는 것이므로, `@Repository` annotation을 붙여주고, `org.springframework.data.jpa.repository.JpaRepository` 를 상속받는 인터페이스를 생성합니다. 여기서 주의해야 할 점은 <mark style='background-color: #fff5b1'>JpaRepository 뒤에 사용하고자 하는 entity 를 넣고, 그 다음에는 key가 되는 값의 형태, 즉 여기서는 @Id 로 선언했던 값의 타입</mark>을 넣어줍니다. 

상속받은 JpaRepository의 합수들을 override 하지 않아도 기본적인 조회와 삽입은 동작하기 때문에 여기서는 아무것도 override하지 않았습니다.

#### Service 생성

이제 conroller에서 사용할, repository 에 동작을 요청할 서비스를 생성합니다. 기본적인 구조에 충실하여 설명하기 위해서 별도의 동작이 없음에도 controller - service - repository 의 구조를 유지하려고 합니다. 

```java
package com.simplify.studySpringBoot.service;

import com.simplify.studySpringBoot.entity.Sample;
import com.simplify.studySpringBoot.repository.SampleRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
@RequiredArgsConstructor
public class SampleService {

    private final SampleRepository sampleRepository;

    public List<Sample> getSamples() {
        return sampleRepository.findAll();
    }
}
```

- @Service : 서비스 임을 명시적으로 넣어줍니다. Component 처럼 기본적으로 bean을 생성해 줍니다. 
- @RequiredArgsConstructor : 과거 `@Autowired` annotation을 사용했던 것과는 다르게 여기서는 `@RequiredArgsConstructor` 를 사용합니다. 이렇게 하면 실제 멤버 변수로 들어가는 repository 객체는 <mark style='background-color: #dcffe4'>private final</mark> 로 설정이 가능합니다. 여러 주입 방식에 대해서 최근에는 이렇게 생성자 주입 방식을 사용하는 추세입니다.

repository 에 `findAll()` 을 호출하여 값을 조회합니다. SELECT * FROM TABLE 을 구현해둔 형태라고 보면 됩니다.

#### Controller 에 API 생성

이제, 외부에 API 를 노출하고, 위에서 생성한 service에 호출할 controller를 구현해 줍니다. 

```java
package com.simplify.studySpringBoot.controller;

import com.simplify.studySpringBoot.entity.Sample;
import com.simplify.studySpringBoot.service.SampleService;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
@RequiredArgsConstructor
public class SampleController {

    private final SampleService sampleService;

    @GetMapping(value = "/samples")
    public List<Sample> getSamples(){
        return sampleService.getSamples();
    }
}
```

여기서도 생성자 주입 방식을 이용해서 sampleService 객체를 멤버로 가지고 있게 하였고, `/samples` 를 호출했을 때, 값을 조회하도록 구성하였습니다. 

#### 결과 확인

이제 bootRun 을 실행하여 웹 브라우저에 `http://localhost:8080/samples` 를 호출하면, 아래와 같이 데이터가 JSON 형태로 넘어오는 것을 확인할 수 있습니다. 

![](/assets/images/posts/study/springboot/2021-03-03-datasource/screenCapture 2021-03-31 PM 3.13.58.png)

RestController와 GetMapping 을 이용한 API 형태에서, 기본적으로 데이터는 JSON 형태로 produce 됩니다.
{: .notice--info}

## 참고자료 및 출처

- <https://www.wrapuppro.com/programing/view/4yO1xLOCovPwa4R>
- <https://blog.jiniworld.me/69>
- <https://goddaehee.tistory.com/209>
- <https://goddaehee.tistory.com/205>

[^1]: docker-compose 에 대한 설명 및 사용법에 대해서는 [docker-compose를 이용하여 docker를 편리하게 사용하기](https://linkeverything.github.io/container/docker-compose/) 글을 참고하시면 좋습니다.