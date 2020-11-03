---
title: "Java에서 Random String 생성하기 (숫자 나누기 포함)"
categories: [java]
tag: [java, string, integer, random]
last_modified_at: 2020-11-03
published: true
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

![](/assets/images/posts/dev/java/2020-11-03-java-random-string/capture 2020-11-03 PM 05.36.51.png)

<br/>

#### Plain Java 이용 - bounded

이번에는 정해진 범위 내에서 랜덤 문자열을 추출하는 방법을 보겠습니다. 위 방법과 유사하지만 연산하는 과정이 좀 더 있고, 시작과 끝에 해당하는 숫자 값을 알아야 합니다.

아래 예제에서는 문자열 `a` 부터 `z`까지의 문자 중에서 랜덤하게 10개를 추출하는 예제입니다. 시작점에서부터 랜덤하게 생성된 float 값을 종료 문자 까지의 범위 내에서 더합니다. 이렇게 되면 시작~종료 범위에 있는 랜덤한 숫자가 추출되고, 이를 char 로 casting하여 StringBuilder 에 append 해 줍니다.

```java
int leftLimit = 97; // letter 'a'
int rightLimit = 122; // letter 'z'
int targetStringLength = 10;
Random random = new Random();
StringBuilder buffer = new StringBuild(targetStringLength);
for (int i = 0; i < targetStringLength; i++) {
    int randomLimitedInt = leftLimit + (int)
            (random.nextFloat() * (rightLimit - leftLimit + 1));
    buffer.append((char) randomLimitedInt);
}
String generatedString = buffer.toString()
System.out.println(generatedString);
```

결과:

![](assets/images/posts/dev/java/2020-11-03-java-random-string/capture 2020-11-03 PM 05.42.04.png)

<br/>

#### Java 8 을 이용한 알파벳으로 된 랜덤 문자열

Java 8 에서는 

{{ site }}

{{ site.url }}

<br/>

## Comment

랜덤하게 문자열을 생성하는 방법이나 숫자를 생성하는 것 등은 이것들 말고도 다양하게 있을 것입니다. Random 객체가 생성하는 숫자 자체가 실제로는 Random 하지 않다는 의견도 있는 만큼, 이러한 방법들이 항상 최상의 결과를 가져온다고 생각하면 안됩니다.

더불어, 대부분의 방법들이 문자에 해당하는 숫자를 랜덤하게 뽑은 뒤 문자열로 변환하고 이를 모아서 출력해주는 형태를 가지고 있기 때문에, TPS(Transaction Per Second)가 매우 높은 경우 성능에 영향을 줄 수 있다는 것을 반드시 명심해야 합니다.

만약 랜덤하게 생성한 것이 ID처럼 쓰이고, 앞뒤로 날짜나 시간 등을 붙여줄 수 있는 경우라면 간단히 현재시간의 밀리세컨드 값이나, timestamp의 마지막 3자리를 뽑아쓰는 등으로 가볍게 처리하기도 합니다.

<br/>

## 참고자료 및 출처

- <https://www.baeldung.com/java-random-string>
- <https://stackoverflow.com/questions/51018901/java-how-to-divide-an-integer-into-5-random-parts>