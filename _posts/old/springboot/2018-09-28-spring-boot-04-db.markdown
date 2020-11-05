---
title:      "SpringBoot - (4) Connect to DB"
categories: [old]
last_modified_at:       2018-09-21
tags:
  - Spring
  - SpringBoot
---

## DB 연동

앞서 생성한 Controller 를 이용하면 단순 API는 만들 수 있지만 단순 로직을 거치고 난 결과, 즉 Java Code로 해결할 수 있는 한도 내에서만의 서비스 구현만 가능할 뿐, 실제 데이터의 조회 등은 구현할 수 없다. 보통은 Spring 에서 JDBC Connection을 이용하여 DB에 접근하도록 개발하고, Query를 하여 결과를 return 하는 등의 작업을 해 주어야 어느 정도 의미있는 API 설계가 가능하다고 할 수 있다. 

여기서는 Spring Boot 에서 JDBC Connection을 어떻게 만드는지, Query를 어떤 방식으로 작성하는지에 대해서 생각해보고, 향후 진행할 암호화부분, 로그인 처리 등에 대비한 기본적인 부분에 대해서 설명하고자 한다. 여기서 설명하는 DB는 Maria DB 기준으로 설명되어 있으며 Maria DB 설치 자체에 대한 설명은 다른 포스팅으로 대체한다. 이 글을 보는 사용자가 DB를 설치하려면 단순히 홈페이지에 접속해서 installer를 다운로드 받고 실행하는 것 만으로도 간단히 Maria DB 사용이 가능하다. 나는 Amazon Lightsail 에 DB를 설치해 두었고, 이를 기본으로 하여 설명하고자 한다.

## 실습하기

##### dependency 추가

아래 내용을 pom.xml 에 추가한다. jdbc 류가 DB Connection 관련한 것들이고, mybatis 류가 Query 처리 관련 것들이라고 이해하면 된다.
```xml
        <!-- DB -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mariadb.jdbc</groupId>
            <artifactId>mariadb-java-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.3.0</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.2.3</version>
        </dependency>
        <!-- DB -->
```

##### Database 접근 관련 Config class 생성

Database에 접속하고 이를 session을 관리하는 class를 생성해야줘야 한다. 이 부분이 실제 DB에 대한 부분이 설정되는 곳이며, 다음과 같이 작성한다.


```java
package com.simplify.sample.db;
 
import javax.sql.DataSource;
 
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.transaction.annotation.EnableTransactionManagement;
 
@Configuration
@MapperScan(basePackages="com.simplify.sample")
@EnableTransactionManagement
public class DatabaseConfig {
 
    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
        sessionFactory.setDataSource(dataSource);
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        sessionFactory.setMapperLocations(resolver.getResources("classpath:mybatis/mapper/*.xml"));
        return sessionFactory.getObject();
    }
    
    @Bean
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) throws Exception {
      final SqlSessionTemplate sqlSessionTemplate = new SqlSessionTemplate(sqlSessionFactory);
      return sqlSessionTemplate;
    }
 
 
}
```


* @Configuration 

  이 클래스는 Database 에 대한 접속 정보를 나타내고, 일반 Bean 이 나닌 Configuration으로 설정되어 있어야 한다. (실제로는 그 하위에 Bean을 두 개 더 생성한다)

* @MapperScan(basePackages="com.simplify.sample")

  최신 Spring Boot 에서는 dao를 dao라고 명시하지 않고, mapper 라는 이름으로 별도 정의하고 있다. 따라서 어떤 패키지에서 mapper들을 scan할 것인가를 최상위에 annotation으로 정의하여 주고 있다. 다중 Database에 접근해야 하는 경우, 이 클래스를 두 개를 만들어 두고 scan할 대상 mapper 들을 패키지 구조상에서 분리해 두는 것도 하나의 방법이라고 할 수 있다.

* @EnableTransactionManagement

  TransactionManager를 적용할 것인지에 대해 설정하는 annotation 이다.

* @Bean SqlSessionFactory

  DataSource 를 parameter로 받아, sqlSessionFactory를 생성하는 Bean 이다. 아래 있는 SqlSessionTemplate Bean 생성 시 인자로 넘겨주기 위해서 사용된다. 즉, 여기서 만들어진 기본정보, 설정값 등을 이용해서 SqlSessionTemplate를 만들게 되는 것이다.


* .setMapperLocations()

  실제 Query 문이 존재하는 xml파일들의 위치를 지정해 준다. 여기서는 mybatis/mapper 폴더 하위에 있는 모든 xml을 명시했다. 이 경로는 실제로는 src/main/resource 하위에 존재한다. ①이 폴더 구조 자체가 생성되어 있지 않거나, ②그 폴더에 mapper xml 이 하나도 없으면, ③혹은 그 xml 의 문법에 오류가 있으면 초기 구동 시점에 에러가 발생한다. 측, 초기에 mapper들의 위치 정보만을 지정하는 것이 아니라, 해당 mapper xml 들에 오류가 있는지 문법 체크까지 모두 한 뒤에 Factory가 생성되게 되는 것이므로, 이 부분을 다룰 때에는 중간중간 실행하여 오류가 발생하지는 않는지 꼭 확인하면서 지나가야 한다.

