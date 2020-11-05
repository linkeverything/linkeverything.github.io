---
title: "Spring Boot에서 여러 Datasource 사용하기"
categories: [SpringBoot]
tag: [multiple, datasource, jpa]
last_modified_at: 2020-11-04
---
Spring Boot를 사용하면서 다중 데이터소스를 사용하는 필요성이 가끔 있습니다. 과거 EJB환경이나 Web/WAS 등의 구조를 보면 이렇게 다중 데이터소스를 활용하는 일이 많았고, 현재도 여러 데이터소스, 예를 들어 사용자 관련 Datasource와 Transaction관련 부분을 별도 DB로 분리해서 구성하는 경우가 있어 종종 필요한 부분입니다. 

물론 최근 화두가 되어온 MSA(Micro-Service Architecture)사상에서는 다중 데이터 소스를 활용하는 것이 맞지 않을 것입니다.[^1] 그럼에도 불구하고 MSA 형태로 전환 과정에 있거나, 프로젝트 상황, 기존 시스템과의 연계성 때문에라도 어쩔 수 없이 다중 데이터소스를 활용해야 할 일이 있습니다. 

다중 데이터소스를 활용한다는 것에서 중요한 포인트는 <mark style='background-color: #ffdce0'>Transaction관리</mark>입니다. 하나의 Datasource를 이용할 때에는 DB가 가진 기능으로도 Transaction관리가 가능하고, 아니면 WAS등의 도움을 받을 수 있지만, 다중 데이터소스를 이용하는 경우에는 쉽지 않습니다. 또한 Datasource가 미정인 경우, 즉 Saas 형태의 어플리케이션을 개발하거나, 혹은 다양한 DB를 모두 지원하는 어플리케이션 설계에 있어 Transaction관리는 어플리케이션 영역에서 이루어질 수 밖에 없습니다. 

여기서는 일반적인 Spring 환경에서 어떻게 다중 데이터소스를 이용할 수 있는지 살펴보고, Spring Boot 에서도 어떻게 구현하는지를 간단히 살펴봅니다.



## 사전 준비 및 준비물

개발 및 테스트를 위해 다음을 준비합니다. 더 편리한 방법이 있다면 어떤 것을 사용해도 무방합니다. 



#### Database

DB는 mariadb를 이용합니다. 간단하게 구현하기 위해서 docker 로 구동하는 것으로 하는데, docker로 두 개의 instance로 띄우는 것 보다는 그저 하나의 instance안에서 database scheme만 두 개를 생성해서 구동하겠습니다. 

docker가 설치되어 있는 환경에서 아래와 같은 내용으로 `docker-compose.yml`파일을 생성합니다.

```yml
version: '3.1'
services:
  maria_db_1:
    image: mariadb:10.5.3
    container_name: ${CONTAINER_NAME_1}
    restart: always
    ports:
      - "63306:3306"
    volumes:
      - ${PWD}/${CONTAINER_NAME_1}/volumes/etc/mysql/conf.d:/etc/mysql/conf.d:ro
      - ${PWD}/${CONTAINER_NAME_1}/volumes/var/lib/mysql:/var/lib/mysql
      - ${PWD}/${CONTAINER_NAME_1}/volumes/var/log/maria:/var/log/maria
    environment:
      - MYSQL_ROOT_PASSWORD=qwer1234!
      - TZ="Asia/Seoul"
```

위 내용을 가볍게 분석하면 다음과 같습니다. 

- `image: mariadb:10.5.3` : 사용할 docker container 의 버전을 설정합니다.

- `container_name: ${CONTAINER_NAME_1}` : 환경 변수 중 CONTAINER_NAME_1으로 설정된 이름으로 container 이름을 설정합니다. 
 
- `restart: always` : docker 서비스가 시작될 때 자동으로 재시작합니다. 

- `ports: - "63306:3306"` : container내부 포트 3306을 Host 의 63306에 포트포워딩 합니다.

- `volumes: ` : host 의 특정 경로와 그에 매핑되는 container 내부 경로를 지정합니다.

