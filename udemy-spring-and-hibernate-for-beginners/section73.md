# Section 73. Spring Boot - Application Properties

### lecture 517. Injecting Custom Application Properties

어플리케이션 프로퍼티 커스터마이징
* 앱에 설정이 필요하나 하드코딩을 피하고 싶을 때 필요하다
  - 스프링 부트의 기본 설정은 프로퍼티 파일에서 정보를 읽어오게끔 되어 있다
  - 프로퍼티 파일에서 설정값을 읽어오는 방식을 사용한다
  - 경로 : src/main/resources/application.properties
* 파일에는 어떤 제한도 없이 커스텀 프로퍼티를 설정할 수 있다
* 스프링 부트는 @Value애노테이션을 이용해서 프로퍼티에 접근한다

개발 실습 과정
1. application.properties에 커스텀 프로퍼티 등록
```txt
coach.name=Mickey Mouse
team.name=The Mouse Club
```

2. 스프링 부트 앱에서 @Value를 이용해 주입
```java
@RestController
public class FunRestController {
  @Value("${coach.name}")
  private String coachName;

  @Value("${team.name}")
  private String teamName;
}
```

---

### lecture 518. Configuring the Spring Boot Server

서버와 관련된 설정도 프로퍼티 파일에서 설정할 수 있다
* port, context path, actuator, security etc..
* [자주 쓰이는 common pro*perties](www.luv2code.com/spring-boot-props)

일반적으로 프로퍼티들은 이 영역들과 연관되어 있다
* Core, Web, Security, Data, Actuator, Integration, DevTools, Testing

로깅 레벨 설정하기
```txt
logging.level.org.springframework=DEBUG
logging.level.org.hibernate=TRACE
logging.level.com.luv2code=INFO

logging.file=my-crazy-stuff.log
```
* 위 코드처럼 패키지 네임을 기초로 로깅 레벨을 설정할 수 있다
  - TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF
* 로그 파일의 경로도 설정할 수 있다
* [스프링 부트 로깅](www.luv2code.com/spring-boot-logging)

서버 관련 설정
```txt
# HTTP server port
server.port=7070

# Default context path is /
server.servlet.context-path=/my-silly-app 

# Default HTTP settion time out is 30m
server.servlet.session.timeout=15m
```

Actuator 관련 설정
```txt
# Endpoints to include by name or wildcard
management.endpoints.web.exposure.include=*

# Endpoints to exclude by name or wildcard
management.endpoints.web.exposure.exclude=beans,mapping

# Base path for actuator endpoints
management.endpoints.web.base-path=/actuator
```

Security 관련 설정
```txt
# Default user name
spring.security.user.name=admin

# Password for default user
spring.security.user.password=topsecret
```
 
Data 관련 설정
```txt
# JDBC URL of the database
spring.datasource.url=jdbc:mysql://localhost:3306/ecommerce

# Login username of the database
spring.database.username=scott

# Login password of the database
spring.database.password=tiger
```

개발 실습 과정
1. 서버 포트 설정
2. context path 설정
```txt
server.port=7070

server.servlet.context-path=/mycoolapp
```
* 변경하면 기존의 localhost:8080/teaminfo에 접근하기 위해서 localhost:7070/mycoolapp/teaminfo로 접근해야 한다