* @Bean SqlSessionTemplate

  실제 DB접속에 이용되는 SqlSessionTemplate를 생성하여 반환하는 Bean 이다. 실제 코드상에서 이용된다고 보면 된다.


여기까지 구현하고 실행하여 정상적으로 구동되는지 반드시 확인한다.

##### mybatis/mapper/TestDB.xml 생성하기

아래 코드를 갖는 Query xml 을 작성하여 해당 폴더에 넣는다. (폴더도 생성한다.) 여기에서 향후 생성할 mapper interface에 대한 풀패키지경로를 알아 두어야 하며(아래 노란 색 부분), 각 query 문에 대한 id값이 향후에 mapper interface에서의 함수명과 일치(아래 초록색 부분) 해야 한다는 점을 기억해야 한다.


```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.simplify.sample.dao.DbMapper">
    <select id="getAll" resultType="com.simplify.sample.db.dto.Test">
        SELECT * FROM Test
    </select>
</mapper>
```
##### Dto 클래스 생성하기

현재 다루고자 하는 Table 은 다음과 같이 되어 있다. ID, NAME 두 개 Column 으로 구성되어 있으므로, Java에서는 그냥 모두 String으로 받아 처리하는 것으로 한다. 

![](/assets/images/posts/old/img/post/2018-09-28-spring-boot-04-db/spring-boot-04-db-00001.png)

```java
package com.simplify.sample.db.dto;
 
public class Test {
 
    private String id;
    
    private String name;
 
    public String getId() {
        return id;
    }
 
    public void setId(String id) {
        this.id = id;
    }
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
    
    
}
```

패키지 위치는 당연히 위에 정의한 mapper xml 에서 result type 과 일치하는 위치에 일치하는 이름으로 생성한다. 

##### Mapper 클래스 작성하기

이제 dao, daoImpl 을 사용하지 않고, Mapper라는 인터페이스를 사용해서 Query를 던지는 것으로 구현한다. 아래와 같은 Mapper interface를 생성한다. 여기서 주의할 것은 위 mapper xml 에 선언된 mapper namespace와 일치해야 한다는 점이며, 각 query의 id와 interface의 함수명이 일치해야 정상 동작한다.

```java
package com.simplify.sample.db.mapper;
 
import java.util.List;
 
import com.simplify.sample.db.dto.Test;
 
public interface TestMapper {
 
    public List<Test> getAll() throws Exception;
    
}
``` 

여기서 주의할 것은 위에 mapper xml 에서 mapper tag 안에 선언한 namespace 에 정확하게 일치하는 위치에 같은 이름으로 생성해야 하며, method 이름은 위에 select tag 안에 있는 id 값과 동일하게 작성되어야 자동으로 call 이 이루어진다. dao를 쓸 때에는 각 id와 함께 parameter를 넘겼었으나, 여기서 자동으로 call 하므로 이름을 주의해서 작성해줘야 한다. 

##### Service 생성하기

Controller 가 DB 조회를 요청할 Service를 아래처럼 작성한다.

```java
package com.simplify.sample.db.service;
 
import java.util.List;
 
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
 
import com.simplify.sample.db.dto.Test;
import com.simplify.sample.db.mapper.TestMapper;
 
@Service
public class TestService {
 
    @Autowired
    TestMapper testMapper;
    
    public List<Test> getAll() throws Exception{
        return testMapper.getAll();
    }
}
```

TestMapper interface를 autowired 걸고, 그 안의 getAll 함수를 call한다. 이 부분은 기존의 Spring 과 동일하고 별도의 로직이 없으므로 큰 설명은 생략한다. 향후 어떠한 로직이 포함되어야 하는 경우에는 이 service안에서 처리하는 것이 Spring 개발에서 통용되는 부분이다.

##### Controller 생성 및 Service 호출

이제 위에서 만든 Service를 호출하고 이를 Return 하는 Controller를 하나 만들겠습니다. 

```java
package com.simplify.sample.db.controller;
 
import java.util.List;
 
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
 
import com.simplify.sample.db.dto.Test;
import com.simplify.sample.db.service.TestService;
 
@Controller
public class TestController {
 
    @Autowired
    TestService testService;
    
    @RequestMapping("/query")
    public @ResponseBody List<Test> query() throws Exception{
        return testService.getAll();
    }
 
}
```

Service를 autowired 로 걸고 call 한 결과를 return 합니다. 지난 포스팅에서 언급한 것 처럼, 여긴 @RestController 가 아닌, @Controller이고, 따라서 화면/jsp 등을 return 하는 것이 아니라면 반드시 @ResponseBody를 앞에 넣어 주어야 합니다. 


## 결과 확인

![](/assets/images/posts/old/img/post/2018-09-28-spring-boot-04-db/spring-boot-04-db-00002.png)

## 최종 결과 패키지 구조

![](/assets/images/posts/old/img/post/2018-09-28-spring-boot-04-db/spring-boot-04-db-00003.png)


GitHub 소스 위치 : https://github.com/Simplify-study/SpringBootSample.git

출처: https://4urdev.tistory.com/43 [Simplify]
