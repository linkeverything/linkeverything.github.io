---
title:      "SpringBoot - (6) Hide private information"
categories: [old]
last_modified_at:       2018-10-02
tags:
  - Spring
  - SpringBoot
---

## 들어가며

여기까지 개발하고 나서 GitHub에 소스 코드를 공유하려고 보니, Database에 대한 접속 정보가 그대로 노출되어 있습니다. application.properties에 보면 그 정보가 그대로 있는데, 실제 개발하는 환경, 업무든 개인이든 서버에 직접 접근하지 않는 이상 이 내용을 읽어내기란 쉬운 과정은 아니기 때문에 크게 걱정할 부분은 아니다. 

다만, 여기서는 소스 코드 자체를 배포할 것이기 때문에 id, password를 공개하는 것 자체가 추후 DB에 어떠한 식으로든 영향을 줄 수 있어, 이에 대해서 숨길 수 있는 방안을 고안해 보려고 한다. 이 방법만이 있는 것도 아니고 이 방법이 가장 효율적이라고 할 수는 없으나, 암호화 등등을 넘어서서 특정 위치에 별도의 파일로 분리 함으로써 보안에 대한 문제를 조금 더 해결할 수 있지 않을까 싶다. 

## 개선 방향

민감 정보(sensitive data) 들을 숨기기 위해서, 내가 선택한 방법은 '별도의 파일로 분리하기' 입니다. 별도의 파일로 분리 해 두고, 이를 시작시점에 읽어서, property를 구성 해 주면 되고, 그 파일 관리만 신경쓴다면 그 어떠한 암호화보다도 안전한 방법이 될 수 있습니다. 또한 이 방법이 갖는 장점은 그 파일을 VI Editor 나 nano 등으로 수정하고 재기동하면 변경된 property 에 따라 소스 코드 수정 없이 변경 구동이 가능하다는 점입니다. 물론 그 파일이 없다면 시작 시점부터 프로그램이 종료되어 버릴 것입니다. 


이렇게 별도로 구성될 파일은 xml형태이든, properties파일이든 아무런 상관없으나, 기존 application.properties 파일에 더불어서 설정 파일의 성격을 갖게 될 것이므로 config.properties 로 하겠습니다. 또한 그 안의 문법 또한 &#42;.properties 에 따릅니다.

##### .properties 파일의 분리

기존 application.properties 파일에서 민감 정보를 분리하여 config.properties 파일로 만들고 특정 위치에 둡니다. 이 경로는 개발하기 용이하고 추후 배포에도 사용될 소지가 있기 때문에 고정된 위치를 주는 것이 좋습니다.

src/main/resources 폴더 하위에 있는 application.properties 파일을 아래와 같이 #을 붙여서 주석 처리 해 줍니다. 


```properties
#spring.datasource.driverClassName=org.mariadb.jdbc.Driver
#spring.datasource.url=jdbc:mariadb://52.78.41.121:3306/TestDB
#spring.datasource.username=test
#spring.datasource.password=test
 
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```

그리고 별도의 파일을 적절한 이름으로 생성하고 위에서 주석 처리한 정보들을 넣어줍니다. 꼭 프로젝트 내부에 만들지는 않아도 됩니다. 그럴 필요도 없구요..  (공유된 GitHub repository 에는 /etc/ 폴더 안에 있습니다)

```properties
spring.datasource.driverClassName=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://52.78.41.121:3306/TestDB
spring.datasource.username=test
spring.datasource.password=test
```

이제 이 파일을 Spring Boot를 구동시킬 시스템의 접근 가능한 공간에 가져다 놓습니다. Linux 기반 시스템의 경우에는 읽기 권한이 당연히 있어야겠죠.. 권한 체크 잘 하시고 파일을 가져다 놓습니다. 

##### .properties 읽기

Spring쪽 공식 문서들을 보면 다양한 방법으로 properties 파일을 다중으로 읽어들일 수 있다고 가이드하고 있습니다. 근데 문제는 실행 구문에서 이를 조정해주어야 하고 (-D 옵션 등..) 변경점이 생겼을 때에 실행구문 고쳐야 하는 점을 감안한다면 어차피 완벽에 가까운 것은 없으므로, 여기서는 properties 를 읽는 Bean 을 하나 만들고 이를 이용해서 DataSource를 구성하는 것으로 개발하도록 하겠습니다. 위에서 만든 config.properties 파일을 읽어들이는 파일을 아래와 같이 생성합니다. 이름은 GlobalPropertySource로 하겠습니다. 

```java
package com.simplify.sample;
 
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.context.annotation.PropertySources;
 
@Configuration
@PropertySources({
    @PropertySource( value = "file:c:/dev/config.properties", ignoreResourceNotFound = true ),
    @PropertySource( value = "file:${user.home}/env/config.properties", ignoreResourceNotFound = true)
})
public class GlobalPropertySource {
 
    @Value("${spring.datasource.driverClassName}")
    private String driverClassName;
    
    @Value("${spring.datasource.url}")
    private String url;
    
    @Value("${spring.datasource.username}")
    private String username;
    
    @Value("${spring.datasource.password}")
    private String password;
 
    public String getDriverClassName() {
        return driverClassName;
    }
 
    public String getUrl() {
        return url;
    }
 
    public String getUsername() {
        return username;
    }
 
    public String getPassword() {
        return password;
    }
 
}
```

