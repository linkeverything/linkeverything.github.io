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


## SpringBoot 에서 연결하고 사용하기




## 참고자료 및 출처

- <https://www.wrapuppro.com/programing/view/4yO1xLOCovPwa4R>
- <https://blog.jiniworld.me/69>

[^1]: docker-compose 에 대한 설명 및 사용법에 대해서는 [docker-compose를 이용하여 docker를 편리하게 사용하기](https://linkeverything.github.io/container/docker-compose/) 글을 참고하시면 좋습니다.