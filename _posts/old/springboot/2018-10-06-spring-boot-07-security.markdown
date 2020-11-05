---
title:      "SpringBoot - (7) Security"
categories: [old]
last_modified_at:       2018-10-06
tags:
  - Spring
  - SpringBoot
published: false
---

## 들어가며

지금까지 구현한 Spring Boot는 아주 기본적인 서버 프로그래밍이라고 할 수 있습니다. 서버는 Client가 조회해 달라고 요청(Request)하는 시점에 적절한 데이터를 DB에서 조회해 주는 역할이 기본이라고 할 수 있습니다. 그런데 이렇게 서버를 구현해 놓고 Client 가 폐쇄망이 아닌 공개된 곳에서 요청이 가능해야 한다면, 당연히 서버도 공인 IP 를 가지고 동작해야 합니다. 이렇게 되면 악성 Client 에 의해서 공격받고 서버가 부하를 견디지 못해 멈춰버리게 될 겁니다.

악의적인 요청을 막기 위해서 흔히 사용하는 것이 로그인 처리와 Session 처리입니다. 여기서는 이러한 부분을 가능하게 해 주는 Spring Security 설정에 대해서 설명합니다. 



## Security 구현하기

##### Database - Table 생성하기

DB명은 상관없으나, 사용자와 그에 해당하는 auth 정보를 가진 테이블 두 개를 생성하도록 하겠습니다. 다른 방식으로 해도 무관하지만 일반적으로 포함시키는 컬럼들을 포함시켜 두었습니다. 아래 Query를 참고하세요.


```sql
CREATE TABLE `user` (
    `username` VARCHAR(20) NULL DEFAULT NULL,
    `password` VARCHAR(500) NULL DEFAULT NULL,
    `name` VARCHAR(20) NULL DEFAULT NULL,
    `isAccountNonExpired` TINYINT(1) NULL DEFAULT NULL,
    `isAccountNonLocked` TINYINT(1) NULL DEFAULT NULL,
    `isCredentialsNonExpired` TINYINT(1) NULL DEFAULT NULL,
    `isEnabled` TINYINT(1) NULL DEFAULT NULL
)
COLLATE='utf8_general_ci'
ENGINE=InnoDB;
```

```sql
CREATE TABLE `authority` (
    `username` VARCHAR(20) NULL DEFAULT NULL,
    `authority_name` VARCHAR(20) NULL DEFAULT NULL
)
COLLATE='utf8_general_ci'
ENGINE=InnoDB;
```

위에서 보면 username 을 기준으로 두 table을 join 하여 사용자에 대한 authority_name 들을 가져올 수 있는 것을 확인할 수 있습니다. Table을 생성하고 나면 아래 query를 실행하여 데이터를 넣어줍니다. 

```sql
INSERT INTO `authority` (`username`, `authority_name`) VALUES
    ('abc', 'ADMIN'),
    ('abc', 'USER');
 
INSERT INTO `user` (`username`, `password`, `name`, `isAccountNonExpired`, `isAccountNonLocked`, `isCredentialsNonExpired`, `isEnabled`) VALUES
    ('abc', '$2a$10$zNM1N.WnfC1Sq.vkqieCnuEfE3sZ3Hwo6.ytaSBtFTyg33qr2oI2G', 'ABC', 1, 1, 1, 1);
```

데이터의 내용을 잠깐 살펴보자면, abc라는 username 을 가진 ABC 계정의 password는 '$2a$....' 이고, 이에 해당하는 authority 들은 ADMIN, USER 가 있다는 의미입니다. 

##### Domain 생성하기 

테이블에 맞는 Domain (VO객체)를 생성하도록 하겠습니다. 그저 각 테이블에 있는 컬럼들을 Camel 표기법으로 변경하여 적어주면 되는 것이고, 간단히 아래와 같이 생성합니다. Authority 테이블에 대한 VO는 생성하지 않겠습니다. 두 컬럼 뿐인데, 하나는 key로 사용되고 조회 결과는 그대로 Stirng Array로 받는 것으로 가정합니다.

