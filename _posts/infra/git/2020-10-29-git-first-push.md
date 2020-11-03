---
title: "Local 개발환경 git에 연결하기(2fa 인증 관련사항 포함)"
categories: [Git]
tag: [git, personal access token]
last_modified_at: 2020-10-29
---
Git이나 SVN을 처음 접하고, 개발을 진행할 때에는 회사에서 단순히 '개발자' 였기 때문에 별다른 어려움 없이 매일 같은 명령어만 사용해 왔습니다. 그러던 중 신규로 repository를 생성해야 할 일이 발생하였는데, 아무렇지 않게 가져다가 사용하던 repository들이, 직접 설정하여 사용하려고 하니 꽤나 어려움이 있었습니다. 

그래서, 약간 편법으로 사용했었는데, 제대로 된 방법을 정리해야 할 것 같아 아래에 정리합니다. [^1] 

[^1]: 이 문서에서의 가이드들은 편의상 Git에 한정하여 설명합니다. 그리고 그 예제로 사용된 git server는 GitHub를 사용하는 것으로 하겠습니다.

<br/>

## 좋지 않은 사용

이 방법은 지양해야 할 방법입니다. 물론 동작하지 않는 것은 아니지만, 뒷쪽에 설명하는 것이 실제 상황에서는 더 정확하고 올바른 방향인 것 같습니다. 간단히 요약하자면, git 에 빈 깡통인 repository를 만들고, 작업중인 폴더를 덮어쓰기 하여 원래부터 버전관리를 해 왔던 것 처럼 진행하는 것입니다.

<br/>

#### repository 생성 및 clone

우선 만들고자 하는, 버전관리를 하고자 하는 repository를 하나 생성합니다. github에서 사용하는 경우, repository를 생성하는 데, repository이름만으로 간단히 생성할 수 있습니다. repository를 생성했다면 해당 repository에 대한 주소를 알아둡니다. 이 repository이름은 실제 프로젝트 명과 동일할 필요는 없습니다. IDE로 IntelliJ를 사용하든 eclipse 를 사용하든, 동일하게 프로젝트에 대한 정보를 폴더명으로 하지 않습니다. 그러므로 그 이름 자체는 크게 문제되지 않지만 repository관리가 편리하도록, 다른 repository들을 고려하여 지정해야 합니다.

![](/assets/images/posts/infra/git/2020-10-29-git-first-push/capture 2020-10-30 PM 01.44.02.png)

GitHub에서 repository를 생성하면 첫 화면에 주소를 확인할 수 있고, 그 옆에 버튼을 눌러 clipboard에 복사할 수 있습니다(붙여넣기 하면 https 주소가 붙여집니다).
{: .notice--info}

![](/assets/images/posts/infra/git/2020-10-29-git-first-push/capture%202020-10-30%20PM%2001.47.37.png)

<br/>

#### 작업중이던 파일 push 하기

이제 Local IDE에서 개발하고 있었던 프로젝트를 해당 repository에 push해야 합니다. 정상적인 방법이라면 git의 정보를 그대로 가지고 remote address 를 알아내서, 그 주소에 정상적으로 push해 주는 것이 좋습니다. 하지만 그 방식이 정확하게 기억이 나지 않는 경우, 약간 우회하는 형태로 진행할 수 있습니다.

1. 우선 앞서 만든 repository를 clone 합니다. 이 repository에는 online/offline 으로 아무런 작업을 하지 않았기 때문에 repository 명 폴더만 생성되면서 clone 될 것입니다. 

  ```sh
  git clone https://github.com/Simplify-Criss/testRepository.git
  ```

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/capture 2020-10-30 PM 02.06.16.png)

2. 이제 작업중이던 프로젝트 폴더의 모든 파일을 복사해 옵니다. 마치 clone 한 껍데기에서 처음부터 작업이 이루어진 것 처럼 작성하게 되는 것입니다. [^2]

[^2]: `-r` 옵션을 넣어 폴더들까지 모두 복사가 되도록 합니다.

  ```sh
  cp -r {작업하고 잇던 프로젝트 폴더} {clone 한 껍데기가 있는 폴더}
  ```

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/capture 2020-10-30 PM 02.11.46.png)

  이렇게 하면 마치 깡통 프로젝트를 git에 생성해서, 그 안에서 작업하고, 그것을 push하는 것 처럼 보여집니다.

