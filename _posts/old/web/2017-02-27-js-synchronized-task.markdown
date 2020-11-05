---
title:      "How to do synchronized task AFTER async tasks are done"
categories: [old]
last_modified_at:       2017-02-27
tags:
  - javascript
---

## 들어가며

제목은 거창하나.. 이번에도 팁 수준에서의 글을 작성해 보려고 합니다. javascript는 아직도 웹, 모바일 웹 등에서 자주 사용되고 있고, 어떤 프레임워크를 사용하던지 Javascript 언어 기반으로 jQuery 를 사용하는 것이 가장 일반적인 것 같습니다. (화면을 구성하는데 말이죠.. 어디까지나 제 시각에서...)

그런데 javascript, 즉 jQuery 에서 비동기 Ajax call등을 수행하게 될 때, 특정 call이 무조건 우선해야 하는 경우가 생깁니다.

다음 세 가지 network 작업이 있는 웹 페이지라고 가정을 해 보겠습니다.

1. 사용자 정보를 받아온다. (session 정보, 권한 정보 등..)
2. 기본 코드값, 일반정보를 받아온다. 
3. 상세 목록을 출력하면서 1. 2. 에서 받아온 정보를 확인하여 버튼 등에 권한 처리를 하거나, 기본정보에서 가져와 각 코드값을 맵핑한다. 

단순해 보이는, 일반적인 화면이지만, 이 화면은 순차적으로 불러와도 안되고, async 하게 받아와도 안됩니다. 순차적으로 받아오게되면 동작은 하겠지만 속도 문제가 있고(하나하나 순서대로 받아야 처리되므로 각 시간의 총합만큼 시간이 걸림), 비동기(async) 방식으로 받아오면 순서가 꼬일 가능성이 있습니다. 항상 Ajax call 등의 네트웍크는 어떠한 결과값이 먼저 도착하는지에 대해서 보장할 수 없기 때문입니다. 

## 해결방법

이럴 때 사용하는 방법이 비동기와 동기를 결합하여 사용하는 방법입니다. 이 방식은 jQuery 라이브러리가 반드시 있어야 하고, 그렇지 않은 방법은 더 찾아서 나중에 포스팅 하도록 하겠습니다. 

이 방식은 간단히 다음과 같이 표현할 수 있습니다..

```js
$.when([deferreds]).done(function([results]){});
```

위 항목을 소개하면, 


[deferreds]

- 선행되어야 할 function들을 기술한다. (복수 개 가능)
- 이 항목들은 deferred 라는 함수를 통해서 새로 생성된 deferred 에 결과값을 리턴한다. 
- 이 항목들은 비동기(async) 실행된다. 

[results]

- 각 deferreds 에서 수행 후 내놓은 결과값이 return 되어 들어온다. 
- 객체 형태로 받을 수 있다. 
- 이 results 가 포함된 함수는 deferred 가 모두 실행된 뒤에 실행된다. 


조금 난해하게 보일 수도 있지만, 다음 예제를 보겠습니다. 


```js
function getData(){
 
  var deferred = $.Deferred();
  
  XMLHttpRequest xhr = new XMLHttpRequest();
  xhr.open("GET","data",true);
  
  xhr.addEventListener('load',function(){
    if(xhr.status === 200){
      deferred.resolve(xhr.response); // call done() method
    }else{
      deferred.reject("HTTP error: " + xhr.status);
    }
  },false) 
  
  xhr.send();
  return deferred.promise();
}
 
function getData2(){
 
  var deferred = $.Deferred();
  
  XMLHttpRequest xhr = new XMLHttpRequest();
  xhr.open("GET","data",true);
  
  xhr.addEventListener('load',function(){
    if(xhr.status === 200){
      deferred.resolve(xhr.response); // call done() method
    }else{
      deferred.reject("HTTP error: " + xhr.status);
    }
  },false) 
  
  xhr.send();
  return deferred.promise();
}
 
$.when(getData(), getData2()).done(function(result1, result2){
    console.log(result1, result2);
});
```


위 항목을 풀면, 다음과 같이 설명할 수 있습니다. 


- getData(), getData2() 함수는 동시 실행된다. - 비동기(async)

- 두 작업이 모두 종료된 뒤에 done 내부 함수가 호출되면서 각 함수에서 deferred.resolve 부분에 내보낸 parameter 가 들어오게 된다. 

- 만약 deferred.reject 에 들어온 값을 처리하고 싶으면 .done 말고 .fail 부분을 만들어 처리하면 된다. 

- getData(), getData2() 두 함수는 ; 없이 실행하고, 두 함수 중 오래 걸리는 함수가 끝나는 즉시 done 부분이 실행되므로 시간을 최소화 할 수 있다. 


위와 같은 동작을 잘 활용하면 에러 handling 을 한 곳에서 할 수 있고(.done 부분), async 와 sync 를 잘 구분지어 동작하게 함으로써, 더욱 효과적인 진행이 가능합니다. 실질적으로 처음 언급한 예제에서 보면 deferred 부분에 1, 2 작업을 한 뒤에 3 항목을 .done 함수 부분에서 처리해 주면 됩니다. 


한번 알고 나면 이 부분은 엄청나게 많이 사용되는 것 같습니다. 저도 이 부분으로 다수 처리하면서 업무를 진행하고 있고, 많은 도움이 되고 있습니다. 



출처: https://4urdev.tistory.com/24 [Simplify]