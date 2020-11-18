---
title: "Container기반 환경 (Docker 설치하고 사용하기)"
categories: [study, springboot]
tag: [install, docker]
last_modified_at: 2020-11-18
published: false
---

Docker는 Container 기반의 프로그램 구동을 돕는 도구 중 하나입니다. Container기반 이라고 이야기를 하였지만 현재는 `container = docker` 라고 할 정도로 docker가 정점을 찍고 있는 것 같습니다. 

2018-2019년 정도부터 docker에 대한 이야기를 꾸준히 들어온 것 같습니다. 일반 SI 업체에 근무하고 있었고, 인프라 담당자가 아닌 Java 개발자로서 접하게 되었다는 것은 이미 많이 유명해졌다는 것을 의미합니다. 

## Container

Container 방식으로 서버측 프로그램을 구축한다는 것은 단어 의미 그대로 하나로 감싸진 덩어리를 실행한다는 것과 동일한 개념입니다. 요즘 출시되는 거의 대부부의 CPU 들이 지원하고는 있지만, 당연히 가상화 기술을 지원해야 합니다. 

여기서 이야기하는 가상화는 일반적으로 생각하는 가상화와는 조금 차이가 있고, OS에서 지원한다고 해서 CPU에서도 지원하는 것은 아니기 때문에 반드시 확인해야 합니다. 
{: .notice--warnging}

커널이나 드라이버들을 제외한 나머지 요소들을 container라는 덩어리로 감싸고 이를 docker 기반 위에 구동시키는 개념이라고 보면 됩니다. 프로그램에 직접 관련된 것들은 container안에 존재하고 공통으로 사용해도 되는 네트워크 드라이버, 커널 요소 등은 Host 의 것을 공유하는 개념입니다.

#### 왜?

왜 갑자기 부각되었는가를 생각해봐야 제대로 활용할 수 있을 것 같습니다. 

우선 MSA(Micro-Service Architecture)라는 개념이 다시 부각되기 시작하면서 Container로 감싼 작은 덩어리들로 나누는 것이 더욱 힘을 받은 것 같습니다. Container형태로 구현하게 되면 각 기능별로 container를 구성할 수 있고, 그 기능에 대해서 부하가 증가하는 경우, container 별로 scaling할 수 있기 때문에 자원의 소모가 적다고 이야기합니다. 또한 특정 업무에 몰리는 시간대가 있다거나, 특정 부분에 대해서 일시적 많은 자원이 필요한 경우에도 유용합니다. 

사실 이렇게 conatiner가 유행하게 된 것에는 Hardware의 발전도 한 몫을 하고 있다고 생각합니다. Linux 기반 서버에 직접 어플리케이션을 구동시킬 당시에는 CPU / Memory 에 대해서 가격적인, 비용적인 측면에서 부담이 될 수밖에 없었고, 증설하는 데 있어서도 많은 노력과 비용이 소요되었습니다. 하지만 hardware의 발전으로 성능이 더욱 좋은 machine 을 저렴한 가격에 도입할 수 있어졌고, 따라서 그 하나의 machine 에 여러 업무를 구동하여 비용적인 우위를 점할 수 있게 된 것입니다. 

물론 여기에는 AWS, Azure, GCP와 같은 Public Cloud 의 확산도 영향을 주었다고 생각합니다. 사실 docker와 같은 container기반의 환경을 구축하고 관리하려면 kubernetes와 같은 관리 툴이 또 하나 필요한데, 위에서 말한 3개의 클라우드 서비스 제공업체를 비롯한 대다수의 메이져 업체들이 이런 instance를 제품으로 제공하고 있습니다. 즉, 해당 인스턴스를 사용하겠다고만 하면 준비 작업은 다 된 셈이 되는거죠. 물론 그 이외에도 container환경을 구축하기 위한 여러 모듈들이나 서비스들이 Saas형태로 제공되고 있습니다.

## 사전 지식

우선 간단하게나마 다음과 같은 정보들을 알고 있으면 사용하는데 도움이 됩니다.

#### Network

네트워크는 Host의 것을 공유하는 개념입니다. 당연히 container에서 트레픽을 많이 사용하면 Host에 부담이 되고, 이는 다른 container에도 영향을 주게 됩니다. 따라서 Host에 어떤 container를 구동시킬 때에는 여기 들어오는 트레픽 양을 산정하고 그에 따라서 적절히 배분하는 것이 필요합니다. 

container에서 사용하는 특정 포트는 외부 노출이 필요하다면 Host 의 특정 포트에 매핑되게 됩니다. container가 하나의 docker base위에서 구동될 때에는 내부 네트워크를 통해서 통신하기 때문에 문제가 되지 않겠지만, 외부에 서비스하게 되어 load balancer를 구축하고 container들에 접근하게 된다면 각각의 container에서 사용하는 포트들은 host의 포트와 1:1로 매핑되게 됩니다. 적절한 rule 을 가지고 포트를 매핑하여 마치 port forwarding 처럼 동작하게 해야 하기 때문에 이에 대한 관리가 필요합니다. 