```java
package com.simplify.sample.security.domain;
 
import java.util.Collection;
 
public class Member {
 
    private String username;
    
    private String password;
    
    private String name;
    
    private boolean isAccountNonExpired;
    
    private boolean isAccountNonLocked;
    
    private boolean isCredentialsNonExpired;
    
    private boolean isEnabled;
    
    public String getUsername() {
        return username;
    }
 
    public void setUsername(String username) {
        this.username = username;
    }
 
    public String getPassword() {
        return password;
    }
 
    public void setPassword(String password) {
        this.password = password;
    }
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public boolean isAccountNonExpired() {
        return isAccountNonExpired;
    }
 
    public void setAccountNonExpired(boolean isAccountNonExpired) {
        this.isAccountNonExpired = isAccountNonExpired;
    }
 
    public boolean isAccountNonLocked() {
        return isAccountNonLocked;
    }
 
    public void setAccountNonLocked(boolean isAccountNonLocked) {
        this.isAccountNonLocked = isAccountNonLocked;
    }
 
    public boolean isCredentialsNonExpired() {
        return isCredentialsNonExpired;
    }
 
    public void setCredentialsNonExpired(boolean isCredentialsNonExpired) {
        this.isCredentialsNonExpired = isCredentialsNonExpired;
    }
 
    public boolean isEnabled() {
        return isEnabled;
    }
 
    public void setEnabled(boolean isEnabled) {
        this.isEnabled = isEnabled;
    }
 
}
```

코드가 좀 길긴 하지만, 거의 대부분이 getter/setter들이고, 향후 Spring Security에 있는 User와의 혼동을 방지하기 위해서 Member라는 이름으로 생성하였습니다. 이제 DB를 조회하는 연결고리를 만들어 주도록 하겠습니다.

##### Mapper 구현하기 

Spring Boot 에서 사용하는 DB조회는 Dao라는 것을 사용하지 않고, 최근에는 Mapper라는 것을 이용한다고 앞서 이야기했습니다. 여기서도 mapper를 구현하여 사용자를 조회하고, 각각에 대한 authority들을 조회하여 로그인에 사용될 User 정보를 생성하는 부분을 만들려고 합니다. 우선 Mapper를 생성해 보겠습니다 아래와 같이 interface를 생성합니다.


```java
package com.simplify.sample.security;
 
import java.util.List;
 
public interface UserMapper {
 
    public Member readUser(String username);
 
    public List<String> readAuthority(String username);
}
```

이 Mapper에는 readUser라는 id를 갖는 query하나와, readAuthority라는 id의 query가 있을 것입니다. 둘 다 username을 인자로 받고, 하나는 Member를 단건 조회하고, 다른 하나는 그 username에 해당하는 authority를 리스트로 조회합니다.

/src/main/resources/mybatis/mapper/ 경로에 UserMapper.xml 파일을 생성하고 아래와 같이 입력합니다. 여기서 위에서 생성한 mapper interface의 위치를 mapper namespace 속성에, readUser id 를 갖는 select 구분의 resultType에 앞서 만든 Member 클래스를 적절히 잘 할당해 줍니다. (이게 맞지 않으면 구동 시점부터 에러를 발생시키는 경우가 많으므로, 주의하여 작성합니다.)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.simplify.sample.security.UserMapper">
    <select id="readUser" parameterType="String" resultType="com.simplify.sample.security.Member">
        SELECT * FROM user WHERE username = #{username}
    </select>
    <select id="readAuthority" parameterType="String" resultType="String">
        SELECT authority_name FROM authority WHERE username = #{username}
    </select>
</mapper>
```

##### Controller 생성하여 확인하기

여기까지 진행한 뒤에, 아래와 같이 Controller를 추가해서 각 DB 조회가 잘 되는지 우선 확인합니다. 향후에 Security Library를 포함시키고 나면, DB조회 자체에서 오류가 발생하는지, 아니면 로직에 문제가 있는지조차 확인이 복잡해지기 때문에 우선 확인하고 넘어가는 것으로 하겠습니다. 

아래 부분에서 앞서 학습한 @ResponseBody 부분을 주의해서 살피고, @PathVariable 부분은 경로에 포함된 문자열을 파라메터로 처리하는 부분입니다. 참고로 알아두면 좋을 것 같습니다. 저는 향후에 Security가 적용된다고 하더라도 이 부부은 open해 둘 생각이므로, 앞에 /openapi 라는 경로를 붙여 주었습니다. 

```java
package com.simplify.sample.security.controller;
 
import java.util.List;
 
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
 
import com.simplify.sample.security.domain.Member;
import com.simplify.sample.security.mapper.UserMapper;
 
@Controller
public class UserTestController {
 
    @Autowired
    UserMapper userMapper;
    
    @RequestMapping("/openapi/readUser/{username}")
    public @ResponseBody String  openApiReadUser(@PathVariable String username) {
        Member member = userMapper.readUser(username);
        return member.getName();
    }
    
