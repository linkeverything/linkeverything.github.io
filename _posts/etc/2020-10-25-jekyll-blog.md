---
title: "GitHub pages 에 jekyll 로 블로그 만들기 (블로그 장단점 비교)"
categories: [Etc]
tag: [blog, jekyll, github]
last_modified_at: 2020-10-25
---
블로그를 시작하려는 많은 사람들이 있습니다. 물론 최근에는 글을 통한 블로그 보다는 영상을 이용한 유투브를 더 많이 선호하는 것 같기도 합니다. 영상이 발전하고 휴대 기기를 통한 손쉬운 스트리밍이 발전하고, 전자책 역시 꽤 많은 발전을 이루어 왔음에도 불구하고, 여전히 서점은 있고, 책은 출판되고 있습니다. 물론 혹자는 이러한 종이로된 책들이 없어질 것이라고는 하지만, 그래도 여전히 '글'을 통한 전달 방식은 꾸준히 존재할 것이라고 생각합니다.

블로그를 서비스하는 것은 매우 많습니다. 가장 쉬운 경로로 네이버 블로그, 카페, 다음(카카오)의 티스토리, 해외의 Blogger, Medium, brunch그리고 여기에서 소개하는 GitHub Pages까지 다양한 서비스들이 존재하고, 저 역시 거의 대부분의 서비스들을 가입하고 사용해본 것 같습니다. 

당연히 개인적인 견해들이 있으므로, 사람마다 느끼는 차이가 있을 수 있습니다.
{: .notice--warning}

<br/>

## 블로그 서비스들에 대한 의견

<br/>

#### Naver 블로그

가장 쉽게 접할 수 있고, 진입하기 좋은 블로그 서비스는 아무래도 네이버 블로그인 듯 합니다. PC는 물론이고, 폰이나 패드 등으로도 쉽게 작성이 가능합니다. 사진 첨부등의 기능도 제공하고 있으므로 당연히 모든 플랫폼에서 정상적으로 사용이 가능합니다. 국내 최대 포털 사이트이기 때문에 검색 결과 노출이 쉽고, 따라서 많은 방문자를 수용하기에 적절합니다. 

글꼴 변경이나 단락 수정 등의 기능도 원활이 작동하며, 꾸준한 업데이트로 <mark style='background-color: #dcffe4'>블로그 앱 역시 안정적으로 동작</mark>합니다. 또한 <mark style='background-color: #dcffe4'>댓글 알림</mark> 등의 기능을 통해 포스트를 보는 사람들과 소통할 수 있고, 우클릭 방지 기능 등으로 세세한 설정도 가능합니다. 다양한 스킨을 제공하고 있어 예쁘게 꾸미는 것도 가능합니다. 

다만 제가 생각하는 단점은 <mark style='background-color: #ffdce0'>개발 내용의 공유</mark>에 있습니다. 지금은 조금 좋아졌는지 모르겠지만, 스크립트를 넣는 것이 조금 불편했고, 보안에 조금이라도 취약해 보이는 것은 차단되어 버립니다. 또한 <mark style='background-color: #ffdce0'>개발 언어를 멋지게 highlighting 하여 첨부하는 데 조금 불편함</mark>이 있었습니다. 

마지막으로 블로그를 통한 수입원인 광고 역시 네이버 광고를 삽입하여 가능하지만, 다른 플랫폼에는 친화적이지 못한 점이 아쉬웠습니다. 

<br/>

#### TiStory 블로그

가장 최근까지 잘 사용했던 블로그입니다. (현재도 운영이 되고 있긴 합니다)

조금 특이하게도, 아무나 블로그를 생성할 수는 없고, 초대장을 받아 생성이 가능한 블로그입니다.(지금도 그런지는 확인해봐야 할 듯 합니다) 그래서 이런저런 커뮤니티에 초대장을 구글해서 받고 나면, 블로그를 생성하고 글을 쓸 수 있습니다. 

이 블로그를 사용하게 된 가장 큰 이유는 <mark style='background-color: #dcffe4'>Code Highlighting</mark> 기능을 사용할 수 있다는 점 때문이고, 이 이외에도 많은 부분에서 CSS, HTML 파일 등을 수정할 수 있습니다. 물론 일정 수준이상의 개발 스킬이 필요하긴 합니다.

<mark style='background-color: #dcffe4'>플러그인</mark> 이라는 개념을 도입하여 블로그에 여러 기능들을 설치할 수 있어, 구글 adsense 를 통한 광고 삽입, 구글 analytics를 통한 통계 기능 등을 활용할 수 있습니다. 

