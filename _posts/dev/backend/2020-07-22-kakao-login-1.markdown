---
title: "카카오톡 로그인 구현하기"
excerpt: "How to login with Kakao account"
categories:
  - "dev"
  - "backend"
tag:
  - "spring"
  - "kakao login"
last_modified_at: 2020-07-22
---

> 웹이든 앱이든 서비스를 개발하는 데 있어서 SSO(Single Sign On) 의 필요성이 계속 화두가 되는 것 같습니다. 이제 **기존 시스템** 이라는 것이 거의 다 있게 마련이고, 따라서 기존에 사용하던 로그인정보를 그대로 유지해 달라고 하거나, 더 나아가서 session 도 유지해 달라는 요구사항이 더러 있습니다. 다양한 방식의 SSO 를 구현할 수 있고, 프로그램도 많이 있지만, B2C 서비스에서 필수라고 할 수 있는 카카오 계정 로그인 방식에 대해서 설명해 보고자 합니다. 

시리즈로 작성될 것으로 예상되는 이 글은, 맨 아래에 있는 출처의 글을 상당 부분 참고했음을 밝힙니다. 많은 도움이 되었고, 저는 과정에 충실하게 정리하였습니다. 해당 글에는 개념 설명도 더러 해 놓으셨으니 가서 한번 보세요^^
{: .notice--info}

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/kakaoLogin){:target="_blank" .btn .btn--primary}

## 카카오 API 사용 등록

Google 서비스 API 를 사용하려고 할 때에도 마찬가지이듯, 카카오 역시 API 를 개발하고, 문서를 만들고, 해당 API를 사용하려면 등록할 수 있는 페이지를 잘 구축해 두었습니다. 그저 가입하고 등록과정만 거치면 누구나 사용할 수 있습니다. 

아마도 여기에는 일일 사용할 수 있는 API에 대해서 제한이 걸려 있을 것입니다. 진짜로 대 고객 서비스를 계획하고 있다면 정식으로 등록 과정을 거쳐야 할 것입니다. 
{: .notice--warning}

#### kakao developers 페이지 등록

우선 [kakao developers](https://developers.kakao.com/) 페이지에 접속하여 사용자 등록을 합니다. 한번 가입하면 쭉 사용하게 될 것이고, 메일 주소를 주로 사용하기 때문에 주로 사용하는 메일을 이용해 줍니다. 저는 개발 편의 상 gmail로 등록했습니다. 

![](/assets/images/screenshot-developers.kakao.com-2020.07.22-17_13_33.jpg)

#### 참고자료 및 출처

https://antdev.tistory.com/34