    @RequestMapping("/openapi/readAuthority/{username}")
    public @ResponseBody String  openApiReadAuthority(@PathVariable String username) {
        List<String> auths = userMapper.readAuthority(username);
        
        StringBuffer buf = new StringBuffer();
        for(String auth : auths) {
            buf.append(auth);
            buf.append(" ");
        }
        return buf.toString();
    }
}
```

![](/assets/images/posts/old/img/post/2018-10-06-spring-boot-07-security/spring-boot-07-security-00001.png)

![](/assets/images/posts/old/img/post/2018-10-06-spring-boot-07-security/spring-boot-07-security-00002.png)


##### Dependency 추가

이제 Dependency를 추가하겠습니다. 기본적인 Spring, Spring Boot 에는 Security 항목이 포함되어 있지 않습니다. pom.xml 을 열어, 아래 항목을 추가합니다. 

```xml
        <!-- SECURITY -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <!-- SECURITY -->
```

참고로 말씀드리면, 위와 같이 dependency 를 추가한 경우, 아래처럼 여러개의 library 가 추가됩니다. 아래 중, Core부분과 화면 부분에 대한 것을 나눠서 넣어 주어도 상관없습니다만, 편의상 모두를 추가하고 진행하겠습니다. 

![](/assets/images/posts/old/img/post/2018-10-06-spring-boot-07-security/spring-boot-07-security-00003.png)

##### Member Class 수정하기

Dependency를 수정했으면, 아래와 같이 Member를 수정합니다. 아래에서 설명할 Security의 User를 구현하는 과정에서 어차피 Authority정보가 필요하고 이를 활용하기 위한 방법입니다. 이 부부은 조금씩 다르게 변형해서 프로젝트 상황이나 구현 방식에 따라서 조금 다르게 구현될 수도 있겠습니다. 아래 노란색으로 표시된 부분을 추가합니다.

```java
package com.simplify.sample.security.domain;
 
import java.util.Collection;
 
import org.springframework.security.core.GrantedAuthority;
 
public class Member {
 
    private String username;
    
    private String password;
    
    private String name;
    
    private boolean isAccountNonExpired;
    
    private boolean isAccountNonLocked;
    
    private boolean isCredentialsNonExpired;
    
    private boolean isEnabled;
    
    private Collection<? extends GrantedAuthority> authorities;
 
    public String getUsername() {
        return username;
    }
 
    public void setUsername(String username) {
        this.username = username;
    }
 
    public String getPassword() {
        return password;
    }
 
    public void setPassword(String password) {
        this.password = password;
    }
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public boolean isAccountNonExpired() {
        return isAccountNonExpired;
    }
 
    public void setAccountNonExpired(boolean isAccountNonExpired) {
        this.isAccountNonExpired = isAccountNonExpired;
    }
 
    public boolean isAccountNonLocked() {
        return isAccountNonLocked;
    }
 
    public void setAccountNonLocked(boolean isAccountNonLocked) {
        this.isAccountNonLocked = isAccountNonLocked;
    }
 
    public boolean isCredentialsNonExpired() {
        return isCredentialsNonExpired;
    }
 
    public void setCredentialsNonExpired(boolean isCredentialsNonExpired) {
        this.isCredentialsNonExpired = isCredentialsNonExpired;
    }
 
    public boolean isEnabled() {
        return isEnabled;
    }
 
    public void setEnabled(boolean isEnabled) {
        this.isEnabled = isEnabled;
    }
 
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }
 
    public void setAuthorities(Collection<? extends GrantedAuthority> authorities) {
        this.authorities = authorities;
    }
    
}
```

##### org.springframework.security.core.userdetails.User 를 상속받는 Domain 생성하기

기존에 작성한 Member는 이 프로젝트, 테이블에 맞게 구현된 사용자 정보이고, Spring에서 인정하는 User 형태는 별도로 존재합니다. 이 것이 org.springframework.security.core.userdetails.User 이고 이를 상속한 Domain을 만들면서 앞서 만든 Member 와 Mapping해주는 소스를 생성합니다. 그저 별도로 Class를 하나 더 만든다고 생각할 수 있겠습니다. 

사실 Member 자체를 org.springframework.security.core.userdetails.User 를 상속받게 하여 구현하면서 둘로 만들지 않고 하나로 합칠수도 있겠지만, 그렇게 하면 혼란을 야기할 것 같아, 두 개로 나눠서 구성하는 것으로 진행하겠습니다. 

```java
package com.jonghun.example.e05.service;
 