3. 마지막으로 변경사항을 push 하여 git server(여기서는 github입니다)에 반영합니다. 

  ```sh
  git add .
  git commit -m "first commit"
  git push
  ```
 
  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/capture 2020-10-30 PM 02.15.22.png)

이 과정이 올바르지 않다고 이야기하는 이유는 폴더가 너무 들쭉날쭉합니다. 생각나는 몇 가지 문제점은 아래와 같습니다. 

- IDE가 가리키는 폴더가 온전해 지지 못합니다. 최근 많이 사용중인 eclipse, IntelliJ, vscode 와 같은 것들은 모두 열어서 작업했었던 프로젝트 폴더의 구조를 기억하고 있습니다. 만약 위와 같이 clone 하여 거기에 덮어쓰고 작업을 한 것처럼 했다면, local 개발환경에 있는 IDE에게도 그 git repository를 clone한 폴더를 바라보게 변경해줘야 합니다. 이 과정을 시간도 소요되고 여간 불편한 작업이 아닐 수 없습니다(특히 eclipse 의 경우에는 workspace에 프로젝트들을 관리하고 있습니다)

- 이렇게 폴더 자체를 복사하면, IDE의 '내보내기' 기능을 사용하는 것에 비해서 쓰레기 파일들이 다 같이 복사됩니다. 예를 들어 여러 빌드 파일들이나 설정 정보 등도 같이 넘어오게 되는데 이 과정에서 path가 달라져서 문제가 발생하기도 합니다. 물론 바로잡아주면 정상 동작하겠지만, 분명 작업하던 내용이고 이상없는 것을 굳이 경로를 바꿔가면서까지 바로잡아야 하나 싶습니다. 

- 간혹 기존 project와 clone 한 project가 헷갈리는 경우도 발생합니다. IDE가 기존 작업 폴더를 기억하고 있어, 기존 폴더가 open되는 경우도 허다하고 git에 연결되지 않는 기존 작업 프로젝트에 계속 작업하게 되는 경우도 있을 것입니다. 

<br/>

## 이상적인 사용법

제가 **이상적인** 이라는 단어로 표현한 것은 일반적인 경우를 이야기하는 것입니다. repository를 만들고 이 위에다가 프로젝트를 생성하는 경우도 있겠지만, IDE들의 특성상, 그리고 보편적인 절차가, IDE에서 그저 프로젝트를 생성하고 우선 prototype처럼 개발을 진행하게 됩니다. 

이렇게 local환경에서 개발을 하고 나서 조금 쓸만한 수준이 되었을 때, 혹은 이 정도면 함께 일하는 사람들과 공유하고 본격적으로 개발을 할 수 있겠다 싶은 수준이 되면 이를 git server(github)에 push하여 공유하고 개발을 시작합니다.

<br/>

#### 작업 시작하기

IDE에서 기본 프로젝트 생성하듯 프로젝트를 생성합니다. 본인의 workspace나 project를 옮겨놓은 폴더를 가리킬 것이고, 본인이 이상적이라고 생각하는 프로젝트명을 주게 되면 그 자체가 폴더명이 되면서 그 하위에 프로젝트 구조를 생성해 줄 것입니다. 

예를 들어 gradle 프로젝트의 경우 gradle관련 설정 파일들이나 빌드 파일들이 생성되고, IDE의 종류에 따라서 

- IntelliJ 는 `.idea` 폴더가 생성되고, 
- eclipse 는 `.settings` 폴더와 `.project` 파일이 

생성됩니다. 이러한 파일들에는 프로젝트 자체에 대한 정보를 가지고 있음은 물론이고, IDE의 각종 설정에 대한 정보도 가지고 있습니다. 예를 들어 이 프로젝트에서 특정 플러그인을 활성화 했다거나, 빌드하는 Java JDK 버전에 대한 정보 및 alias도 가지고 있습니다. 최근에는 이러한 부분이 개발 환경에 따라 맞지 않으면 알아서 보정해 주기도 하지만, 아래쪽에서 이 부분은 ignore하는 방법도 공유하겠습니다.