<mark style='background-color: #dcffe4'>모바일 앱도 제공</mark>하여 댓글 알림이나 글쓰기 기능 충분히 활용할 수 있고, 깔끔한 인터페이스를 제공합니다.

<br/>

#### 그외 블로그 서비스

그 외의 많은 블로그 서비스들은 당연하게도 글쓰기 자체에 관심이 많습니다. 특이 brunch 같은 경우 수필을 적어야 적절할 것만 같은 느낌을 많이 받았습니다. 

medium 이나 Blogger 등의 서비스들도 다 좋은 인터페이스를 제공하고, 최근 들어서는 대동소이하게 다양한 기능들도 제공합니다. 모든 서비스가 그렇듯, 조금씩 불편한 점들이 있었고, 또 생각외로 잘 구성되어 있는 장점들도 있어 특이 나쁘다는 느낌은 받지 못하였습니다. 

<br/>

## GitHub Pages

<br/>

#### 블로그를 왜 하는가?

지금 GitHub Pages를 왜 선택했는지를 이야기하자면, 블로그를 왜 하는지부터 정확하게 생각해야 할 것 같습니다. 

S/W 개발 업무를 하고 있어, 검색 서비스를 많이 이용하고 있고, 이렇게 얻은 정보들을 즐겨찾기에 넣어 뒀었습니다. 지금도 수백개의 즐겨찾기가 크롬의 구글 계정을 통해 동기화되어 있는데, 아주 가끔 이 즐겨찾기를 정리하다 보면 페이지가 없어진 것이 많아서 이들을 어딘가에 **남겨두어야 겠다** 는 것이 블로그 작성의 시작이었습니다. 

블로그에 글을 남겨두면 나 아닌 다른 사람들에게도 도움이 된다는 점을 알게 되었고, 심지어는 시간이 지나 내가 내 블로그를 검색하고 있다는 점을 알게 되고나서는, 블로그를 조금 더 다듬고 싶은 생각이 들었고, 다수의 사람들이 방문한다면, 그리고 내가 찾기 힘든 자료들을 잘 알아냈다면, 이를 통해서 광고 수입을 얻어야겠다는 생각도 있었습니다. 

<br/>

#### 왜 GitHub Pages 일까?

GitHub Pages 의 장점을 나열하자면 많겠지만, 가장 큰 이유 중 하나가 markdown 기반 문서입니다. 여기서 설명하는 jekyll 이라는 기능을 사용하지 않더라도 그저 `.md` 나 `.markdown` 확장자를 가진 마크다운 포멧의 문서를 올려두면, 그 포멧을 만들어준다는 점이 좋았습니다. 

현재는 tistory 블로그도 markdown 을 지원합니다.
{: .notice--info}

markdown 을 이용하면 좋은 것이 코드를 삽입할 때에 `` ` ``표시만으로도 코드를 삽입할 수 있습니다. 예를 들어,

```
```java
public class Test{
    public static void main(String[] args){

    }
}
```

이렇게 `` ` `` 세 개를 적고, 그 위에 formatting 이 필요한 언어를 적으면 아래와 같이 Code highlighting이 됩니다.

```java
public class Test{
    public static void main(String[] args){
        
    }
}
```

또 하나의 장점은 폴더 구조를 내 마음대로 구조화할 수 있고, 따라서 사진 첨부 등의 기능을 쓸 때에도 `파일첨부` 버튼으로 이용하는 것이 아니라, 파일을 GitHub 에 Push 하고 이 파일을 그대로 끌어올 수 있다는 점에서 좋았습니다. 

GitHub Pages를 이용하면 `*.github.io/` 라는 URL이 생성되면서 블로그를 만들 수 있습니다. 이렇게 단순히 만들어 놓으면 메뉴 구성 등에서 어려움이 따르기 때문에 jekyll 이라는 것을 이용하면 테마도 사용 가능하고 각종 설정을 통해 메뉴 등을 구성할 수 있습니다. 

<br/>

#### GitHub Pages 이용하기

github pages를 이용하는 것은 github 에서 repository 를 생성하는 것과 동일한 과정입니다. 다만, github pages를 구성할 때에는 반드시 `본인 계정명.github.io` 로 설정해야 정상적으로 동작함을 이해해야 합니다. 

만약 github의 본인 계정이 aaa 인 경우, `aaa.github.io` 형태로 만들어야 정상적으로 동작하게 됩니다. 아마도 수많은 URL을 관리하는데 제한을 준 것으로 보입니다. 

정확히는 사용자 혹은 orgranization 이름 으로 구성되어야 하기 때문에 저 같은 경우 organization 을 생성하여 이름을 `linkeverything.github.io` 로 구성하였습니다. 

github page를 생성하고 `README.md` 파일에 아래와 같이 적어넣고, `https://{본인계정명}.github.io/` 로 접속해 보면 잘 보이는 것을 확인할 수 있습니다. 

