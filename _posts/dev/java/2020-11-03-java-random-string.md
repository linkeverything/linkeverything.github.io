---
title: "Java에서 Random String 생성하기 (숫자 나누기 포함)"
categories: [java]
tag: [java, string, integer, random]
last_modified_at: 2020-11-03
---
Java 에서 종종 필요한 기능 중 하나가 랜덤 문자열 생성입니다.

예를 들어 카카오톡 같은 대화방을 생성할 때에 순차 생성하는 것이 아니라 랜덤 문자열을 생성하고 거기에 timestamp 를 붙인다던지 하여 이론적으로 완벽하게 유니크한 문자열을 생성해 낸다거나, 혹은 무작위로 만들어진 값을 이용하여 어떤 ID로 만든다거나 하는 등으로 필요한 기능일 경우가 있습니다. 

여기선 몇 가지 랜덤 문자열 생성 방법을 정리하고, 마지막으로는 숫자를 랜덤하게 나누는 것도 정리합니다.

<br/>

## 문자열 나누기

<br/>

#### Plain Java 이용 - unbounded

아래는 7자리로 이루어진 랜덤 문자열을 생성하는 예제입니다. 문자열은 byte 값에 대응할 수 있으므로, 7개 byte 배열을 우선 생성합니다. 그리고 그 배열을 인자로 받는 nextBytes()함수를 호출하면서 난수를 생성합니다. 그 이후에 String 생성자 중 해당 배열과 encoding에 해당하는 character set을 활용하는 문자열을 생성합니다. 첫 번째 배열 생성시 넣어준 숫자 7 대신 다른 숫자를 입력하여 랜덤 생성된 문자열의 길이를 정의할 수 있습니다.

여기서 'unbounded' 라고 언급한 것은 문자열의 범위를 한정하지 않았기 때문입니다. byte array에 들어있는 byte값은 완벽한 난수 값들로 특정 범위를 생성하지 않아 흔히 말하는 알파벳 등 이외에 너 넓은 범위의 문자열을 생성합니다.

```java
byte[] array = new byte[7]; // length is bounded by 7
new Random().nextBytes(array);
String generatedString = new String(array, Charset.forName("UTF-8"));

System.out.println(generatedString);
```

결과:

![](2020-11-03-java-random-string/capture%202020-11-03%20PM%2005.09.25.png)


<br/>

#### 



<br/>

## 참고자료 및 출처

- <https://www.baeldung.com/java-random-string>
- <https://stackoverflow.com/questions/51018901/java-how-to-divide-an-integer-into-5-random-parts>