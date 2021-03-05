---
title: "docker-compose를 이용하여 docker를 편리하게 사용하기"
categories: [container]
tag: [docker, compose]
last_modified_at: 2021-03-05
---

## docker 사용에 대해..

container 형태로 여러 프로그램, 서비스들을 실행하려고 할 때에는 그 명령어들이 복잡한 이미지(docker images)를 얼마나 customizing 하느냐, 또 얼마나 많은 환경변수등을 추가해서 사용하느냐에 따라 엄청나게 길어질 수 있습니다. 예를 들어 jenkins를 docker container 형태로 실행하려고 할 때에 기본적으로 아래 정도의 명령어를 이용해서 구동하게 될 것입니다. 

```sh
$ sudo docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```

아마도 이런 과정이 귀찮고 외우기 힘드니, 위 명령어를 shell 로 만들어서 두고 사용하게 될 것 같습니다만, docker 특성상 사용하게 되는 명령어들이 많아, 빠뜨리는 경우도 많고, 또 조금의 변경사항이 발생할 떄 마다 수정하고 실행하는 것 또한 잘 이해가 가지 않습니다. 또한 위 명령어는 그 길이가 길어지게 되면 가독성이 많이 떨어지기 때문에 알아보기도 쉽지 않습니다. 또한 docker 명령어는 기본적으로 root 권한이 있어야 하기 때문에 sudo 를 매번 적는 것 역시 여간 귀찮은 것이 아닙니다.

## docker-compose 란..?

이러한 분편함을 해소해 주는 것이 docker-compose 입니다. docker-compose는 parameter 로 설정된 위 정보들을 모두 docker-compose.yml 이르는 파일에 적게 하고(일종의 설정 파일), 이를 이용해서 docker를 실행해 줍니다. 이를 위해서는 docker 이외에도 docker-compose를 설치해야 합니다. 

#### 필수사항

우선 docker-compose 를 사용하기 위해서는 다음의 필수 요소들이 설치되어 있어야 합니다.

- sudo 권한이 있는, root 가 아닌 사용자

  이에 대해서는 root 권한만 가지고 있는 것이 아니라 docker 그룹에 사용자를 등록해서 사용하는 것이 조금 더 편리합니다.

  ```sh
  sudo usermod -aG docker $USER
  ```

  > $USER 자리에는 내 계정 id 를 직접 넣어도 됩니다.

- docker 설치


#### 설치

1. current release 를 체크하고, 필요하다면 update 한다.

   ```sh
   sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
   ```

2. 아래 명령어로 권한 처리를 합니다.
  
   ```sh
   sudo chmod +x /usr/local/bin/docker-compose
   ```

3. 버전 확인을 합니다. 

   ```sh
   docker-compose --version
   ```

   결과로 아래와 같이 나오면 됩니다.

   ```sh
   $ docker-compose --version
   docker-compose version 1.18.0, build 8dd22a9
   $
   ```

#### 사용

1. docker-compose.yml 파일 생성

내가 docker 를 실행하고자 하는 폴더에 docker-compose.yml 파일을 생성합니다. 여기서는 mariaDB 에 대한 docker-compose.yml 파일을 예로 들겠습니다. 

version: '3.1'

services:
  maria:
    image: mariadb:latest
    container_name: "mariadb"
    restart: always
    ports:
      - "63306:3306"
    volumes:
      - ~/docker/mariadb/etc/mysql/conf.d:/etc/mysql/conf.d:ro
      - ~/docker/mariadb/var/lib/mysql:/var/lib/mysql
      - ~/docker/mariadb/var/log/maria:/var/log/maria
    environment:
      - MYSQL_ROOT_PASSWORD=???
      - TZ="Asia/Seoul"
 

2. 실행 및 재실행

실행하고자 하는 yml 파일이 있는 위치에서 아래 명령어를 입력합니다. 백그라운드에서 실행을 원하는 경우 -d 옵션을 추가합니다.

$ docker-compose up
$ docker-compose up -d
 

3. 확인

확인은 docker-compose 보다는 docker 자체 명령어로 하는 것이 직관적이라 그렇게 하고 있습니다.

$ docker ps -a
 

4. 중지 및 삭제

중지는 stop 파라미터를, 삭제는 rm 파라미터를 주가합니다. 여기서의 삭제는 당연히 image가 아니라 container 입니다. 역시 yml 파일이 있는 위치에서 아래 명령어를 입력합니다.

$ docker-compose stop
$ docker-compose rm 


출처: https://4urdev.tistory.com/81 [Simplify]




## 참고자료 및 출처

기존 tistory 블로그에 적었던 본인의 글을 기준으로 하여 조금 보완하였습니다.

- <https://4urdev.tistory.com/81>