- `environment: ` : 환경 변수 값을 지정합니다.[^2]

이제 같은 폴더에서 `.env` 파일을 생성합니다. (참고: [Docker 에 환경변수 관리하기](https://linkeverything.github.io/container/docker-env/))

```
CONTAINER_NAME_1=maria_db_1
```

이제 `docker-compose up -d` 명령어를 이용해서 실행합니다. 

여기서 사용자 추가 및 database 추가에 대한 부부은 작성하지 않겠습니다. 해당 내용은 [MySQL에 데이터베이스 및 사용자 추가하기](https://linkeverything.github.io/linux/mysql-user-database-creation/)글을 참고하세요
{: .notice--warning}



#### Spring Boot

이제 [Spring Initializr 페이지](https://start.spring.io/)에서 프로젝트를 하나 생성합니다. 바로 이어지는 내용은 Spring Framework 공통이지만, Spring Boot를 활용해도 무방합니다. 

생성한 프로젝트를 IDE(IntelliJ)에 import하고 gradle/maven 빌드를 돌려 정상적으로 import되었는지 확인합니다.

`buidl.gradle` 파일을 열어 다음 내용을 dependencies 부분에 추가합니다.

```
	implementation 'org.springframework.boot:spring-boot-starter-web:2.3.2.RELEASE'
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa:2.3.2.RELEASE'
	implementation 'mysql:mysql-connector-java:8.0.21'
    compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
```



## 적용하기

간단한 예제만을 소개합니다. 



#### Entity

다음과 같이 각 Datasource에 해당하는 User, Product entity를 생성합니다. 기본적으로 자동 생성되게 되어 있으므로, 테이블이 없어도 자동 생성합니다. 

```java
@Entity
@Table(schema = "users")
public class User {
 
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
 
    private String name;
 
    @Column(unique = true, nullable = false)
    private String email;
 
    private int age;
}
```

```java
@Entity
@Table(schema = "products")
public class Product {
  
    @Id
    private int id;
  
    private String name;
  
    private double price;
}
```

위 두 개의 entity는 서로 다른 패키지에 위치시킵니다.



#### JPA repository

다음으로는 각각의 repository를 생성합니다. 이는 예전 Dao를 대체하는 요소로 jpa를 이용합니다. 추가적인 method 는 정의하지 않고 넘어가고 자동 생성되는 요소만 이용합니다.

```java
package com.simplify.multidsspringboot.dao.user;

import com.simplify.multidsspringboot.model.user.User;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Integer> {
}
```

```java
package com.simplify.multidsspringboot.dao.product;

import com.simplify.multidsspringboot.model.product.Product;
import org.springframework.data.jpa.repository.JpaRepository;

public interface ProductRepository extends JpaRepository<Product, Integer> {
}
```

extends 받는 JpaRepository에 대해서는 앞서 생성한 entity와 그 key에 해당하는 것을 넣어줍니다. 둘다 `@Id` annotation을 사용하면서 int 로 지정하였으므로 이를 넣어주면 됩니다. 만약 long 으로 id를 잡았다면 `<User, Long>` 과 같은 형태가 될 것입니다.



#### JPA configuration 

이제 가장 중요한 Jpa Configuration을 생성합니다. 

```java
@Configuration
@PropertySource({ "classpath:persistence-multiple-db.properties"})
@EnableJpaRepositories(
        basePackages = "com.simplify.multipleDatasource.dao.user",
        entityManagerFactoryRef = "userEntityManager",
        transactionManagerRef = "userTransactionManager"
)
public class UserConfig {

    @Autowired
    private Environment env;

    @Primary
    @Bean
    public DataSource userDataSource(){

        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(env.getProperty("jdbc.driverClassName"));
        dataSource.setUrl(env.getProperty("user.jdbc.url"));
        dataSource.setUsername(env.getProperty("jdbc.user"));
        dataSource.setPassword(env.getProperty("jdbc.pass"));

        return dataSource;
    }

    @Primary
    @Bean
    public LocalContainerEntityManagerFactoryBean userEntityManager(){
        LocalContainerEntityManagerFactoryBean em = new LocalContainerEntityManagerFactoryBean();
        em.setDataSource(userDataSource());
        em.setPackagesToScan(
                new String[] {"com.simplify.multipleDatasource.model.user"}
        );

        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        em.setJpaVendorAdapter(vendorAdapter);

        HashMap<String, Object> properties = new HashMap<>();
        properties.put("hibernate.hbm2ddl.auto", env.getProperty("hibernate.hbm2ddl.auto"));
        //properties.put("hibernate.dialect", env.getProperty("hibernate.dialect"));
        properties.put("hibernate.dialect", env.getProperty("spring.jpa.properties.hibernate.dialect"));

        em.setJpaPropertyMap(properties);

        return em;
    }

    @Primary
    @Bean
    public PlatformTransactionManager userTransactionManager(){
        JpaTransactionManager transactionManager = new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(userEntityManager().getObject());
        return transactionManager;
    }

}
```

```java
@Configuration
@PropertySource( {"classpath:persistence-multiple-db.properties"} )
@EnableJpaRepositories(
        basePackages = "com.simplify.multipleDatasource.dao.product",
        entityManagerFactoryRef = "productEntityManager",
        transactionManagerRef = "productTransactionManager"
)
public class ProductConfig {

    @Autowired
    private Environment env;

    @Bean
    public DataSource productDataSource(){

        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(env.getProperty("jdbc.driverClassName"));
        dataSource.setUrl(env.getProperty("product.jdbc.url"));
        dataSource.setUsername(env.getProperty("jdbc.user"));
        dataSource.setPassword(env.getProperty("jdbc.pass"));

        return dataSource;
    }

    @Bean
    public LocalContainerEntityManagerFactoryBean productEntityManager(){

        LocalContainerEntityManagerFactoryBean em = new LocalContainerEntityManagerFactoryBean();
        em.setDataSource(productDataSource());
        em.setPackagesToScan(new String[] { "com.simplify.multipleDatasource.model.product" });

        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        em.setJpaVendorAdapter(vendorAdapter);

        HashMap<String, Object> properties = new HashMap<>();
        properties.put("hibernate.hbm2ddl.auto", env.getProperty("hibernate.hbm2ddl.auto"));
        //properties.put("hibernate.dialect", env.getProperty("hibernate.dialect"));
        properties.put("hibernate.dialect", env.getProperty("spring.jpa.properties.hibernate.dialect"));
        em.setJpaPropertyMap(properties);

        return em;
    }

    @Bean
    public PlatformTransactionManager productTransactionManager() {
        JpaTransactionManager transactionManager = new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(productEntityManager().getObject());
        return transactionManager;
    }
}
```

위 두 class는 거의 유사하게 구성되어 있고, 이 안에서 사용되는 각 property 파일들은 다음과 같습니다. 

```properties
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MariaDBDialect
jdbc.driverClassName=com.mysql.cj.jdbc.Driver
user.jdbc.url=jdbc:mysql://10.250.46.219:63306/sample_database?serverTimezone=UTC&characterEncoding=UTF-8
product.jdbc.url=jdbc:mysql://10.250.46.219:63306/sample_database2?serverTimezone=UTC&characterEncoding=UTF-8
jdbc.user=sample
jdbc.pass=
```

여기서는 그저 같은 사용자/비밀번호를 사용하고 있고, url 만 다르게 설정하여 간단하게 테스트 목적으로만 구현합니다.



#### Controller

이제 이들을 테스트할 class를 생성합니다. 테스트를 위해서 repository를 직접 controller에서 호출합니다. 아마도 일반적인 경우에서는 Service를 생성하고 그 안에서 repository에 호출하는 구조로 생성할 것입니다. 

```java
@Controller
public class MultiDataSourceController {

    @Autowired
    UserRepository userRepository;

    @Autowired
    ProductRepository productRepository;

    @RequestMapping(value = "/addUser")
    @ResponseBody
    public Object addUser() {

        User user = new User();
        user.setName("John");
        user.setEmail("john@test.com");
        user.setAge(20);

        User savedUser = userRepository.save(user);

        return savedUser;
    }

    @RequestMapping(value = "/addProduct")
    @ResponseBody
    public String addProduct() {

        Product product = new Product();
        product.setName("product1");
        product.setPrice(100);

        Product savedProduct = productRepository.save(product);

        return String.valueOf(savedProduct.getId());
    }

    @RequestMapping(value = "/addUserAndProduct")
    @ResponseBody
    public String addUserAndProduct() {

        User user = new User();
        user.setName("John");
        user.setEmail("john@test.com");
        user.setAge(20);

        User savedUser = userRepository.save(user);

        Product product = new Product();
        product.setName("product1");
        product.setPrice(100);
        product.setUserId(savedUser.getId());

        Product savedProduct = productRepository.save(product);

        return savedUser.getId() + "/" + savedProduct.getId() + "-" + savedProduct.getUserId();
    }

    @RequestMapping(value = "/addUserAndProductNonTransactional")
    @ResponseBody
    public String addUserAndProductNonTransactional() {

        User user = new User();
        user.setName("John");
        user.setEmail("john@test.com");
        user.setAge(20);

        User savedUser = userRepository.save(user);

        Product product = new Product();
        product.setName("product1");
        product.setPrice(100);
        //product.setUserId(savedUser.getId());
        product.setUserId(19);

        Product savedProduct = productRepository.save(product);

        return savedUser.getId() + "/" + savedProduct.getId() + "-" + savedProduct.getUserId();
    }

    @RequestMapping(value = "/addUserAndProductTransactionalOk")
    @ResponseBody
    @Transactional
    public String addUserAndProductTransactionalOk() {

        User user = new User();
        user.setName("John");
        user.setEmail("john@test.com");
        user.setAge(20);

        User savedUser = userRepository.save(user);

        Product product = new Product();
        product.setName("product1");
        product.setPrice(100);
        product.setUserId(savedUser.getId());
        //product.setUserId(19);

        Product savedProduct = productRepository.save(product);

        return savedUser.getId() + "/" + savedProduct.getId() + "-" + savedProduct.getUserId();
    }

    @RequestMapping(value = "/addUserAndProductTransactionalError")
    @ResponseBody
    @Transactional
    public String addUserAndProductTransactionalError() {

        User user = new User();
        user.setName("John");
        user.setEmail("john@test.com");
        user.setAge(20);

        User savedUser = userRepository.save(user);

        Product product = new Product();
        product.setName("product1");
        product.setPrice(100);
        //product.setUserId(savedUser.getId());
        product.setUserId(19);

        Product savedProduct = productRepository.save(product);

        return savedUser.getId() + "/" + savedProduct.getId() + "-" + savedProduct.getUserId();
    }
}
```

사실 `@Autowired` 를 사용하여 Field Injection을 사용하는 것을 지양하는 것이 맞지만 예제이므로 무시합니다.



#### 테스트

다음과 같이 테스트할 수 있습니다. 

- `/addUser` : 하나의 User가 생성되고 내용이 반환됩니다. 

- `/addProduct` : 하나의 Product가 생성되고 내용이 반환됩니다.

- `/addUserAndProduct` : User가 생성되고 그에 따른 Product가 생성됩니다.

- `/addUserAndProductNonTransactional` 
  
  최초 한번 실행하여 User와 그에 매핑되는 Product를 생성하고, 
  
  다시 한 번 실행하면 에러가 발생
    - USERID에 대해서 Duplicate entry '19' for key 'USERID' 에러 
    - Product를 INSERT(save)하다가 발생

  User는 정상적으로 INSERT 되고 Product만 INSERT되지 않음 <mark style='background-color: #ffdce0'>(transaction관리 실패)</mark>

- `/addUserAndProductTransactionalOk` : 정상적인 logic 확인용

- `/addUserAndProductTransactionalError`
  
  transaction 관리가 되는지 확인 목적
  
  User는 저장하고 나서, Product을 넣으려다 실패
  
  sample_datasource, User 테이블에 사용자도 저장 안됨 <mark style='background-color: #dcffe4'>(transaction관리 성공)</mark>



## Spring Boot에 적용하기

Spring Boot 에 적용하는 것은 앞선 과정과 동일합니다. 실제로 위 내용을 그대로 Spring Boot 환경에 넣어도 무방합니다. 그러나 아주 조금 더 편리하게 구성을 할 수 있다고 소개하고 있습니다. 



#### .properties

우선 properties 파일에 대해서 아래 내용을 변경합니다. 

```properties
spring.datasource.jdbcUrl = jdbc:mysql://10.250.46.219:63306/sample_database?serverTimezone=UTC&characterEncoding=UTF-8
spring.datasource.username = sample
spring.datasource.password = 

spring.second-datasource.jdbcUrl = jdbc:mysql://10.250.46.219:63306/sample_database2?serverTimezone=UTC&characterEncoding=UTF-8
spring.second-datasource.username = sample
spring.second-datasource.password = 
```

위와 같이 각 property의 앞 부분(prefix)만 다르게 하여 동일한 내용들을 입력해 줍니다. 아마도 조금 더 다양한 형태로 구성된다면 그를 다 반영해 주면 될 것입니다. 



#### configuration 

이제 configuration의 Datasource 생성하는 부분을 아래와 같이 수정합니다. 기존 소스 코드를 간단히 `create().build()` 형태로 변경하면 됩니다. 

```java
    @Primary
    @Bean
    @ConfigurationProperties(prefix="spring.datasource")
    public DataSource userDataSource() {
        return DataSourceBuilder.create().build();
    }
```

```java
    @Bean
    @ConfigurationProperties(prefix = "spring.second-datasource")
    public DataSource productDataSource() {
        return DataSourceBuilder.create().build();
    }
```

이렇게 하면 자동적으로 해당 datasource를 잡아줍니다. 이 정도 수준으로 약간 편한 수준입니다. 



## Comment

실질적으로 이 post에서 중요한 내용은 두 개의 서로다른 jpaRepository를 생성했다는 점, 그리고 각각에 대해서 사용하는 쪽(여기서는 controller)에 설정된 `@Transactional` annotation에 따라서 어떻게 동작하는지 입니다. 

`@Transactional`이 선언된 request를 요청하면, user 추가가 정상적으로 이루어지고 난 뒤에 product 추가 시 에러가 발생하면 기존에 추가했던 user 역시 삭제가 됩니다. transaction관리가 되고 있다는 의미입니다. 

이 내용만으로 다중 데이터 소스에 대한 transaction 관리 내용이 완벽하게 커버되기는 힘들겠지만, 더 확장해서 구현하면 원하는 기능을 추가적으로 구현할 수 있을 것입니다.



## 참고자료 및 출처

- <https://www.baeldung.com/spring-data-jpa-multiple-databases>

[^1]: MSA 에서 이야기하는 구조에서 보면 하나의 관련 업무를 처리하는 것들을 하나의 작은 시스템으로 묶어 사용하는 것을 권장합니다. 따라서 각 기능별로 구분된 형태로 Database도 나눠지고, 각 Database에 관련한 시스템 역시 하나로 구성되게 됩니다. 반드시 이렇게 구성하는 것은 아니지만, 하나하나의 작은 단위로 나누게 되면 이렇게 1:1 관계가 되게 마련인데 그렇게 되면 다중 데이터소스를 활용하는 것은 거의 관련성이 없어 보입니다.

[^2]: 이 환경 변수 값들에 대해서는 [mariadb 공식 documentation](https://mariadb.com/kb/en/installing-and-using-mariadb-via-docker/)과 [docker hub의 mariadb](https://hub.docker.com/_/mariadb)쪽에 설명되어 있습니다.