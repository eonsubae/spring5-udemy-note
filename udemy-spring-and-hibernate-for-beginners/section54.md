# Section 54. Spring Security - Add JDBC Database Authentication

앞선 섹션에서의 인증

- 지금까지 사용한 인증은 자바 코드 안에 하드코딩된 간단한 유저를 이용한 것이었다
- 그러나 실제 프로젝트에서는 이렇게 정적으로만 인증을 처리하는 경우는 없다
- 이번 섹션에서는 데이터베이스를 활용해 인증을 다루는 법을 배운다

스프링 시큐리티의 데이터베이스 지원

- 스프링 시큐리티는 데이터베이스로부터 유저 계정 정보를 읽어들인다
- 기본적으로 스프링 시큐리티가 만들어놓은 테이블 스키마를 따르면 아주 적은 코드로 데이터베이스를 이용한 인증을 구현할 수 있다

스프링 시큐리티에서의 데이터베이스 커스터마이징

- 물론 기본적인 테이블 스키마가 있긴 하지만, 커스터마이징 또한 가능하다
- 다만 커스터마이징 시에는 데이터에 접근하기 위한 코드를 작성해야 하는 책임이 생긴다
- 사용하고 있는 DB 통신 방법(JDBC, Hibernate, etc...)으로 직접 테이블에서 정보를 가져와야 한다

개발과정

1. 데이터베이스 테이블 설정을 위한 SQL 스크립트 작성
2. Maven POM 파일에 데이터베이스 지원 추가하기
3. JDBC 프로퍼티 파일 생성하기
4. 스프링 설정에서 DataSource정의하기
5. 스프링 시큐리티 설정을 JDBC를 사용하도록 변경하기

구현을 위한 간단한 테이블 구성하기

- users 테이블

```sql
CREATE TABLE 'users' (
  ''' VARCHAR(50) NOT NULL,
  'password' VARCHAR(50) NOT NULL,
  'enabled' TINYINT(1) NOT NULL,
  PRIMARY KEY ( 'username' )
) ENGINE=InnoDB DEFAULT CHARSET=UTF-8;
```

- enabled가 1이면 로그인 할 수 있는 유저, 0이면 로그인 할 수 없는 유저다

* authorities 테이블

```sql
CREATE TABLE 'authorities' (
  'username' VARCHAR(50) NOT NULL,
  'authority' VARCHAR(50) NOT NULL
  UNIQUE KEY 'authorities_idx_1' ('username', 'authorities'),
  CONSTRAINT 'authorities_ibfk_1'
  FOREIGN KEY ('username')
  REFERENCES 'users' ('username')
) ENGINE=InnoDB DEFAULT CHARSET=UTF-8;
```

스프링 시큐리티의 비밀번호 저장소

- 스프링 시큐리티5에서는 비밀번호를 특정 포맷을 사용해 저장한다
  - {id}encodedPassword
  - id가 noop이면 일반 텍스트로 비밀번호를 저장한다
  - id가 bcrypt이면 bcrypt로 해싱한 비밀번호가 저장된다
  - 이외에도 다양한 해싱 알고리즘이 있다

데이터베이스 테이블 설정을 위한 SQL 스크립트 작성

```sql
INSERT INTO 'users'
VALUES
('john', '{noop}test123', 1),
('mary', '{noop}test123', 1),
('susan', '{noop}test123', 1);
```

```sql
INSERT INTO 'authorities'
VALUES
('john', 'ROLE_EMPLOYEE'),
('mary', 'ROLE_EMPLOYEE'),
('mary', 'ROLE_MANAGER'),
('susan', 'ROLE_EMPLOYEE'),
('susan', 'ROLE_ADMIN');
```

Maven POM 파일에 데이터베이스 지원 추가하기

pom.xml

```xml
<!-- MySql -->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>5.1.45</version>
</dependency>

<!-- C3PO -->
<dependency>
  <groupId>com.mchange</groupId>
  <artifactId>c3p0</artifactId>
  <version>0.9.5.2</version>
</dependency>
```

