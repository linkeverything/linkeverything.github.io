---
title:      "JavaScripting Powerful JavaScript with && and ||"
categories: [old]
last_modified_at:       2015-08-05
tags:
  - javascript
---

## 들어가며

Java, JavaScript 개발을 해 본 사람이라면 &&, &#124;&#124; 연산자를 자주 사용할 것이다. 일반적으로 이 두 연산자를 학습할 때 보면, 앞의 항목이 참인지 거짓인지에 따라서 뒤 연산을 하지 않고 넘어가기 때문에, 보다 효율적인 연산자라고 배운다. 아래 예를 보자.

```js
if( a > 0 || b < 0 ) {
 ...
}
```

위와 같은 경우, 앞의 연산자인 a > 0 가 참이라면, 위 연산자에 관계 없이 이 if 문은 참으로 간주되므로, {} 안의 연산을 진행해야 한다. 따라서 뒤 연산인 b < 0 을 체크하지 않으므로, 조금 더 효율적인 연산이 된다. 

```js
if( a > 0 && b < 0 ) {
 ...
}
```

위 연산도 앞의 연산과 마찬가지이다. 위 연산에서 앞의 연산인 a > 0 이 거짓이라면, 뒤 조건의 참/거짓 여부에 관계 없이 거짓이므로, 이 if 문은 넘어가게 된다. 



각설하고, javascript 에서는 이보다 더 강력하게 &&과 &#124;&#124;을 사용할 수 있다. 

## 연산 고급화하기

##### Example 1 : || 연산을 이용

```js
function documentTitle(theTitle) {
​  if (!theTitle) {
    theTitle  = "Untitled Document";
  } else {
    theTitle = theTitle;
  }
}
```

보통은 위와 같은 형태로 처리해서, parameter로 넘어온 theTitle 값이 있는지 여부에 따라서 (undefined 역시 체크함), 있으면 그 값을 전역 변수에 넣고, 그렇지 않으면 문자열 "Untitled Document" 를 전역 변수에 넣는다. 실제 별거 아닌 작업이 이렇게 여러 라인을 보여주면서 불필요한 공간을 차지하게 된다. 이를 아래와 같이 처리하면 아주 간단한 방법으로 고급스러운 javascript를 구현할 수 있다.

```js
function documentTitle(theTitle) {
  theTitle  = theTitle || "Untitled Document";
}
```
위와 같이 처리하면 우선은 앞의 논리 연산을 한다. 그렇게 되면 theTitle 이라는 parameter가 있는지 우선 확인하고 있으면 그 값을 전역 변수인 theTitle에 넣는다. 만약 parameter가 유효하지 않다면(null 이거나 undefined) "Untitled Document"라는 값을 전역 변수에 넣는다.


>JavaScript Falsy Values: null, false, 0 undefined, NaN, and “” (this last item is an empty string).
— Note that Infinity, which is a special number like NaN, is truthy; it is not falsy, while NaN is falsy.
JavaScript Truthy Values: Anything other than the falsy values.



##### Example 2 : && 연산을 이용

Example 1과 비슷한 형태로 아래 code를 조금 더 간략하고 편하게 사용할 수 있다.

```js
function isAdult(age) {
  if (age && age > 17) {
  return true;
}
​else {
  return false;
  }
}
```
아래와 같이 사용하면, 우선 좌측의 age를 검사해서 false이면, 그대로 false를 return 한다. (우측 연산은 하지 않음) 그러나 앞의 연산이 true이면, 우측 연산을 해서 그 결고를 return 한다. 
```js
function isAdult(age) {
   return age && age > 17 ;
}
```


##### Example 3
```js
if (userName) {
  logIn (userName);
}
 else {
   signUp ();
}
```
위 연산을 아래와 같이 처리하면, userName이 true이면 userName 을 parameter 로 이용해서 logIn 이라는 function을 call하고, userName 이 거짓이면 signUp() function을 call 한다.
```js
userName && logIn (userName) || signUp ();
```



##### Example 4
```js
var userID;
​if (userName && userName.loggedIn) {
  userID = userName.id;
}
​else {
  userID = null;
}
```
아래처럼 처리하면 userName 의 유효성을 파악해서 그것이 참이면 userName.loggedIn을 check 한다. 그것도 참이라면 userName.id 를 userID에 넣는다. 만약 userName이 거짓이라면 null 을 userID에 넣는다.
```js
var userID = userName && userName.loggedIn && userName.id
```

출처: https://4urdev.tistory.com/13?category=604669 [Simplify]