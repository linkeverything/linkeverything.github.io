---
title:      "How to convert string to number, how to cut decimal point from number"
categories: [Old]
last_modified_at:       2015-07-22 23:38:00
tags:
  - javascript
published: false
---

javascript 는 특정 타입을 구분하지 않기 때문에 아래 두 가지 케이스는 동일한 함수를 이용해서 처리할 수 있다.

- 문자를 숫자로 변환하기. 예를 들어, "40"을 숫자 40으로 변환
- 소수점이 있는 실수(float) 숫자를 정수형태(integer)로 변환하기

결국 이를 변환할 경우, 점(.)까지도 문자열로 처리하기 때문인 것으로 생각된다. (필자의 생각이다.)

```js
var a = parseInt("10");
var b = parseInt("10.00");
var c = parseInt("10.33");
var d = parseInt("34 45 66");
var e = parseInt(" 60 ");
var f = parseInt("40 years");
var g = parseInt("He was 40");
var h = parseInt("10",10);
var i = parseInt("010");
var j = parseInt("10",8);
var k = parseInt("0x10");
var l = parseInt("10",16);
```

위와 같은 처리를 했을 때 아래와 같은 결과가 나타난다. 잘 이해가 가지 않을 부부에 대해서는 아래에 설명 해 놓았다. 

```js
10
10 // 소수점 제거
10 // 소수점 제거
34 // 공백 뒤는 문자열로 취급, 제거됨
60 // 문자열로 취급된 공백 제거
40 // 문자열은 제거
NaN // 문자부터 시작하면 모두 제거, 결국 남는 문자 없음, not a number
10 // 10을 두 자리 정수("10")로 변환
10 // 앞에 붙은 0 제거
8 // 최대 2자리이므로 그대로 8
16 // 16진수 문자열은 10진수로 변환됨
16 // 16을 두 자리 문자열로 변환
```

출처 : w3School.com - http://www.w3schools.com/jsref/jsref_parseint.asp 