- mysql-connector-java는 JDBC 드라이버다
- c3p0은 DB 커넥션 풀 설정을 위한 의존성이다

JDBC 프로퍼티 파일 생성하기

src/main/resources/persistence-mysql.properties

```txt
#
# JDBC connection properties
#
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/spring_security_demo?useSSL=false
jdbc.user=springstudent
jdbc.password=springstudent

#
# Connection pool properties
#
connection.pool.initialPoolSize=5
connection.pool.minPoolSize=5
connection.pool.maxPoolSize=20
connection.pool.maxIdleTime=3000
```

스프링 설정에서 DataSource정의하기

DemoAppConfig.java

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages="com.luv2code.springsecurity.demo")
@PropertySource("classpath:persistence-mysql.properties")
public class DemoAppConfig {
  // ...
}
```

- PropertySource애노테이션에 위에서 작성한 프로퍼티 파일의 위치를 인자로 넘겨준다

DemoAppConfig.java

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages="com.luv2code.springsecurity.demo")
@PropertySource("classpath:persistence-mysql.properties")
public class DemoAppConfig {
  @Autowired
  private Environment env;
}
```

- Environment는 프로퍼티 파일에서 읽은 데이터들을 저장하고 있는 객체다

DemoAppConfig.java

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages="com.luv2code.springsecurity.demo")
@PropertySource("classpath:persistence-mysql.properties")
public class DemoAppConfig {
  @Autowired
  private Environment env;

  private Logger logger = Logger.getLogger(getClass().getName());

  @Bean
  public DataSource securityDataSource() {

    // create connection pool
    ComboPooledDataSource securityDataSource = new ComboPooledDataSource();

    // set the jdbc driver
    try {
      securityDataSource.setDriverClass(env.getProperty("jdbc.driver"));
    } catch (PropertyVetoException exc) {
      throw new RuntimeException(exc);
    }

    // for sanity's sake, let's log url and user ... just to make sure we are reading the data
    logger.info(">>>> jdbc.url=" + env.getProperty("jdbc.url"));
    logger.info(">>>> jdbc.user=" + env.getProperty("jdbc.user"));

    // set database connection props
    securityDataSource.setJdbcUrl(env.getProperty("jdbc.url"));
    securityDataSource.setUser(env.getProperty("jdbc.user"));
    securityDataSource.setPassword(env.getProperty("jdbc.password"));

    // set connection pool props
    securityDataSource.setInitialPoolSize(getIntProperty("connection.pool.initialPoolSize"));
	  securityDataSource.setMinPoolSize(getIntProperty("connection.pool.minPoolSize"));
	  securityDataSource.setMaxPoolSize(getIntProperty("connection.pool.maxPoolSize"));
	  securityDataSource.setMaxIdleTime(getIntProperty("connection.pool.maxIdleTime"));

    return securityDataSource;
  }

  // need a helper method
	// read environment property and convert to int

	private int getIntProperty(String propName) {

		String propVal = env.getProperty(propName);

		// now convert to int
		int intPropVal = Integer.parseInt(propVal);

		return intPropVal;
	}
}
```

- securityDataSource는 DataSource객체를 정의하기 위한 메서드다

스프링 시큐리티 설정을 JDBC를 사용하도록 변경하기

DemoSecurityConfig.java

```java
@Configuration
@EnableWebSecurity
public class DemoSecurityConfig extends WebSecurityConfigurerAdapter {

  @Autowired
  private DataSource securityDataSource;

  @Override
  protected void configure(AuthenticationManagerBuilder auth) throws Exception {

    auth.jdbcAuthentication().dataSource(securityDataSource);
  }

}
```

- Autowired로 위에서 설정한 DataSource객체를 주입한다
- auth.jdbcAuthentication().dataSource(securityDataSource)코드로 스프링 시큐리티에 DataSource객체를 활용해 JDBC인증을 사용하라고 말한다. 이제 더이상 하드코딩된 유저를 사용하지 않아도 된다
