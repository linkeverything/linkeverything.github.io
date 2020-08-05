---
title: "자바에서 정규식 사용하기 - 전방/후방 탐색"
excerpt: "How to use regular expression with Java"
categories:
  - "dev"
  - "backend"
tag:
  - "java"
  - "regular expression"
last_modified_at: 2020-08-05
---

> 파일 권한(permission) 관리하는 좋은 방법이 있어 공유합니다. 사실 프로그램 내에서 파일의 permission을 직접 관리하는 것은 문제의 소지가 분명히 있지만, 꼭 필요한 경우들이 있어 공유해 두겠습니다.

출처에 있는 정보와 조금 상이하게 동작하는 점이 있어 정리합니다. `Java1.8` 에서 확인된 내용임을 밝힙니다.
{: .notice--info}

여기서는 Java 의 Pattern 객체와 Matcher 객체를 이용한 정규식 사용을 기본 방법으로 사용합니다. 다음 예시와 같은 소스코드를 주로 사용하였으며, 아래 `GitHub` 링크를 따라가면 예시 파일이 있습니다.

```java
Pattern pattern = Pattern.compile(":");
Matcher matcher = pattern.matcher("T:EST");
System.out.println(matcher.find());

true
```

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/regLookAround){:target="_blank" .btn .btn--primary}

<br/>

## 전방탐색 vs. 후방탐색

전방 탐색은 앞에서부터 후방 탐색은 뒤에서부터 특정 문자열을 찾는 방법입니다. 여러가지 방법을 이용할 수 있으나, 여기서는 정규식(regular expression)을 이용한 방법을 설명합니다.

<br/>

#### 전방탐색

우선 앞에서부터 문자열을 찾는 방법입니다. 정규식의 `.+` 를 이용하여 **'문자열이 시작하여.. 어떤 문자가 없거나 있다가..'** 라는 의미의 정규식을 넣어줍니다.

예시 문자열:

```
"http://www.linkeverything.com"
"https://mail.linkeverything.com"
"ftp://ftp.linkeverything.com"
```

```
.+(:)
```

이를 분석하면 앞에서 부터 시작하여 0개 이상의 문자열이 있다가, `:`가 나타나는 순간 거기까지의 문자열을 반환하는 것입니다.

결과:

```
http:
https:
ftp:
```

만약, 기준이 되는 문자열을 제외한 검색을 하고 싶다면, 다음과 같이 `=?` 를 포함하면 됩니다.

첫 번째 예시처럼 `:`를 포함하여 반환하는 경우를 `소비한다(consume)`라고 표현합니다.
{: .notice--info}

```
.+(=?:)
```

결과:

```
http
https
ftp
```

<br/>

#### 후방탐색

전방탐색과 반대의 경우로서, 뒤에서부터 문자열을 찾아갑니다. 사용하는 문자열은 `?<=` 이며 뒤에서부터 탐색합니다. 

예시 문자열:

```
ABC01: $23.45
HGG42: $5.31
CFMX1: $899.00
XTC99: $69.96
Total items found: 4
```

```
(?<=\$)[0-9.]+
```

위에서부터 탐색하며, `$` 문자열로 시작하여 뒤에 `0 ~ 9`나 `점(.)`이 여러 개 나오는 것을 찾아 반환합니다.

결과:

```
23.45
5.31
899.00
69.96
```

<br/>

## 응용하기






## 참고자료 및 출처

- http://minsone.github.io/regex/regexp-lookaround