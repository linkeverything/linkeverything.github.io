---
title:      "Set and use property value for an object in javascript with jQuery"
categories: [Old]
last_modified_at:       2017-03-05
tags:
  - javascript
  - jQuery
published: false
---

자바스크립트를 사용하는데 jQuery 라이브러리는 빼놓을 수 없는 요소가 되어버렸습니다. 물론 순수 javascript 를 이용한 코딩이 원론적(?)이라서 더욱 선호하는 분들도 있을지 모르겠으나, 네트워크 속도가 워낙 빨라지고, 기기들의 성능이 좋아진 만큼, 많이들 사용하게 된 것 같습니다. 

자바스크립트 사용하는 데, 특정 객체의 프로퍼티를 수정하는 과정은 수없이 일어나는 과정이며, 다양한 방법을 사용하게 됩니다. 물론, 그냥 점(dot, .)을 찍고 넣으면 된다고는 하지만, 다양한 Case들에 대비해서 처리를 해 놓은 함수를 사용하는 것도 좋은 방법 중 하나입니다. 이럴 때 사용하는 것이 jQuery 의 data 함수입니다.


```js
$( "body" ).data( "foo", 52 );
$( "body" ).data( "bar", { myType: "test", count: 40 } );
$( "body" ).data( { baz: [ 1, 2, 3 ] } );
$( "body" ).data( "foo" ); // 52
$( "body" ).data(); 
// { foo: 52, bar: { myType: "test", count: 40 }, baz: [ 1, 2, 3 ] }
```

위 예제는 jQuery 공식 홈페이지 document 문서에서 발췌되었습니다. (https://api.jquery.com/data/)  

data 함수는 다음과 같이 설명할 수 있습니다. 

- 설정: 원하는 property key 명, 설정하고자 하는 값(혹은 object) 두 가지를 넘기면 됩니다. 위 첫번째 두 번째 줄에서 보이는 것 처럼, 'foo' 라는 key 에 '52' 라는 숫자값을 입력해 넣거나, 'bar' 라는 key 에 객체( { myType: "test", count: 40 } ) 를 넘기면서 설정하게 됩니다. 
- 설정2: 설정하기를 원하는 object 를 넘기면 됩니다. javascript 에서 key 값은 String 형태로 되어 있기 때문에, 객체가 첫 번째 인자로 넘어오면 그것을 그대로 설정해 주게 됩니다. 앞서 살펴보았던 assign 함수와 비슷한 성격이라고 볼 수 있을 것 같습니다. 
- 확인: 값을 확인하려면 해당 property key 값을 첫 번째 인자로 넣고 다음 아무것도 넣지 않으면 됩니다. 보통 이러한 방식으로 함수 처리가 되어 있는 것이 jQuery 에서는 일반적입니다. 네 번째 줄에서 보는 것 처럼 'foo' 에 해당하는 값을 확인합니다. 
- 확인2: 값을 모두 확인하려면 아무런 인자도 넘기지 않습니다. 위의 다섯번째 줄을 보면 해당 객체의 모든 속성을 나열하게 됨을 확인할 수 있습니다. 
이렇게 해서 특정 object 의 property 들을 넣고, 확인할 수 있습니다. 

.attribute 함수 등이나, .{property key} 로 직접 확인할 수 있으나, 저렇게 하면 undefined / not defined 처리 등을 더욱 쉽게 처리할 수 있습니다. jQuery 의 내장함수를 이용하는 것은 편리성 측면도 있으나, 코드의 가독성 측면도 있으니 충분히 활용하는 것이 중요하다고 생각합니다.



출처: https://4urdev.tistory.com/25 [Simplify]