<br/>

#### repository 생성

repository생성은 앞서 설명한 내용과 동일합니다. git server(github)에서 repository 를 생성해 둡니다. 그리고 역시 해당 repository의 주소를 복사해 둡니다. 

<br/>

#### .gitignore 에 설정하기

이제 github에 project를 연결하고 push 하면 되는데, 그 전에 한 가지 해야할 일이 있습니다. 

개인 local환경에서 IDE를 열어 개발을 하다 보면, 앞서 이야기한 `.idea`폴더나 `.settings` 폴더와 같이 개인 환경에만 적합한 내용들이 있습니다(물론 이런 것 까지 공유해야 하는 경우도 있긴 합니다). 또한 빌드 결과로 생긴 `build` 폴더 역시 공유의 대상이 아닙니다. 이렇게 git 에서 제외할 것들을 명시해 두는 파일이 `.gitignore`파일입니다. 

IDE에서 제외하고자 하는 파일이나 폴더를 선택/우클릭 하여 git ignore 를 실행하는 것도 가능하지만, `.gitingore` 파일을 직접 열어, 내용을 추가해 주어도 됩니다.[^3] `.gitignore` 파일은 일반적인 텍스트 파일로서 그 안에 내용은 한 줄에 하나의 항목씩 git 을 통해 공유하지 않을 대상을 명시합니다.

[^3]: git 프로젝트에만 이 메뉴가 나타납니다. git 프로젝트가 아닌 경우 이 메뉴가 나타나지 않으니 파일을 직접 추가하시기 바랍니다.

![](/assets/images/posts/infra/git/2020-10-29-git-first-push/capture 2020-10-30 PM 03.04.23.png)

![](/assets/images/posts/infra/git/2020-10-29-git-first-push/capture 2020-10-30 PM 03.05.13.png)

![](/assets/images/posts/infra/git/2020-10-29-git-first-push/capture 2020-10-30 PM 03.05.47.png)

git이나 svn모두 push 한 내용을 추후에 ignore하게 되면 서버에는 그 파일이 그대로 남아 있게 되어 지저분한 상태가 됩니다. 이런 때에는 서버에서 직접 삭제해 주거나 해야 하는데 그 과정이 복잡하고 꼬일 수 있는 소지가 있으니 미리 작업하시길 권합니다. 

`.gitignore` 파일은 공동 작업하는 모두가 동일하게 적용되어야 하므로 git 에 push 합니다. 이렇게 되면 git clone (git pull) 하는 사용자 모두에게 동일하게 적용되므로 모두 같은 상태로 개발이 가능합니다.

<br/>

#### 작업중인 내용 연결하기

이제 이 프로젝트를 git server에 연결해 보겠습니다. 우선 MacOS 의 터미널이나 iterm2, Windows 의 명령 프롬프트 등을 열어 해당 프로젝트의 최상위 루트로 이동합니다. 그리고 다음을 입력하여 git 프로젝트로 변환합니다. 

```sh
git init
```

이제 프로젝트가 git 프로젝트로 변환되었으니 local 환경 내에서 git 프로젝트로 add / commit 이 가능합니다. 추가로 한 작업이 있다면 add / commit 해 줍니다.

```sh
git add .
git commit -m "modified some files"
```

이제 이를 github에 연결하고 push 하려면 다음과 같이 입력합니다.[^5]

[^5]: `git config` 명령을 통해 이름과 이메일을 입력해 두어야 할 수도있습니다. github를 사용하던 환경에서는 별도로 요구되지 않습니다.

```sh
git remote {연결하려는 github repository주소}
```

여기까지가 원격 git server에 대해 <mark style='background-color: #ffdce0'>연결</mark>하는 작업입니다. 하지만 그야말로 <mark style='background-color: #ffdce0'>연결되기만 한 상태</mark>이기 때문에 이를 업로드(push)해 주어야 합니다.

```sh
git push -u origin master
```

이렇게 하면 이제 git server(github)에 해당 내용이 올라갑니다. 

<br/>

