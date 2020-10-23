---
title: "카카오톡 로그인 구현하기 - (3) OAuth 방식 구현하기"
categories: [dev, backend]
tag: [spring, kakao login]
last_modified_at: 2020-07-23
---
웹이든 앱이든 서비스를 개발하는 데 있어서 SSO(Single Sign On) 의 필요성이 계속 화두가 되는 것 같습니다. 이제 **기존 시스템** 이라는 것이 거의 다 있게 마련이고, 따라서 기존에 사용하던 로그인정보를 그대로 유지해 달라고 하거나, 더 나아가서 session 도 유지해 달라는 요구사항이 더러 있습니다. 다양한 방식의 SSO 를 구현할 수 있고, 프로그램도 많이 있지만, B2C 서비스에서 필수라고 할 수 있는 카카오 계정 로그인 방식에 대해서 설명해 보고자 합니다. 

시리즈로 작성될 것으로 예상되는 이 글은, 맨 아래에 있는 출처의 글을 상당 부분 참고했음을 밝힙니다. 많은 도움이 되었고, 저는 과정에 충실하게 정리하였습니다. 해당 글에는 개념 설명도 더러 해 놓으셨으니 가서 한번 보세요^^
{: .notice--info}

[Download Sample code from GitHub](https://github.com/Simplify-Criss/SampleProjects/tree/master/kakaoLogin){:target="_blank" .btn .btn--primary}

<br/>

## Access token 받아오기

앞서 설명한 과정까지를 하면 `code`라는 `key`에 어떠한 값이 넘어옵니다. 카카오에서 로그인이 성공했을 때에 넘겨주는 값인데, 이 값을 이용해서 access token 과 refresh token 을 다시 요청해서 받아와야 합니다. 이러한 프로세스는 우리가 흔히 이야기하는 OAuth 방식의 기본 사상을 그대로 수용한 것이므로 해당 내용들을 조금 더 찾아보시면 더욱 자세한 내용을 찾아보실 수 있습니다. 

결국 중요한 것은 **access token** 입니다. 로그인 이후에 모든 API 호출에서 사용하는 것이 바로 이 **access token** 입니다. 
{: .notice-warning}

#### controller 수정하여 값 가져오기

앞서 로그인이 성공하고 나면 `/login` 이라는 경로로 `redirect uri` 를 설정해 주었고, 그 결과 controller에 구현해 놓은 해당 method 로 호출이 들어오는 것을 확인할 수 있었습니다. 이 부분을 아래와 같이 수정하여 code값을 받아옵니다. 

```java
    @RequestMapping(value="/login")
    public String login(@RequestParam("code") String code) {
        System.out.println("code : " + code);
        return "index";
    }
```

위와 같이 수정하고 실행한 뒤, 카카오 계정으로 로그인하면 아래와 같이 나오는 것을 확인할 수 있습니다. 

![](/assets/images/2020-07-23-kakao-login-3/screenCapture.png){: .align-center}

<br/>

#### gson 라이브러리 포함시키기

이 이후에 호출하는 대부분의 API들은 거의 대부분 json 형태의 값을 return 하게 되어 있습니다. 따라서 사용 편의를 위해 gson 라이브러리를 포함시키겠습니다. (개인적인 선호도에 따라 jackson 등 본인에게 편한 라이브러리를 사용하면 됩니다.)

pom.xml 파일을 열어 아래 내용을 추가합니다.

```xml
		<dependency>
			<groupId>com.google.code.gson</groupId>
			<artifactId>gson</artifactId>
			<version>2.8.5</version>
		</dependency>
```

<br/>

#### acces token 을 가져오는 service 구현하기

카카오 공식 문서에도 나와 있듯, 저 code값을 가지고 access token 을 가져오는 API는 `/oauth/token` 입니다.

java 파일을 하나 생성하여 아래 내용을 넣어 주었습니다. 기본적인 사항입니다만, `@Service` annotation을 넣으면 Singleton으로 생성되므로 Controller에서도 가져다가 쓸 수 있습니다. 

```java
    public String getAccessToken (String authorize_code) {
        String access_Token = "";
        String refresh_Token = "";
        String reqURL = "https://kauth.kakao.com/oauth/token";

        try {
            URL url = new URL(reqURL);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            //    POST 요청을 위해 기본값이 false인 setDoOutput을 true로
            conn.setRequestMethod("POST");
            conn.setDoOutput(true);

            //    POST 요청에 필요로 요구하는 파라미터 스트림을 통해 전송
            BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(conn.getOutputStream()));
            StringBuilder sb = new StringBuilder();
            sb.append("grant_type=authorization_code");
            sb.append("&client_id=97d39d6e91ff9fdedf425d2586080d4e");
            sb.append("&redirect_uri=http://localhost:8080/login");
            sb.append("&code=" + authorize_code);
            bw.write(sb.toString());
            bw.flush();

            //    결과 코드가 200이라면 성공
            int responseCode = conn.getResponseCode();
            System.out.println("responseCode : " + responseCode);

            //    요청을 통해 얻은 JSON타입의 Response 메세지 읽어오기
            BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
            String line = "";
            String result = "";

            while ((line = br.readLine()) != null) {
                result += line;
            }
            System.out.println("response body : " + result);

            //    Gson 라이브러리에 포함된 클래스로 JSON파싱 객체 생성
            JsonParser parser = new JsonParser();
            JsonElement element = parser.parse(result);

            access_Token = element.getAsJsonObject().get("access_token").getAsString();
            refresh_Token = element.getAsJsonObject().get("refresh_token").getAsString();

            System.out.println("access_token : " + access_Token);
            System.out.println("refresh_token : " + refresh_Token);

            br.close();
            bw.close();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

        return access_Token;
    }
```

위에서도 다음 사항을 본인 환경에 맞게 수정해 주어야 합니다. 
- `client_id` 부분의 값은 본인이 등록한 `REST API 키` 값으로 변경해 줍니다. 

<br/>

#### controller를 수정하기

이제 만들어둔 위 Service를 호출하여 access token 등을 받아오도록 수정해 보겠습니다. 다시 Controller 파일을 열어 다음과 같이 수정합니다.

```java
    @RequestMapping(value="/login")
    public String login(@RequestParam("code") String code) {
        System.out.println("code : " + code);

        String access_Token = kakaoAPI.getAccessToken(code);
        System.out.println("access_Token : " + access_Token);
    }
```

<br/>

#### 실행 후 결과 확인

이제 여기까지 수정한 사항을 가지고 실행하여 로그인 테스트 합니다. console 창에 나타난 로그를 잘 보면 다음과 같이 나타날 것입니다.

![](/assets/images/2020-07-23-kakao-login-3/screenCapture2.png){: .align-center}

<br/>

#### 정보 가져오기

이제 앞서 가져온 access token 값을 이용해서 사용자 정보를 가져와 보도록 하겠습니다. 앞서 권한 처리에 대해서 설정을 해준 것 처럼 해당 동의가 없으면 사용자 정보를 가져올 수 없게 처리되어 있습니다.

다시 Service 파일을 열어 아래 사항을 추가합니다.

```java
    public HashMap<String, Object> getUserInfo (String access_Token) {

        //    요청하는 클라이언트마다 가진 정보가 다를 수 있기에 HashMap타입으로 선언
        HashMap<String, Object> userInfo = new HashMap<>();
        String reqURL = "https://kapi.kakao.com/v2/user/me";
        try {
            URL url = new URL(reqURL);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod("POST");

            //    요청에 필요한 Header에 포함될 내용
            conn.setRequestProperty("Authorization", "Bearer " + access_Token);

            int responseCode = conn.getResponseCode();
            System.out.println("responseCode : " + responseCode);

            BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));

            String line = "";
            String result = "";

            while ((line = br.readLine()) != null) {
                result += line;
            }
            System.out.println("response body : " + result);

            JsonParser parser = new JsonParser();
            JsonElement element = parser.parse(result);

            JsonObject properties = element.getAsJsonObject().get("properties").getAsJsonObject();
            JsonObject kakao_account = element.getAsJsonObject().get("kakao_account").getAsJsonObject();

            String nickname = properties.getAsJsonObject().get("nickname").getAsString();
            String email = kakao_account.getAsJsonObject().get("email").getAsString();

            userInfo.put("nickname", nickname);
            userInfo.put("email", email);

        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

        return userInfo;
    }
```

<br/>

#### controller 에서 해당 내용 호출하기 

다시 controller 를 열어 아래 내용을 추가합니다. 

```java
    @RequestMapping(value="/login")
    public String login(@RequestParam("code") String code, HttpSession session) {
        System.out.println("code : " + code);

        String access_Token = kakaoAPI.getAccessToken(code);
        System.out.println("access_Token : " + access_Token);
        
        HashMap<String, Object> userInfo = kakaoAPI.getUserInfo(access_Token);
        System.out.println("login Controller : " + userInfo);

        //    클라이언트의 이메일이 존재할 때 세션에 해당 이메일과 토큰 등록
        if (userInfo.get("email") != null) {
            session.setAttribute("userId", userInfo.get("email"));
            session.setAttribute("access_Token", access_Token);
        }

        return "index";
    }
```

여기서는 앞서 만든 getUserInfo 메소드를 다시 호출하여 사용자 정보를 가져오고, 이를 session 에 저장하는 로직이 있습니다. 카카오 계정에서는 키가 될 만한 정보로 활용할 수 있는 것이 email 이기 떄문에 여기서는 email 을 저장하였습니다.

<br/>

#### 실행 후 확인

실행하여 로그를 확인하면 다음의 정보를 알 수 있습니다.

![](/assets/images/2020-07-23-kakao-login-3/screenCapture3.png){: .align-center}

<br/>

## 로그아웃 구현

로그아웃 과정은 기존에 발급된 access token을 바로 만료시키게 서버에 요청하고, 저장해 두었던 session 정보를 삭제하는 과정입니다.

<br/>

#### logout 서비스 구현하기

앞서 만든 서비스에 다음 method를 추가합니다.

```java
    public void kakaoLogout(String access_Token) {
        String reqURL = "https://kapi.kakao.com/v1/user/logout";
        try {
            URL url = new URL(reqURL);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod("POST");
            conn.setRequestProperty("Authorization", "Bearer " + access_Token);

            int responseCode = conn.getResponseCode();
            System.out.println("responseCode : " + responseCode);

            BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));

            String result = "";
            String line = "";

            while ((line = br.readLine()) != null) {
                result += line;
            }
            System.out.println(result);
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
```

<br/>

#### controller 에서 api 개발

로그아웃은 로그인 버튼에 대해서 dynamic 하게 처리해도 되지만 여기서는 그냥 별도로 처리하도록 하겠습니다.

로그아웃 과정은 
1. session에 저장된 access token을 가져와서, 
2. 서버에 해당 access token에 대한 로그아웃 처리를 진행하고,
3. session 에서 저장된 email 과 access token을 지우는

과정으로 되어 있습니다. 

```java
    @RequestMapping(value="/logout")
    public String logout(HttpSession session) {
        String access_Token = (String)session.getAttribute("access_Token");

        if(access_Token != null && !"".equals(access_Token)){
            kakaoAPI.kakaoLogout(access_Token);
            session.removeAttribute("access_Token");
            session.removeAttribute("userId");
        }else{
            System.out.println("access_Token is null");
            //return "redirect:/";
        }
        //return "index";
        return "redirect:/";
    }
```

<br/>

#### index.jsp 수정하기

화면에서도 logout 처리와 관련한 링크(혹은 버튼)가 있어야 하므로, 다음과 같이 수정합니다. 

```jsp
    <c:if test="${userId ne null}">
        <h1>로그인 성공입니다</h1>
        <input type="button" value="로그아웃" onclick="location.href='/logout'">
    </c:if>
```

<br/>

## 참고자료 및 출처

- <https://antdev.tistory.com/34>