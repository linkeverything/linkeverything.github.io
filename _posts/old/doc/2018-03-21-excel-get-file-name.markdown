---
title:      "How to get file name from full path using MS Excel"
categories: [Old]
last_modified_at:       2018-03-21tags:
  - Excel
published: false
---

## 들어가며

개발을 하다보면, 늘어진 내용을 좀 정리해야 하는 경우가 있습니다. 예를 들어 파일 목록은 쭉 가져왔는데, 각 파일명을 뽑아내고 싶다거나 하는 등등으로 말이죠.

물론 리눅스 상에 소스가 업로드 되어 있고, 여기에 SSH접속이 가능한 상황이라면 크게 고민할 필요없이 ls 명령어를 이용해서 추출할 수 있을 겁니다만, 단순 텍스트로 나열되어 있다면 여느 에디터의 '열 편집' 모드를 이용하기도 불편하고 그런 상황이 됩니다. 

사실 전 이런 경우에 간단한 자바 프로그램을 하나 만들어서 main 함수에서 모든 목록을 배열 형태로 받아 이에 대해서 원하는 문자열을 추출하는 방식으로 진행했었습니다. 프로그램을 만들 줄 아는 사람이라면 크게 어려운 방법은 아닌지라, 충분히 활용해 볼 가치는 있습니다.

```java
public class TestClass {
    public static void main(String[] args){
        
        File file = new File("c:\a\b.txt");
        file.getName();
 
    }
}
```

경우에 따라서는 그 간단한 Java 프로그램 하나 만드는 것 조차 여의치 않을 수도 있습니다. 환경 문제이건 기타 등등 어떤 문제로든 말이죠. 이런 경우에 엑셀을 활용할 수만 있다면 엑셀을 이용해서 파일명만을 뽑아낼 수 있을 것입니다. 

## 해결방안

![](/assets/images/posts/old/img/post/2018-03-21-excel-get-file-name/excel-get-file-name-00001.png)

위 캡쳐본 처럼 파일 전체 경로가 쭉 주어졌다고 했을 경우, 그 옆에 아래처럼 수식을 넣으면 됩니다. 

= MID(B2,FIND("*",SUBSTITUTE(B2,"/","*",LEN(B2)-LEN(SUBSTITUTE(B2,"/",""))))+1,LEN(B2))

각 함수들의  의미를 아래와 같이 풀어서 적어보았습니다. 좌측이 각 함수들의 의미이고, 우측이 그 결과입니다.

![](/assets/images/posts/old/img/post/2018-03-21-excel-get-file-name/excel-get-file-name-00002.png)

위 세 줄을 해석하면 다음고 같습니다. 

1. B7의 내용을 변경(SUBSTITUTE) 할 건데, "/"를 찾아서 '*"로 변경할 것이다. 
2. 이 변경의 시작 위치는 총 길이(LEN)에서 /를 공백 치환한 길이를 뺀 것 만큼의 위치부터 시작할 것이다.
3. 그렇게 되면 마지막 "*"의 위치를 찾아내고, 
4. 찾은 위치의 +1 위치부터 마지막까지를 뽑아낸다.

가장 바깥에 있는 MID 함수의 시작을 0, 끝나는 지점을 FIND 함수의 결과값으로 변경 해 주면 경로만을 뽑아내는 것도 가능합니다.


참고 : https://m.blog.naver.com/PostView.nhn?blogId=javaking75&logNo=220533182084&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F

출처: https://4urdev.tistory.com/40 [Simplify]