## GitHub의 2중 인증 처리하기

꼭 github가 아니더라도 개인 계정을 보호하기 위해서 2중인증(2 factor authrentication)은 활성화 해 두기를 권장합니다.

그런데 어떤 서비스든 2중인증을 활성화하면 서비스를 <mark style='background-color: #dcffe4'>연결</mark>할 때마다 추가로 해 주어야 할 작업이 있습니다. 그게 바로 token을 발급하는 것입니다. 

google, naver등과 마찬가지로 github역시 token을 발급해야 위 과정이 정상적으로 수행됩니다. 예를 들어 위와 같이 git push 를 할 떄에 계정 정보가 유효한지를 물어보게 될 텐데, github 로그인 정보를 매번 물어보게 되면, 매번 2중인증 정보를 물어야 하는데, 그 방법도 적절치 않을 뿐더러 command 시에 물어본다는 것 자체가 가능하지는 않습니다. (git을 IDE의 플러그인으로 사용하는 경우에도 마찬가지)

이 때문에 로그인 처리를 해주는 임시 비밀번호인 token을 생성하고 이를 비밀번호 대신 사용하게 해 주어야 문제가 생겼을 때에 비밀번호를 변경(만료)시키는 것이 아니라 해당 token만 만료시켜서 문제를 해결해야 합니다.

<br/>

#### personal access token 생성

다음 과정으로 token을 생성합니다. 

1. email 주소를 확인(verity)합니다.
   
2. github에 로그인하고 본인의 계정 하위에 settings에 진입합니다.

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/1.png)

3. 좌측 메뉴에서 Developer settings를 선택합니다.

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/2.png)

4. 좌측에서 Personal access tokens를 선택합니다.

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/3.png)

5. Generate new token을 선택합니다.

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/4.png)

6. token 설명을 넣습니다. 겉으로 보이는 정보는 이 것들이 전부이기 때문에 이해 가능한 수준으로 적절히 입력해 줍니다.

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/5.png)

7. 범위나 권한 등에 대한 정보를 체크합니다. 만약 command line 에서 사용하고자 한다면 `repo`를 선택합니다.

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/6.gif)

8. Gernerate token 버튼을 눌러 생성합니다.

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/7.png)

9. 다음 나타난 화면에서 복사 아이콘을 눌러 복사합니다. 

  ![](/assets/images/posts/infra/git/2020-10-29-git-first-push/8.png)

  이 정보는 생성 직후에만 1회 보여집니다. 따라서 잘 복사해 두고 사용해야 합니다. 
  {: .notice--warning}

<br/>

#### personal access token 활용

위에서 사용한 정보를 아래의 예시 처럼, git 명령어를 사용할 때에 물어오는 password대신 사용하면 됩니다.

```sh
$ git clone https://github.com/username/repo.git
Username: your_username
Password: your_token
```

<br/>

## IntelliJ 에서 Remote Repository 연결하기

Git repository 로 설정되지 않은, 현재 작업중인 project에 대해서 Remote Git에 연결하는 방법입니다.

1. IntelliJ 메뉴에서 `VCS` &rarr; `Import into Version Control` &rarr; `Create Git Repository` 로 차례로 이동합니다.

2. 팝업 윈도우에서 프로젝트를 선택하고 `OK` 선택합니다.
   
3. local branch master에 필요한 모든 파일들을 commit 합니다.

4. 터미널 등을 실행하여 프로젝트 폴더 루트로 이동합니다. (윈도우에서는 명령 프롬프트)

5. `git remote add origin {remote git address}` 를 입력합니다

6. `git push origin master` 입력합니다.

7. IntelliJ 로 돌아와서 프로젝트를 우클릭하고 `Synchronize` 선택

8. 프로젝트로에서 우클릭하여 `Git` &rarr; `Repository` &rarr; `Branches` &rarr; `origin/master` &rarr; `Checkout as new local branch` 선택


<br/>

## 출처 및 참고자료

- <https://emflant.tistory.com/123>
- <https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/creating-a-personal-access-token#using-a-token-on-the-command-line>
- <https://coderwall.com/p/gkyqmg/intellij-idea-how-to-upload-project-to-remote-branch>