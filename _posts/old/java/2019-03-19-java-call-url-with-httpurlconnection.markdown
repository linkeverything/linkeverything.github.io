---
title:      "How to request HTTP url with HttpURLConnection"
categories: [Old]
last_modified_at:       2019-03-19 00:00 tags:
  - java
published: false
---

## 들어가며

Spring에서 Maven 으로 RestTemplate를 이용하지 않고 순수하게 Java에서의 Class를 이용해서 HTTP 호출하는 예제를 찾아 공유합니다.

## 구현방법

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
 
public class Test01 {
    public static void main(String[] args) {
        BufferedReader in = null;
 
        try {
            URL obj = new URL("http://www.test.co.kr/test.jsp"); // 호출할 url
            HttpURLConnection con = (HttpURLConnection)obj.openConnection();
 
            con.setRequestMethod("GET");
 
            in = new BufferedReader(new InputStreamReader(con.getInputStream(), "UTF-8"));
        
            String line;
            while((line = in.readLine()) != null) { // response를 차례대로 출력
                System.out.println(line);
            }
        } catch(Exception e) {
            e.printStackTrace();
        } finally {
            if(in != null) try { in.close(); } catch(Exception e) { e.printStackTrace(); }
        }
    }
}
```

출처 : https://overcome26.tistory.com/77 http://nine01223.tistory.com/229