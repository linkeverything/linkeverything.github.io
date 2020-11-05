---
title:      "How to list-up properties in an object"
categories: [Old]
last_modified_at:       2015-07-29 23:14:00
tags:
  - javascript
published: false
---

Javascript 에서 객체는 모든 변수들을 의미한다. 자주 사용하게 되는 var 변수는 물론이고, function역시 하나의 객체로서 그 실행부를 포함하고 있는 것이다. 

이 객체에서 속성(property) 라고 하는 것을 추가/삭제/조회 하면서 여러 가지 작업을 할 수 있으며, 각 속성이 갖을 수 있는 값 역시 javascript 객체이다. 그런데 실제 어떤 객체를 사용하다 보면, 그 객체가 어떤 종류의 속성들을 가지고 있는지 난해해지는 경우가 있다. 이 때에는 객체가 가지고 있는 속성을 배열로 리턴해 주는 Object.keys() 함수를 활용할 수 있다. 

```js
Object.keys(object) 
```
이 함수에 parameter로 특정 객체를 넣어 호출하게 되면 각 속성 키(key)값이 return 된다. 다음 예를 보면 더욱 쉽게 이해할 수 있다. 

```js
var arr = ['a', 'b', 'c'];
console.log(Object.keys(arr)); // console: ['0', '1', '2']

// array like object
var obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.keys(obj)); // console: ['0', '1', '2']

// array like object with random key ordering
var an_obj = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.keys(an_obj)); // console: ['2', '7', '100']

// getFoo is property which isn't enumerable
var my_obj = Object.create({}, { getFoo: { value: function() { return this.foo; } } });
my_obj.foo = 1;

console.log(Object.keys(my_obj)); // console: ['foo']
```

위 예에서 볼 수 있듯이, 득정 객체에 속성을 다수 집어넣고, Object.keys()를 호출하게 되면, 그 속성의 key 에 해당하는 문자열이 배열로 return 된다. 

- 첫 번째 예에서 보이는 것 처럼, 배열 형태의 객체에 Object.keys()를 호출하게 되면, 배열의 index를 배열 형태로 되돌려 준다. 
- 두 번째 예에서 처럼, 일반적인 객체에 Object.keys()를 호출하면, 각 property의 key 값들을 되돌려 준다.
- 세 번째 예는 return 되는 key 값들이 ordering 되는 것을 확인할 수 있다. 
- 마지막으로 가산 할 수 없는(not enumerable) 형태의 property는 Object.keys() 함수에 나타나지 않는다.

reference : https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/keys

