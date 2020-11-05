---
title:      "SpringBoot - (7-1) Handler for Login Success"
categories: [old]
last_modified_at:       2018-10-08 10:00
tags:
  - Spring
  - SpringBoot
published: false
---

## 들어가며

앞서 Spring Security 를 구현한 내용에 따르면, 화면에서 넘어온 username 과 password를 이용해서 DB를 조회하고, 조회된 결과 정상적인 사용자이고, 그 사용자의 Authority 정보가 있으면 그것들을 조회해서 별도의 SecurityMember 라는 객체(User, 혹은 UserDetail 을 상속받는)로 만들고 session 을 유지해준 채 로그인 처리를 마무리합니다.

이러한 일련의 과정들은 아주 기본적인 사항만이 적용된 경우이고, 더 상세한 부분에 대해서는 앞으로 하나하나 추가해 나갈 전망입니다. 여기서는 로그인이 성공한 이후에 다음의 과정을 한 뒤에 로그인 완료 처리를 하는 것으로 개발 해 보겠습니다. 

## 구현하기

##### 로그인한 사용자의 IP 정보 저장하기

로그인 이전에 요청했던 URL이 있으면, 로그인이 성공한 뒤, 그 URL로 Redirect 해 주기
위의 두 과정은 약간의 형태만 변경될 뿐, 일반적으로 웹 개발을 하는 데 있어서 많은 부분이 사용됩니다. 예를 들어 사용자 로그인이 완료된 뒤에 그 사용자의 권한을 가져오고(여기까지는 기존 로직), 그 가져온 정보들에 추가적으로 IP등을 가져와서 mapping 해 주고, 별도 처리 (비밀번호를 변경하세요, 기존 URL로 Redirect됩니다, 등.) 를 진행합니다.

사실 이러한 처리를 위해서는 두 가지 경우에 처리로직을 추가할 수 있습니다. authenticationSuccessHandler와 authenticationFailureHandler가 그것입니다. 둘 다 구현체만 만들어주고 앞서 만든 Configure 함수에 추가만 해주면 그럴듯하게 동작합니다. 

##### AuthenticationSuccessHandler 구현하기

파일을 하나 생성하고, 적당히 이름을 넣습니다. 저는 SuccessHandler라고 이름 지었고, AuthenticationSuccessHandler 를 상속받게 만들려다가 SavedRequestAwareAuthenticationSuccessHandler 를 상속받는 것으로 아래와 같이 만들었습니다. 

```java
package com.simplify.sample.handlers;
 
import java.io.IOException;
 
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
 
import org.springframework.security.core.Authentication;
import org.springframework.security.web.authentication.SavedRequestAwareAuthenticationSuccessHandler;
 
import com.simplify.sample.security.domain.SecurityMember;
 
public class CustomLoginSuccessHandler extends SavedRequestAwareAuthenticationSuccessHandler {
 
    public CustomLoginSuccessHandler(String defaultTargetUrl) {
        setDefaultTargetUrl(defaultTargetUrl);
    }
    
    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, 
        Authentication authentication) throws ServletException, IOException {
        
        ((SecurityMember)authentication.getPrincipal()).setIp(getClientIp(request));
        
        HttpSession session = request.getSession();
        if (session != null) {
            String redirectUrl = (String) session.getAttribute("prevPage");
            if (redirectUrl != null) {
                session.removeAttribute("prevPage");
                getRedirectStrategy().sendRedirect(request, response, redirectUrl);
            } else {
                super.onAuthenticationSuccess(request, response, authentication);
            }
        } else {
            super.onAuthenticationSuccess(request, response, authentication);
        }
    }
    
    public static String getClientIp(HttpServletRequest request) {
        String ip = request.getHeader("X-Forwarded-For");
         if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
             ip = request.getHeader("Proxy-Client-IP");
         }
         if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
             ip = request.getHeader("WL-Proxy-Client-IP");
         }
         if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
             ip = request.getHeader("HTTP_CLIENT_IP");
         }
         if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
             ip = request.getHeader("HTTP_X_FORWARDED_FOR");
         }
         if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
             ip = request.getRemoteAddr();
         }
         return ip;
    }
}
```

위에 보이는 것 처럼 onAuthenticationSuccess 함수에서 로그인이 성공했을때에 대한 처리를 해 주었습니다. 저는 로그인 성공 시점에 로그인한 사용자의 IP 정보를 request 를 통해서 알아내, SecureMember 에 넣어주었고, 기존 요청했던 URL이  있다면 그 쪽으로 redirection 해 주는 식으로 개발하였습니다. 

그리고 WebSecurityConfigurerAdapter 에 아래 부분을 추가합니다. 

```java
package com.simplify.sample.security.adapter;
 
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.authentication.AuthenticationSuccessHandler;
 
import com.simplify.sample.handlers.CustomLoginSuccessHandler;
import com.simplify.sample.security.service.CustomUserDetailsService;
 
@EnableWebSecurity
public class CustomWebSecurityConfigurerAdapter extends WebSecurityConfigurerAdapter {
    
    @Autowired
    CustomUserDetailsService customUserDetailsService;
 
    @Bean
    public PasswordEncoder passwordEncoder() {
      return new BCryptPasswordEncoder();
    }
    
    @Bean
    public AuthenticationSuccessHandler successHandler() {
      return new CustomLoginSuccessHandler("/");
    }
    
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring()
        .antMatchers("/openapi/**", "/resources/**");
    }
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable().authorizeRequests().anyRequest().authenticated().and().formLogin().successHandler(successHandler());
    }
 
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(customUserDetailsService).passwordEncoder(passwordEncoder());
    }
 
}
```

##### AuthenticationFailureHandler 구현하기

다음으로 AuthenticationFailureHandler로 구현해 보기로 합니다. 구현채만 만들 뿐, 안에 내용은 만들지 않았습니다. 향후 Logback 설정을 완료하고 나면 그때 로그나 한줄 남기는 것으로 하겠습니다. 

```java
package com.simplify.sample.handlers;
 
import java.io.IOException;
 
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
 
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.authentication.AuthenticationFailureHandler;
 
public class CustomLoginFailureHandler implements AuthenticationFailureHandler {
 
    @Override
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response,
            AuthenticationException exception) throws IOException, ServletException {
        // TODO Auto-generated method stub
 
    }
 
}
```

에제 실행을 하고 로그를 확인해 보면, 아래와 같이 로그인이 성공한 시점에 IP 정보를 정상적으로 받아옴을 알 수 있습니다. 

![](/assets/images/posts/old/img/post/2018-10-08-spring-boot-07-1-login-success-handler/spring-boot-07-1-login-success-handler-00001.png)



GitHub 소스 위치 : https://github.com/Simplify-study/SpringBootSample.git

출처: https://4urdev.tistory.com/43 [Simplify]
