---
title: "하나의 svnserve로 여러 repository 구동하기"
excerpt: "One svnserve, multiple repositories"
categories:
  - "infra"
  - "svn"
tag:
  - "svn"
  - "multiple repositories"
last_modified_at: 2020-07-20
---

> linux 환경에서 여러 svn 서버를 구동하는 경우에, 각 repository 별로 svnserve 명령어를 실행하는 것은 귀찮은 일입니다. 그럼에도 불구하고 각각 실행하는 것이 편리한 경우도 있습니다. 예를 들어 각 repository 를 재실행 할 때에 다른 repository 는 여전히 사용 가능했으면 한다거나 하는 등의 경우입니다.

여기서는 하나의 `svnserve` 명령어를 이용해서 다수의 repository 를 구동하는 것에 대해서 설명합니다. 무려 2007년에 작성된 문서이지만, 깔끔하게 설명되어 있어 차용하여 설명합니다. 

#### 하나의 repository가 실행 중

편의상 기존 repository 를 A, 신규로 생성하려는 repository 를 B, C라고 하겠습니다. 기존에는 다음 명령어로 svn 서버를 구동중이었다고 가정합니다.

```sh
$ svnserve -d -r /home/svn/repository
```

아마도 svn 사용자의 홈 폴더 아래에 repository 폴더를 만들어 두고, 그 폴더에 대해서 svn 서버를 구동하려는 것 같습니다. 

#### svn repository의 추가

시간이 지난 뒤, B, C 두 개의 repository 를 추가 구동해야 하는 상황이 발생하였습니다. 기존 A repository 와는 별개로 구성되어야 하는 상황이어서 다음과 같이 실행합니다.

```sh
$ ssh svn@svnserve
$ mkdir repositories
$ cd repositories
$ svnadmin create b_repository
$ svnadmin create c_repository
```

다음과 같은 환경 구성이 되었습니다. 

```
+---repositories
|   +---b_repository
|   |   +---conf
|   |   +---dav
|   |   +---db
|   |   +---hooks
|   |   \---locks
|   \---c_repository
|       +---conf
|       +---dav
|       +---db
|       +---hooks
|       \---locks
\---repository
    +---conf
    +---dav
    +---db
    +---hooks
    \---locks
```

#### 기존 repository 의 이동

이제 해야 할 것은 기존의 A repository를 옮겨 다른 repository 들과 함께 존재하게 해 주는 것입니다. 그리고 그 최상이 폴더인 repositories 폴더에 대해서 svnserve 명령을 실행해 줍니다.

```sh
$ killall svnserve
$ mv repository  repositories/a_repository
$ svnserve -d -r /home/svn/repositories
```

이렇게 하면 url 정보는 다음과 같이 됩니다.

- svn://svnserver/a_repository
- svn://svnserver/b_repository
- svn://svnserver/c_repository

#### 기존 사용자의 변경

기존 사용자들은 repository 폴더를 바라보고 있을 것 입니다. 따라서 이 때에는  `switch` 명령으로 정보를 변경해 줍니다.

```sh
$ svn switch --relocate svn://svnserver svn://svnserver/a_repository
```



## 출처 및 참고자료

http://wordaligned.org/articles/one-svnserve-multiple-repositories