---
title: "카카오톡 로그인 구현하기 - (1) API사용 설정"
categories: [Spring Boot]
tag: [spring boot, kakao login]
last_modified_at: 2020-07-22
---
웹이든 앱이든 서비스를 개발하는 데 있어서 SSO(Single Sign On) 의 필요성이 계속 화두가 되는 것 같습니다. 이제 **기존 시스템** 이라는 것이 거의 다 있게 마련이고, 따라서 기존에 사용하던 로그인정보를 그대로 유지해 달라고 하거나, 더 나아가서 session 도 유지해 달라는 요구사항이 더러 있습니다. 다양한 방식의 SSO 를 구현할 수 있고, 프로그램도 많이 있지만, B2C 서비스에서 필수라고 할 수 있는 카카오 계정 로그인 방식에 대해서 설명해 보고자 합니다. 

시리즈로 작성될 것으로 예상되는 이 글은, 맨 아래에 있는 출처의 글을 상당 부분 참고했음을 밝힙니다. 많은 도움이 되었고, 저는 과정에 충실하게 정리하였습니다. 해당 글에는 개념 설명도 더러 해 놓으셨으니 가서 한번 보세요^^
{: .notice--info}

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/kakaoLogin){:target="_blank" .btn .btn--primary}

<br/>

## 카카오 API 사용 등록

Google 서비스 API 를 사용하려고 할 때에도 마찬가지이듯, 카카오 역시 API 를 개발하고, 문서를 만들고, 해당 API를 사용하려면 등록할 수 있는 페이지를 잘 구축해 두었습니다. 그저 가입하고 등록과정만 거치면 누구나 사용할 수 있습니다. 

아마도 여기에는 일일 사용할 수 있는 API에 대해서 제한이 걸려 있을 것입니다. 진짜로 대 고객 서비스를 계획하고 있다면 정식으로 등록 과정을 거쳐야 할 것입니다. 
{: .notice--warning}

<br/>

#### kakao developers 페이지 등록

우선 [kakao developers](https://developers.kakao.com/) 페이지에 접속하여 사용자 등록을 합니다. 한번 가입하면 쭉 사용하게 될 것이고, 메일 주소를 주로 사용하기 때문에 주로 사용하는 메일을 이용해 줍니다. 저는 개발 편의 상 gmail로 등록했습니다. 

![](/assets/images/posts/dev/backend/2020-07-22-kakao-login-1/screenshot-developers.kakao.com-2020.07.22-17_13_33.jpg)

<br/>

#### 애플리케이션 등록하기 

우선 가입을 정상적으로 마치고, 로그인을 하고 나면, 아래 그림에서 처럼, `내 애플리케이션` > `애플리케이션 추가하기` 로 진입합니다.

![](/assets/images/posts/dev/backend/2020-07-22-kakao-login-1/screenshot-developers.kakao.com-2020.07.22-17_21_26.jpg)

아래 정보를 입력합니다. 모두 다 임의로 적어주면 됩니다. 

- 앱 이름 : 임의의 구분할 수 있는 이름으로
- 최사 이름 : 학습 목적이기 때문에 'Personal' 이라고 임의로 적음

![](/assets/images/posts/dev/backend/2020-07-22-kakao-login-1/screenshot-developers.kakao.com-2020.07.22-17_27_48.jpg)

생성하고 나오는 `요약정보` 에서 아래 표시된 REST API 키 항목에 대한 부분을 잘 적어둡니다. 

이 정보는 애플리케이션에 고유하게 주어지는 정보이기 때문에 변경되지 않고, 나중에도 계속 확인이 가능한 정보입니다. 
{: .notice--info}

![](/assets/images/posts/dev/backend/2020-07-22-kakao-login-1/screenshot-developers.kakao.com-2020.07.22-17_29_17.jpg)

<br/>

#### 카카오 로그인 API 사용설정하기

이제 좌측 메뉴 중 `카카오 로그인` 항목으로 이동하여 `활성화 설정` 부분을 ON 으로 바꾸어 줍니다. 

![](/assets/images/posts/dev/backend/2020-07-22-kakao-login-1/screenshot-developers.kakao.com-2020.07.22-17_31_38.jpg)

어떤 API 를 사용하든 다 비슷한 과정으로 설명할 수 있습니다. 애플리케이션이라는 단위로 사용 설정을 하고, 그 앱에서 어떤 API를 사용할지를 각각 설정하게 됩니다. 특히나 여기서 다루는 사용자 정보 부분은 아주 민감한 정보이기 때문에 저렇게 별도의 메뉴로 나와 있습니다. 
{: .notice--warning}

<br/>

#### 정보 제공 동의 항목 활성화 하기

이제 좌측 메뉴 중 `동의항목`으로 이동하여 `프로필 정보`와 `카카오계정(이메일)` 부분을 각각 **필수**, **선택** 동의 항목으로 활성화 해 줍니다. 

![](/assets/images/posts/dev/backend/2020-07-22-kakao-login-1/screenshot-developers.kakao.com-2020.07.22-17_34_26.jpg)

<br/>

#### 플랫폼 추가하기

이제 이 API를 사용할 플랫폼이 무엇인지에 대해서 설정을 하게 됩니다. 아래 그림처럼 `플랫폼` 메뉴로 이동하여 `Web`항목에 (개발 목적이므로) `http://localhost:8080/`을 추가해 줍니다.

![](/assets/images/posts/dev/backend/2020-07-22-kakao-login-1/screenshot-developers.kakao.com-2020.07.22-17_45_14.jpg)

<br/>

#### Redirect URI 설정하기

이 부분은 카카오 계정을 이용해서 SSO 를 성공한 경우에 redirection할 대상 URL을 설정하는 화면입니다. 

일반적으로 이러한 이증 방식들의 대부분이 해당 로그인 페이지(대표 페이지)로 redirection하여 로그인을 하고, 그 이후에는 여기 설정된 redirection url(uri)를 호출해 줍니다. 그러면서 특정 키 값(뒤에서 설명할 access token)을 넣어주게 되고, 그 키값을 이용해서 향후 다른 API들을 사용하게 되는 방식입니다.
{: .notice--info}

아래와 같이 `카카오 로그인` 부분으로 다시 이동해서 페이지 아래 쪽으로 내려가다 보면 Redirect URI 를 설정하는 부분이 있습니다. 여기서는 `http://localhost:8080/login` 으로 설정해 주었습니다. 

![](/assets/images/posts/dev/backend/2020-07-22-kakao-login-1/screenshot-developers.kakao.com-2020.07.22-17_53_02.jpg)

<br/>

여기까지 설정하고 나면 개발자 페이지에서 API를 사용하기 위한 설정은 마무리 된 것입니다. 이 다음부터는 이에 맞추어 Spring project를 간단히 생성하고 테스트해보는 것으로 진행하겠습니다.


## 참고자료 및 출처

- <https://antdev.tistory.com/34>