```md
## aaa
```

<br/>

## jekyll

<br/>

#### 개요

평소처럼 Google 서치를 하다가 보니, 어떤 블로그가 아주 심플하고 가독성 좋은 구조로 되어 있는 것을 발견하였습니다. 이정도 깔끔한 수준이면 아주 좋겠다라고 생각하면서 살펴보니(github.io 쪽은 해당 github repository로 가서 구조를 볼 수 있습니다) jekyll 이라는 블로그 생성 툴로 구성되어 있다는 것을 발견하였습니다. (github에서 public repository로 구성되어야만 블로그로 구성이 가능합니다)

이 때부터 jekyll에 대해서 검색해보고 그 기능을 보면서 어떻게 사용하는지, 어떤 구조를 보게 되는지를 살펴보게 된 것 같습니다. jekyll은 현재 어떤 블로그들 보다도 간단하면서 강력한 형태로 블로그를 만들 수 있다는 것을 확신하고 있습니다. 

현재 이와 유사한 형태로 `hexo.io`라는 것이 있습니다. 구조도, 사상도 유사하다고 보면 되겠지만 jekyll 이 워낙 documentation이 잘 되어있어 저는 jekyll을 사용하였습니다. 

아래 가이드에 대해서는 [jekyll 홈페이지](https://jekyllrb-ko.github.io/docs/) 를 참고하고 제가 직접 블로그를 만든 내용을 기반으로 설명합니다.

<br/>

#### 준비 작업

다음과 같은 것들이 준비물로 필요합니다. 

- ruby
  
    2.4.0 혹은 그 이상의 버전이 필요합니다.

    ruby는 jekyll 블로그를 최초 설치, 빌드, 운영하기 위한 기초 프로그램입니다. <mark style='background-color: #ffdce0'>만약 github pages를 이용하고 다른 테마를 그대로 활용한다</mark>면 없어도 무방합니다. 이에 대해서는 뒤 쪽에서 더 설명하겠습니다. 

- rubygem

    gem -v 로 설치 확인

- GCC와 Make

    gcc -v, g++ -v 로 설치 확인

    make -v 로 설치 확인

위 내용은 모두 단독 서버에서 jekyll을 이용하여 블로그를 구축하는 경우 필요한 부분입니다. 만약 여기에서 설명하는 것 처럼 github pages를 그대로 활용하고, 테마도 가져와 사용한다면 이 과정은 불필요합니다. (전 그래도 한번 해보긴 했는데..크게 필요성은 없어 보입니다) 아래 사항들이 오히려 더 필요한 부분입니다.

- github pages 에 repository 생성
  
  github pages 에 repository 를 생성하고 이 것이 정상적으로 동작하는지 확인합니다. 가장 기본적으로 되어야 하며 jekyll과는 무관하게 무조건 선행되어야 하는 부분입니다.

- 사용할 jekyll theme 찾기 <mark style='background-color: #ffdce0'>중요</mark>
  
  다른 사용자를 통해서(구글링), 혹은 jekyll 공식 테마 갤러리([링크1](jamstackthemes.dev), [링크2](jekyllthemes.org), [링크3](jekyllthemes.io))를 통해서라도 테마를 구해 놓습니다. 글씨 크기 등은 중요하지 않고 전체적인 구성이 본인이 원하는 구성이어야 향후에도 조정하기 편합니다.

<br/>

#### 설치 및 설정

만약 ubuntu 등 서버 환경에서 직접 jekyll blog를 생성하고 운영하려면 다음의 절차를 따르면 됩니다. 

1. 앞서 ruby 개발환경 등을 설치합니다. 
2. jekyll 과 bundler 젬을 설치합니다.
   ```
   gen install jekyll bundler
   ```
3. myblog 라는 이름으로 블로그를 생성합니다.
   ```
   jekyll new myblog
   ```
4. 해당 디렉토리로 이동하여 블로그를 구동합니다. 
   ```
   cd myblog
   bundle exec jekyll serve
   ```

이렇게 하면 `http://localhost:4000` 에서 블로그가 구동됩니다.

그런데, 만약 직접 구성하는 것이 아닌 github pages를 이용한다면 찾아둔 theme 페이지에서 그 내용을 모두 복사해서 활용하면 됩니다. 

예를 들어 어떤 테마가 맘에 들고, 그 github페이지를 찾았다면 그 repository 의 모든 내용을 다운로드 받아(혹은 fork해도 됩니다), 생성해 두었던 내 github page에 덮어 쓰고 push 합니다. 

github page에서는 jekyll 블로그를 자동으로 감지하여 빌드하기 때문에 만약 push된 내용에 잘못된 내용이 있으면 email로 알림이 오고 그렇지 않다면 해당 페이지에서 그 테마의 기본 화면을 볼 수 있습니다. 

일반적으로 아래 사항에 대해서 꼭 기억하고 있으면 수월합니다. 아래 내용은 기본 사항일 뿐, 테마에 따라서, 혹은 jekyll버전에 따라서 상이할 수 있습니다.

- jekyll블로그는 <mark style='background-color: #ffdce0'>_config.yml</mark> 파일이 기본 설정 파일입니다. 사용자가 변경할 수 있는 설정 정보들은 모두 여기에 지정되기 때문에 여기서 모든 작업을 하면 됩니다. (반대로 다른 파일들은 잘못 건드리면 안됩니다)

- 모든 포스트(posts)는 <mark style='background-color: #ffdce0'>_posts</mark> 폴더 하위에 작성됩니다. 테마에 따라 조금 상이할 수는 있지만, 이 아래에 폴더 구조로 category를 구조화 할 수 있습니다. 구조화하지 않더라도 이 하위에 `.md` 또는 `.markdown` 파일이 있게 되며, 모든 파일명은 `yyyy-mm-dd-{title with dash}.md` 형태가 됩니다.

- 테마 관련한 파일은 <mark style='background-color: #ffdce0'>_sass</mark> 폴더 하위에 `테마명` 으로 들어가게 되므로, 뭔가 css성격의 무엇을 수정하고 싶다면 그 하위에서 수정하면 됩니다. 예를 들어 제가 사용하고 있는 `minimal-mistakes` 테마는 `_sass/minimal-mistakes` 폴더에 위치하고 있으며 그 안에서 `_variables.scss`, `_base.scss`, `_notices.scss`, `_reset.scss` 등을 수정하여 사용하고 있습니다. 

앞서 이야기한 것 처럼 제가 어떤 파일을 어떻게 수정했는지 확인하려면 이 `linkeverything.github.io` 의 repository를 확인해 보면 됩니다. public repository니까 누구나 확인 가능합니다.

<br/>

#### 작성해 보기

가장 중요한 글 작성입니다. jekyll의 기본적인 사항, 그리고 사용하는 테마에 따라서 적절하게 위치시켜야 합니다. 위치(폴더 구조)는 테마에 따라서 다를지 모르나, 기본적인 파일명 페턴은 동일합니다(`yyyy-mm-dd-{title with dash}.md`).

저의 경우 사용되는 이미지들(첨부 파일)을 `/assets` 라는 폴더 하위에 `images` 라는 폴더를 생성하여 구성하였습니다. 이렇게 되면 이미지를 첨부할 때에 다음과 같이 작성하면 됩니다. (jekyll 구조의 root 폴더가 `/`가 됩니다.)

```
![](/assets/images/posts/dev/backend/2020-07-22-kakao-login-1/screenshot-developers.kakao.com-2020.07.22-17_13_33.jpg)
```

<br/>

#### 기타 

폰트를 변경하고, 본문 폭을 조절하는 등의 일반적인 CSS작업에 대해서 본인이 좀 안다고 이것저것 잘못 건들면 큰코 다칩니다.(경험입니다)

해당 테마의 description을 잘 보고 어떤 값을 변경해야 하는지 꼭 확인하시고 변경해야 이상하게 변경되지 않습니다. 저의 경우 `minimal-mistakes` 라는 테마를 사용하고 있고, 이 테마는 `_sass/minimal-mistakes/_variables.scss` 를 수정해야 정확하게 동작합니다. 

<br/>

## 출처 및 참고자료

- <http://jihyeleee.com/blog/second-designer-can-make-jekyll-blog/>
- <https://jekyllrb-ko.github.io/docs/>