import org.springframework.security.core.userdetails.User;
 
public class SecurityMember extends User{
 
    private static final long serialVersionUID = 1L;
    
    private String ip;
    
    public SecurityMember(Member member) {
        super(member.getUsername(), member.getPassword(), member.getAuthorities());
    }
 
    public String getIp() {
        return ip;
    }
 
    public void setIp(String ip) {
        this.ip = ip;
    }
 
}
```

앞서 만든 Member 를 생성자의 인자로 받아 User 객체를 생성하는 것으로 구현하되, 여기서 Customizing 하여 ip정보를 추가로 넣을 수 있도록 하였습니다. 향후 각 Controller등에서 사용할 User는 이 SecurityMember를 사용하게 되므로, 여기에 필요한 정보를 포함해 두어야 꺼낼 수 있습니다.

##### Service 구현하기

UserDetailsService를 상속받는, 구현체를 하나 만들어야 합니다. 여기서 만든 class 에서 중요한 것은 loadUserByUsername 함수입니다. username 을 인자로 받아, 이에 해당하는 사용자가 있는지를 구현하는 부분입니다. 이 부분에서 정상적인 User를 return 하지 않으면 정상적으로 로그인이 되지 않은 것으로 판단합니다.

```java
package com.simplify.sample.security.service;
 
import java.util.ArrayList;
import java.util.List;
 
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;
 
import com.simplify.sample.security.domain.Member;
import com.simplify.sample.security.domain.SecurityMember;
import com.simplify.sample.security.mapper.UserMapper;
 
@Service
public class CustomUserDetailsService implements UserDetailsService{
    
    private static final String ROLE_PREFIX = "ROLE_";
    
    @Autowired
    UserMapper userMapper;
 
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        
        Member member = userMapper.readUser(username);
        if(member != null) {
            member.setAuthorities(makeGrantedAuthority(userMapper.readAuthority(username)));
        }
        return new SecurityMember(member);
    }
    
    private static List<GrantedAuthority> makeGrantedAuthority(List<String> roles){
        List<GrantedAuthority> list = new ArrayList<>();
        roles.forEach(role -> list.add(new SimpleGrantedAuthority(ROLE_PREFIX + role)));
        return list;
    }
 
 
}
```

위에서 주의해야 할 부부은 SimpleGrantedAuthority 객체를 생설할 때, 'ROLE_' 를 앞에 붙여주어서 Spring이 이해할 수 있게 합니다. (사실, 이 부분은 DB에 그냥 ROLE_ 를 붙여서 데이터를 넣어도 무방하나, 데이터를 봤을 때 의미가 와닿게 하기 위해서 prefix를 분리하는 것으로 하겠습니다. 

##### WebSecurityConfigurerAdapter 상속받는 Adapter 구현하기

다음으로는 WebSecurityConfigurerAdapter 를 상속받아, adapter를 구현합니다. 여기에서 암호화 방식에 대한 password encoder 를 정의하고, 각종 요청에 대한 ignore 처리, login page에 대한 처리 등이 구현됩니다. 지금은 간단하게 아래와 같이 작성하고 확인하겠습니다. 

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
 
import com.simplify.sample.security.service.CustomUserDetailsService;
 
@EnableWebSecurity
public class CustomWebSecurityConfigurerAdapter extends WebSecurityConfigurerAdapter {
    
    @Autowired
    CustomUserDetailsService customUserDetailsService;
 
    @Bean
    public PasswordEncoder passwordEncoder() {
      return new BCryptPasswordEncoder();
    }
    
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring()
        .antMatchers("/openapi/**");
    }
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable().authorizeRequests().anyRequest().authenticated().and().formLogin();
    }
 
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(customUserDetailsService).passwordEncoder(passwordEncoder());
    }
 
}
```

여기까지 작성한 뒤, 재 기동하고 'localhost:8080/' 를 주소창에 입력하여 아래와 같은 login page로 redirection 되는지 확인합니다. 이는 Spring Security 가 기본적으로 가지고 있는 Login page입니다. (추후에 화면을 customizing 하는 것도 알아보겠습니다. )

![](/assets/images/posts/old/img/post/2018-10-06-spring-boot-07-security/spring-boot-07-security-00004.png)

위 로그인 페이지에 abc / abcd 를 입력하여 로그인이 정상적으로 되는 지 확인합니다. 

GitHub 소스 위치 : https://github.com/Simplify-study/SpringBootSample.git

출처: https://4urdev.tistory.com/43 [Simplify]