사실 이러한 부분 때문에 조금 편리합니다. 하나의 machine에 여러 개의 서비스를 구동시키는 경우, `8080`, `8081`, ... 처럼 일정한 숫자들을 배분하게 되어 프로그램 구동 시마다 설정 파일에서 이를 수정해 주거나 하는 등으로 불편하게 관리해야 하는데, container로 구현하게 되면 내부 포트는 하나로 통일하고 Host에 매핑되는 포트만 관리해 주면 됩니다.

#### Volume Mount

특정 이미지를 받아서 구동시킨 결과가 container인데, container 내에서 일어나는 모든 file I/O는 container안에 귀속됩니다. 그렇기 때문에 container를 삭제했다가 다시 실행하면 기존에 써둔 파일이 보이지 않는 현상이 발생합니다. 

이러한 것들을 방지하기 위해서 container안의 그 어떤 파일이나 폴더를 Host의 특정 파일이나 폴더에 매핑하는 작업이 필요하고, 이렇게 매핑된 파일이나 폴더에 대해서는 다시 실행하더라도 유지될 수 있도록 처리하는 것이 필요합니다. 

이러한 과정을 volume mount 라고 이야기하며, docker 실행 구문의 `-v` 옵션으로 `Host의 폴더:container내부 폴더` 와 같은 형태로 옵션을 주어 실행합니다. 이 개념을 잘 구현하면 각종 설정 파일들, container내부에서 작성되는 log 파일들을 외부 Host의 한 곳에 모아둘 수 있고, 각종 설정 정보들이나 config 파일들도 유지시킬 수 있습니다. 

## Docker 설치

각설하고, 이제 docker를 설치해 보겠습니다. MacOS 와 Windows 는 설치 과정상에 특이한 부분이 전혀 없습니다.

#### MacOS & Windows

[Docker 홈페이지](https://www.docker.com/)에 접속하여 아래로 스크롤을 조금 내리다 보면, Docker Desktop 관련 부분이 보입니다. MacOS 와 Windows의 경우에는 Docker Desktop 을 설치하고 사용하는 것이 더 편합니다. 다른 설치 방법 보다는 이 것을 설치하고 사용하는 것을 권장합니다.

![](/assets/images/posts/study/springboot/2020-11-18-install-docker/capture%202020-11-18%20PM%201.38.53.png)

![](/assets/images/posts/study/springboot/2020-11-18-install-docker/capture%202020-11-18%20PM%201.39.58.png)

다운로드 하여 적절히 실행하고 다음..다음..을 눌러서 설치를 완료해주면 됩니다. 참고로 설치 이후에 MacOS의 메뉴바, Windows의 tray 아이콘을 눌러서 설정 화면을 열면 창이 하나 열리는데, **Resource**항목에 대해서는 본인 환경에 맞게 구성해 주시는 편이 좋습니다. 저의 경우에는 mariadb 2개 정도를 아주 가벼운 용도로 돌리기 때문에 아래와 같이 설정하였습니다. 

![](/assets/images/posts/study/springboot/2020-11-18-install-docker/capture%202020-11-18%20PM%201.45.13.png)

#### Ubuntu

## 기본 사용법

#### 실행하기

#### Volume

#### Network

## Spring Boot를 Docker로?

여기서는 Spring Boot를 docker로 직접 구동하는 방식과 더불어, 여기서 사용하는 db를 container로 구동시키고 작업할 예정입니다. 개발 편의성을 확보하기 위해서, 그리고 제가 사용에 익숙한 mariadb를 사용하게 됩니다. 

#### docker-compose.yml

`docker compose`에 대해서는 추후 더 적도록 하겠습니다만, 여기서는 간단히 docker-compose.yml 파일을 공유하겠습니다. docker compose의 사용은 사용 편의성 측면에서 사용하는 이유도 있지만, 향후 사용하게 될 kubernetes와 그 사용법에서 유사하기 때문에 익숙하게 해 두는 것이 편합니다.

```yml
version: '3.1'
services:
  maria_db_1:
    image: mariadb:10.5.3
    container_name: ${CONTAINER_NAME_1}
    restart: always
    ports:
      - "63306:3306"
    volumes:
      - ${PWD}/${CONTAINER_NAME_1}/volumes/etc/mysql/conf.d:/etc/mysql/conf.d:ro
      - ${PWD}/${CONTAINER_NAME_1}/volumes/var/lib/mysql:/var/lib/mysql
      - ${PWD}/${CONTAINER_NAME_1}/volumes/var/log/maria:/var/log/maria
    environment:
      - MYSQL_ROOT_PASSWORD=roqkf1!
      - TZ=Asia/Seoul
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

```
CONTAINER_NAME_1=maria_db
```

## 참고자료 및 출처

- <https://www.baeldung.com/spring-data-jpa-multiple-databases>

