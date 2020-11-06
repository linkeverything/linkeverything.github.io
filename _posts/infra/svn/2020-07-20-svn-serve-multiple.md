---
title: "하나의 svnserve로 여러 repository 구동하기"
categories: [svn]
tag: [svn, multiple repositories]
last_modified_at: 2020-07-20
---
linux 환경에서 여러 svn 서버를 구동하는 경우에, 각 repository 별로 svnserve 명령어를 실행하는 것은 귀찮은 일입니다. 그럼에도 불구하고 각각 실행하는 것이 편리한 경우도 있습니다. 예를 들어 각 repository 를 재실행 할 때에 다른 repository 는 여전히 사용 가능했으면 한다거나 하는 등의 경우입니다.

그럼에도 불구하고 하나의 명령으로 실행하고자 하는 이유는, svn server 가 그렇게 자주 껏다 켰다를 반복하는 대상도 아닐 뿐더러, 서버를 내리고 올리는 데 시간이 과거만큼 많이 걸리지 않는다는 점도 있습니다.

여기서는 하나의 `svnserve` 명령어를 이용해서 다수의 repository 를 구동하는 것에 대해서 설명합니다. 무려 2007년에 작성된 문서이지만, 깔끔하게 설명되어 있어 차용하여 설명합니다. 
{: .notice--info}

#### 하나의 repository가 실행 중

일반적인 것 처럼, 최초에는 하나의 repository 가 서비스되고 있었습니다. 문제가 있지는 않았지만, 업무를 하다 보니 하나 혹은 두 개의 repository 를 더 구성해야 하는 상황이 발생했다고 가정합니다.

설명하기 편하도록 기존 repository 를 <mark style='background-color: #fff5b1'>A</mark>, 신규로 생성하려는 repository 들을 <mark style='background-color: #ffdce0'>B</mark>, <mark style='background-color: #dcffe4'>C</mark>라고 하겠습니다. 처음에 구동중이던 <mark style='background-color: #fff5b1'>A</mark> repository 는 다음 다음 명령어로 svn 서버를 구동중이었습니다.

```sh
$ svnserve -d -r /home/svn/repository
```

/home/svn/repository 라는 경로를 대상으로 하여 svn repository 를 구동하게 됩니다. 아마도 svn 사용자의 홈 폴더 아래에 repository 폴더를 만들어 두고, 그 폴더에 대해서 svn 서버를 구동해 왔던 것 같습니다. 

#### svn repository의 추가

시간이 지난 뒤, <mark style='background-color: #ffdce0'>B</mark>, <mark style='background-color: #dcffe4'>C</mark> 두 개의 repository 를 추가 구동해야 하는 상황이 발생하였습니다. 기존 <mark style='background-color: #fff5b1'>A</mark> repository 와는 별개로 구성되어야 하므로 아래 명령어들을 차례로 입력하면서 repository 를 구성합니다.

단순히 폴더를 만드는 것 대신에 `svnadmin create` 명령어를 사용하는 것을 잘 보아야 합니다. **단순히 mkdir 로 생성한 repository 를 향후에 `svnserve` 명령어로 실행하려고 하면 실행되지 않습니다.**

```sh
$ ssh svn@svnserve
$ mkdir repositories
$ cd repositories
$ svnadmin create b_repository
$ svnadmin create c_repository
```

여기까지 실행하고 나면 다음과 같은 구성이 되어 있을 것입니다. 

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

주목할 점은, 기존의 <mark style='background-color: #fff5b1'>A</mark> respoitory 는 나머지 <mark style='background-color: #ffdce0'>B</mark>, <mark style='background-color: #dcffe4'>C</mark> repository 들과는 다르게 한 단계 위에 위치해 있습니다.
{: .notice--info}

#### 기존 repository 의 이동

이제 해야 할 것은 기존의 A repository를 옮겨 다른 repository 들과 함께 존재하게 해 주는 것입니다. 그리고 그 최상이 폴더인 repositories 폴더에 대해서 svnserve 명령을 실행해 줍니다.

```sh
$ killall svnserve
$ mv repository  repositories/a_repository
$ svnserve -d -r /home/svn/repositories
```

폴더를 옮기지 않고, 이들을 모두 감싸고 있는 `/home/svn` 폴더를 그대로 `svnserve` 명령어로 지정해도 제대로 동작할 것으로 짐작됩니다. 그런데도 여기서 폴더를 이동한 이유는 관리 측면에서 통일감을 주겟다는 측면과 함께, 예제로 들었던 `/home/svn` 폴더가 사용자 폴더이기 때문에 이를 그대로 `svnserve` 하는 것은 지양하려고 하는 의미입니다.
{: .notice--warning}

이렇게 하면 url 정보는 다음과 같이 됩니다.

- svn://svnserver/a_repository
- svn://svnserver/b_repository
- svn://svnserver/c_repository

#### 기존 사용자의 변경

기존 사용자들은 repository 폴더를 바라보고 있을 것 입니다. 따라서 이 때에는  `switch` 명령으로 정보를 변경해 줍니다.

이 작업은 svn client 가 되는 개발자 각각의 PC에서 진행하는 작업입니다. 그냥 개발 환경에서 기존 repository 를 지우고 다시 새 주소의 것을 pull 하는 것으로도 충분히 가능한 작업입니다. 
{: .notice--warning}

```sh
$ svn switch --relocate svn://svnserver svn://svnserver/a_repository
```

## 출처 및 참고자료

- <http://wordaligned.org/articles/one-svnserve-multiple-repositories>