우선 이 파일은 @Cofiguration annotation 을 갖습니다. 그 안에 Bean을 생성하거나 그 자체로서의 Bean이 되기 위한 방법입니다. 그리고 앞서 만든 config.properties 파일을 읽어들이도록 해야 합니다. 

```java
@PropertySource( value = "file:c:/dev/config.properties", ignoreResourceNotFound = true )
```

이러한 방법을 통해서 File을 읽게 만들 수 있습니다. properties 파일에 해당하는 문법을 잘 parse해서 읽을 수 있게 합니다. 그런데 읽어야 할 파일이 여러 개 있을 수 있고, 그런 경우에는 위 소스를 여러 개 만들어서 @PropertySources 로 묶어줍니다. 배열 형태이니 {} 를 넣고, 콤마(,)로 구분해 줍니다. 

위와 같은 처리가 있는 것은 Windows 시스템(개발환경)과 Linux 시스템(실제 구동 환경)에서 모두 동작하게 하기 위함입니다. 상대 경로로 하기는 싫고, 특정 경로를 주고 싶은데 시스템마다 그 경로가 차이가 나는 경우 위와 같이 두 곳을 모두 지정해 주면 됩니다. 두 가지 파일이 모두 존재하는 경우는 없을 것이므로 위 설정이 유효합니다. 단, Windows 에서는 두 번째 @PropertySource가, Linux 에서는 첫 번째 PropertySource 가 정상 동작하지 않을 것이므로(파일이 없음), ignoreResourceNotFound = true 를 넣어 해결해 줍니다.

그리고 편의 상, 위 처럼 getter들을 만들어 주었습니다. 

##### DataSource Bean Customize하기

기존 DatabaseConfig 파일에서는 DataSource 가 Autowired되어 들어오는데, 우린 정해준 적이 없습니다. 이렇게 되면 기본으로 Bean 을 생성해 주게 되고, 그 Bean 생성 파일에 보면 application.properties가 불리게 되어 있습니다. 사실 위에서 만든 config.properties 가 가장 최초 시점에 불리게 해 주면 properties 두 개 파일을 불러오게 되고, 쉽게 해결이 가능하겠으나, 여기서는 그냥 DataSource Bean 까지 생성하는 것으로 하겠습니다. 기존 DatabaseConfig 파일에 아래와 같이 DataSource Bean을 추가해 줍니다. 

```java
package com.simplify.sample.db;
 
import javax.sql.DataSource;
 
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.transaction.annotation.EnableTransactionManagement;
 
import com.simplify.sample.GlobalPropertySource;
 
@Configuration
@MapperScan(basePackages="com.simplify.sample")
@EnableTransactionManagement
public class DatabaseConfig {
 
    @Autowired
    GlobalPropertySource globalPropertySource;
    
    @Bean
    @Primary
    public DataSource customDataSource() {
        return DataSourceBuilder
            .create()
            .url(globalPropertySource.getUrl())
            .driverClassName(globalPropertySource.getDriverClassName())
            .username(globalPropertySource.getUsername())
            .password(globalPropertySource.getPassword())
            .build();
    }
    
    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource customDataSource) throws Exception {
        final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
        sessionFactory.setDataSource(customDataSource);
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

위에 대한 상세 설명은 다음과 같습니다. 

- 앞서 config.properties 파일을 읽어들이던 GlobalPropertySource를 @Autowired 해줍니다. @Configuration annotation도 기본적으로 Autowired하는 데 문제가 없습니다. 
 
- DataSource를 @Bean으로 만들고 @Primary 도 넣어줍니다.
  여기서 앞서 config.properties 파일에 선언한 DB정보를 넣어줍니다. 결국 이렇게 처리하기 위해서 앞선 작업들을 해왔다고 볼 수 있습니다. 설정에 적혀있는 것 처럼 driver, url, id, password 네 가지 정보를 넣어주고 DataSource를 return 합니다.
  
- 내가 만들어준 DataSource를 사용할 수 있도록 SqlSessionFactory를 return 하는 Bean 쪽에 이름을 function 명과 동일하게 맞춰줍니다. (이건 Spring 기본 사항입니다)

## 구동 및 확인

기존 /query url을 호출해서 정상 동작하는지 확인합니다. 이렇게 하면 GitHub 등에 공유하고도 DB 접속정보와 같은 민감정보를 숨길 수 있습니다. 


GitHub 소스 위치 : https://github.com/Simplify-study/SpringBootSample.git

출처: https://4urdev.tistory.com/43 [Simplify]
