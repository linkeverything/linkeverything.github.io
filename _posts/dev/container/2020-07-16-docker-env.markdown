---
title: "Docker 에 환경변수 관리하기"
excerpt: "How to use environment variable on Docker container"
categories:
  - "dev"
  - "container"
tag:
  - "docker"
  - "environment"
  - "variable"
last_modified_at: 2020-07-16
---

Docker container에서 환경변수를 활용하는 방법에 대해서 설명합니다. 현재 시점 기준으로 하여 [`docker documentation`](https://docs.docker.com/compose/environment-variables/)에 명시된 사항들을 대상으로 합니다. 다양한 방법을 소개하나, 각 인프라 환경에 맞게 취사선택하여 활용하는 것을 권장합니다.

#### 기본 사용방법

docker 를 실행하는 구문에 `-e` 옵셥을 이용하여 설정하는 방법입니다. 

`docker run -e VARIABLE=VALUE ...`

만약 `docker-compose`나 `kubernetes` 에서 `yaml` 파일을 활용하는 경우 다음과 같이 설정해 주면 됩니다.
  
```yaml
web:
  environment:
    - DEBUG=1
```

#### 시스템 환경변수 pass 하기

만약 docker 를 실행하는 시스템의 환경 변수를 그대로 전달하고 싶다면 `VARIABLE=VALUE` 쌍에서 `VALUE`를 뺀 `VARIABLE`만을 전달하면 됩니다. 

`docker run -e VARIABLE ...` 

이렇게 하면 `docker run`을 실행하는 시스템의 환경 변수값을 그대로 전달하게 됩니다. (당연하게도, container를 실행한 이후에 시스템에서 환경변수의 값을 변경해도 container에는 적용되지 않습니다. 다시 재시작을 해주어야 합니다.)

```yaml
web:
  environment:
    - DEBUG
```

#### env file 활용하기

환경 변수가 많아지거나, 혹은 시스템별로 관리를 하고 싶은 경우에는 파일로 묶어서 처리할 수 있습니다. 

`docker run --env-file=FILE ...`

파일은 `VARIABLE=VALUE` 가 한줄에 하나씩 나온 형태의 텍스트 문서이면 됩니다. (확장자는 문제되지 않는 것으로 알고 있습니다.)

```yaml
web:
  env_file:
    - web-variables.env
```

#### 기본 환경변수 설정 파일의 활용

만약 일일히 `env_file` 을 지정해 주지 않고 사용하고 싶다거나, 세트로 바꾸어 가면서 실행해야 할 필요가 없다면 `.env` 파일을 실행하는 위치에 생성해 주고 실행하면 됩니다. 

```bash
$ cat .env
TAG=v1.5

$ cat docker-compose.yml
version: '3'
services:
  web:
    image: "webapp:${TAG}"
```

위 상황에서 `docker-compose up` 으로 실행한 container 의 정보를 아래와 같이 활용할 수 있습니다. 

```bash
$ docker-compose config

version: '3'
services:
  web:
    image: 'webapp:v1.5'
```

만약 사용자가 임의로 `shell` 안에서 해당 환경 변수를 설정해 준다면 그 설정이 우선하게 됩니다. (이 부분은 주의가 필요합니다. 만약 혼선을 야기하고 싶지 않다면 unique한 변수명을 지정해 주는 것이 좋습니다. )

```bash
$ export TAG=v2.0
$ docker-compose config

version: '3'
services:
  web:
    image: 'webapp:v2.0'
```

#### 환경 변수의 우선순위는 어떻게 될까?

이렇게 다양한 방법으로 설정할 수 있는 환경 변수의 적용되는 우선순위는 다음의 순서를 따릅니다. 

1. Compose file
2. Shell environment variables
3. Environment file
4. Dockerfile
5. Variable is not defined

1에 대한 것은, 일반적으로 사용하는 docker-compose.yaml 파일에서 설정해 주었던 것을 고려하면 됩니다. 또한 2와 3의 관계는 바로 앞에서 export 로 설정해 준 부분이 우선하는 것을 살펴보면 됩니다. 마지막으로 4에서의 변수는 가장 기본적인 default 값을 설정하는 것이라고 이해하면 됩니다. 

공식 홈페이지 문서에 좋은 예시가 있어 가져왔습니다. 다음과 같이 설정되어 있는 경우를 가정합니다. 

```bash
$ cat ./Docker/api/api.env
NODE_ENV=test

$ cat docker-compose.yml
version: '3'
services:
  api:
    image: 'node:6-alpine'
    env_file:
     - ./Docker/api/api.env
    environment:
     - NODE_ENV=production
```

이를 실행하고 확인 해 보면 다음과 같은 결론을 얻을 수 있습니다.

```bash
$ docker-compose exec api node

> process.env.NODE_ENV
'production'
```

#### docker-compoase 에서 주의할 사항

docker compose를 이용하는 경우에는 `COMPOSE_` 또는 `DOCKER_` 로 시작하거나(prefix), [`CLI Environment Variables`](https://docs.docker.com/compose/reference/envvars/)에 명시된 내용에 대해서는 환경 변수가 적용되지 않습니다. (일종의 예약어라고 보면 됩니다.)

#### 참고자료 및 출처

https://docs.docker.com/compose/environment-variables/