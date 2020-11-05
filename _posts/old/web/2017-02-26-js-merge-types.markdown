---
title:      "How to merge different object in javascript"
categories: [old]
last_modified_at:       2017-02-26
tags:
  - javascript
published: false
---

Javascript 를 사용하다 보면, 두 개의 Object 를 병합해야 하는 경우가 생깁니다. 다양한 경우가 있을 수 있겠지만, 다음 예를 통해서 알아보겠습니다.

```js
var truck = {
    weight: 3.5,
    color: 'red',
    speed: 50,
    availableWeight: 25,
    registerYn: true
}
 
var superCar = {
    weight: 1.8,
    color: 'blue',
    speed: 80,
    zeroToHundred: 3.4,
    convertible: false
}
```

위와 같은 경우, 공통 요소인 weight, color, speed 를 '차(Car)' 의 공통 분모로 가져갈 수 있으므로, 매 객체마다 동일하게 적어주는 것은 개발을 하는 사람에게나, 이를 Review 하는 사람에게나 모두 부담스러운 일이다. 따라서 이런 경우에는 이를 통합하여 Java에서 말하는 부모 클래스, 혹은 추상화 과정을 통해 공통 요소를 통합하고, 여기에 특정 요소들만을 추가하여 사용하는 것이 효과적이다. (이렇게 되면 공통 요소가 무엇인지도 명확하게 눈에 들어오게 된다)

이러한 경우에 사용하는 Javascript 함수가 바로 assign 함수이다. 

Object.assign() 함수는 첫 번째 Object 에 그 다음 Object (들)을 병합해 주는 것으로서, 위의 코드를 아래와 같은 방법으로 재 작성할 수 있다.  

```js
var car = {
    weight: 3.5,
    color: 'red',
    speed: 50
}
 
var truckSpecific = {
    availableWeight: 25,
    registerYn: true
}
 
var superCarSpecific = {
    zeroToHundred: 3.4,
    convertible: false
}
 
var truck = Object.assign(car, truckSpecific);
var superCar = Object.assign(car, superCarSpecific);
``` 

(위 예제는 워낙 간단한 예제이기 때문에 오히려 복잡도가 증가한 것 처럼 느껴지지만, 적절한 때에 사용하면 훨씬 깔끔하고 좋은 코드가 완성됨을 확인할 수 있다. 

사실 필자는 assign 함수를 위 예제와 같은 경우에 사용한다기 보다는, Ajax call parameter 처럼 경우에 따라 Dynamic 하게 parameter 를 처리해야 하는 경우에 자주 사용한다. 공통된 요소 - caller, sessionId 등 - 은 공통으로 뽑아놓고 사용하는 반면, 경우에 따라서 추가적인 parameter 들은 각 함수에서 dynamic 하게 변경하여 parameter object 로 추가하여 넘긴다. 위와 같은 경우가 이런 Ajax call에 유용한 이유는, 특정 요소가 null이나 undefined, 혹은 ''(공백문자) 로 호출되어서는 안 되고, 아얘 존재하지 않아야 깔끔하게 처리되는 경우 유용하다. 

위 경우, 모든 자동차에 대한 속성들을 모두모두 모아서, 하나의 객체에 다 넣어 놓고, 트럭에 해당되지 않는 제로백 항목은 제외한 채 사용하면 될 것 같으나, 실질적으로 트럭에 제로백 항목 자체가 없어야 깔끔하게 처리되는 경우에는 위와 같이 처리하는 것이 훨씬 깔끔한 코딩 형태가 된다고 할 수 있다. 

또한 Assign 함수는 병합(추가)하고자 하는 Object 들이 복수 개 들어갈 수 있으므로, 부모-자식 관계를 여러 개층으로 구분하여 작성하는 것 또한 가능하다고 생각한다. 사실 Object 쪽 함수들을 자주 사용하지 못하고, 접할 기회도 없는 것이 사실이지만, 조금 알아보면 사용하기 편리한 함수들이 정말 많이 있다. 


그런데, 이 함수에는 주의해야 할 사항이 있습니다. 

병합을 당하는 객체 역시 변화한다는 점입니다. (따라서 사실 저 함수는 잘못된 예제입니다.)

위 예제를 진행한 뒤에 각 객체를 찍어보면...

```js
var car = {
    weight: 3.5,
    color: 'red',
    speed: 50
}
 
var truckSpecific = {
    availableWeight: 25,
    registerYn: true
}
 
var superCarSpecific = {
    zeroToHundred: 3.4,
    convertable: false
}
 
var truck = Object.assign(car, truckSpecific);
var superCar = Object.assign(car, superCarSpecific);
 
console.log(truck);  
// {weight: 3.5, color: 'red', speed: 50, availableWeight: 25, registerYn: true}
console.log(superCar); 
// {weight: 3.5, color: 'red', speed: 50, availableWeight: 25, registerYn: true, zeroToHundred: 3.4, convertable: false }
```

이런 결과가 나옵니다. 전혀 의도하지 않은 결과를 나타내고 있지요... 결국 병합 당하는 객체 자체도 영향을 받기 때문에, 이러한 경우에는 아래와 같은 트릭(?) 을 사용해야 합니다...

```js
var car = {
    weight: 3.5,
    color: 'red',
    speed: 50
}
 
var truckSpecific = {
    availableWeight: 25,
    registerYn: true
}
 
var superCarSpecific = {
    zeroToHundred: 3.4,
    convertable: false
}
 
var truckTarget = JSON.parse(JSON.stringify(car));
var superCarTarget = JSON.parse(JSON.stringify(car));
 
var truck = Object.assign(truckTarget, truckSpecific);
var superCar = Object.assign(superCarTarget, superCarSpecific);
 
console.log(truck);  
// {weight: 3.5, color: 'red', speed: 50, availableWeight: 25, registerYn: true}
console.log(superCar); 
// {weight: 3.5, color: 'red', speed: 50, zeroToHundred: 3.4, convertable: false }
```

이렇게 되면.. 기존에 원했던 것 처럼 다양한 형태로 이를 사용할 수 있을 겁니다. 타깃 오브젝트는 변화시키지 않고, 객체를 다양하게 활용할 수 있는 예제라고 할 수 있습니다..



출처: https://4urdev.tistory.com/22 [Simplify]