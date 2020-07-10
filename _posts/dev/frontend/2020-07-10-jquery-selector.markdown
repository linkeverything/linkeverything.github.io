---
title: "알아두면 유용한 jQuery selectors"
excerpt: "Some useful jQuery selectors those are useful in development"
categories:
  - "dev"
  - "frontend"
tag:
  - "jquery"
  - "selector"
last_modified_at: 2020-07-10
---

jQuery selector 라고 하면 보통은 ```#```으로 아이디를 검색하거나 ```.```으로 class를 검색하는 것으로 많이 사용합니다. 그런데 조금만 더 높은 수준의 화면을 개발하려면 그에 걸맞는 selector 가 필요합니다. 여기서는 잘 모르는 사람이 많지만, 알아두면 유용한 몇 가지 selector들을 정리해서 소개합니다. 기본적인 id selector와 class selector는 소개하지 않겠습니다.

작성 기준일인 **2020년 7월** 현재 [jQuery 홈페이지](https://api.jquery.com/category/selectors/)에 올라와 있는 내용을 바탕으로 정리합니다. 
{: .notice--info}
  
## attribute 관련 selectors

특정 속성 값을 기준으로 하여 선택하는 것은 아주 흔하고 많이 사용되는 방식입니다. 

| 사용법 | 기능 | 예시 |
|------|------|----|
|```[name|="value"]```|속성의 값이 ```value```와 같거나 하이픈(```-```)으로 구분된 앞 문자열인 경우|```$( "a[hreflang|='en']" )```<br>a태그 중에서 ```hreflang```이라는 속성의 값이 ```en```과 같거나 하이픈(```-```)으로 구분된 앞 문자열인 경우|
|```[name*="value"]```|속성의 값이 ```value```를 포함|```$( "input[name*='man']" )```<br>input태그 중에서 ```name```이라는 속성의 값이 ```man```이라는 문자열을 포함|
|```[name~="value"]```|속성의 값이 ```value```를 단어로 포함(띄어쓰기로 분리)|```$( "input[name~='man']" )```<br>input태그 중에서 ```name```이라는 속성의 값이 ```man```이라는 단어를 포함|
|```[name$="value"]```|속성의 값이 ```value```로 끝|```$( "input[name$='letter']" )```<br>input태그 중에서 ```name```이라는 속성의 값이 ```letter```로 끝|
|```[name="value"]```|속성의 값이 ```value```와 같음|```$( "input[value='Hot Fuzz']" )```<br>input태그 중에서 ```value```라는 속성의 값이 ```Hot Fuzz```와 같음|
|```[name!="value"]```|속성의 값이 ```value```가 아님|```$( "input[name!='newsletter']" )```<br>input태그 중에서 ```name```이라는 속성의 값이 ```newsletter```가 아님|
|```[name^="value"]```|속성의 값이 ```value```로 시작|```$( "input[name^='news']" )```<br>input태그 중에서 ```name```이라는 속성의 값이 ```news```으로 시작|

## child/descendant Selector

| 사용법 | 기능 | 예시 |
|------|------|----|
|```"parent > child"```|부모 요소의 바로 아래에 위치한 자식 선택자를 모두 반환|```$( "ul.topnav > li" )```<br>topnv클래스를 가진 ul 태그 **바로 아래에** 위치한 li 태그들을 선택|
|```"ancestor descendant"```|조상 선택자 하위에 있는 모든 하위 선택자에 해당하는 요소들을 반환|```$( "form input" )```<br>form 태그 하위에 위치한 모든 input 태그를 선택(여러 단계를 거쳐도 선택됨)|

## status 관련

아래 설명하는 선택자는 특정 요소들의 상태값에 대한 선택자입니다. 

| 사용법 | 기능 | 예시 |
|------|------|----|
|```":checked"```|상태가 checked 이거나 selected 인 요소들을 모두 반환<br>checkbox, radio, options 모두에 대해서 동작함|```$( "input:checked" )```<br>input 태그 중 checked 상태인 것들 반환함. checked="checked" 와 checked 만 있는 경우 모두 선택|
|```":disabled"```<br>```":endabled"```|상태가 disabled / endabled 인 것들 반환|```$( "input:enabled" )```<br>input 태그 중 상태가 enabled 인 것들 모두 선택|
|```":hidden"```|hidden 상태인 것들 반환|```$( "div:hidden" )```<br>div 태그 중 hidden 인 것들 선택|

## 기타

| 사용법 | 기능 | 예시 |
|------|------|----|
|```"prev ~ sibling"```|prev 선택자 이후에, 동일한 부모를 갖는, sibling 선택자에 해당하는 요소 반환|```$( "#prev ~ div" )```<br>prev 라는 id 를 가진 요소 이후에 나오면서, 동일한 부모를 가진 요소들을 선택|
|```"prev + next"```|prev 하위에 next 가 있는 조합은 모두 선택됨|```$( "label + input" )```<br>label 하위에 있는 input은 모두 선택됨|

#### 출처 및 참고자료

 https://api.jquery.com/category/selectors/