# Section 71 - Spring Boot - Spring Boot Dev Tools and Spring Boot Acturator

### lecture 511. Spring Boot - Dev Tools

스프링 부트 애플리케이션 실행 중 불편한 점
* 소스 코드 변경시 변경된 점을 적용하기 위해서는 서버를 리스타트 해야한다
* spring-boot-devtools를 이용하면 변경사항을 저장할 떄마다 자동으로 재시작해준다

spring-boot-devtools
* POM 파일에 의존성을 추가하면 설정이 끝난다
```xml
(...)
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
</dependency>
(...)
```
* [devtools documentation](www.luv2code.com/devtools-docs)

서버를 실행하고 workout 라우터 추가하기
```java
@RestController
public class FunRestController {
    @GetMapping("/")
    public String sayHello() {
    	return "Hello World! Time on server is " + LocalDateTime.now();
    }
    
    @GetMapping("/workout")
    public String getDailyWorkout() {
    	return "Run a hard 5k!";
    }
}
```
* getDailyWorkout을 추가하고 저장하면 실행중인 서버가 자동으로 재시작된다

---

### lecture 512. Spring Boot Actuator - Overview

애플리케이션 성능 관리 문제
* 애플리케이션은 모니터링, 관리가 필요하다
* Spring Boot Actuator는 데브옵스 기능들을 쉽게 사용하게 만들어준다

Spring Boot Actuator
* POM 파일에 의존성만 등록하면 된다
```xml
(...)
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
(...)
```
* /actuator 엔드포인트를 기준으로 각종 정보들을 확인할 수 있다
  - /health는 애플리케이션의 상태를 보여준다
  - /info는 프로젝트의 정보를 보여준다
  - /auditevents는 애플리케이션의 이벤트들을 점검한다
  - /beans는 스프링 애플리케이션 컨텍스트에 등록된 모든 빈들의 리스트를 보여준다
  - /mappings는 모든 @RequestMapping 경로들을 보여준다
  - 그 외에도 10개 이상의 엔드포인트가 있다
  - 자세한 정보는 [actuator full list](www.luv2code.com/actuator-endpoints)

health 엔드포인트
* 애플리케이션의 상태를 점검하는 엔드포인트(UP or DOWN으로 상태를 표시)
* 애플리케이션을 모니터링하기 위해 사용한다
* 비즈니스 로직에 맞게 커스터마이징 할 수 있다

info 엔드포인트
* 애플리케이션의 정보를 보여주는 엔드포인트
* 기본값은 비어 있다({})
* application.properties를 수정하면 info에 반영된다
```txt
info.app.name=My Super Cool App
info.app.description=A crazy and fun app!
info.app.version=1.0.0
```

엔드포인트 노출
* 기본적으로 /actuator 하위에는 /health와 /info만 노출되어있다
* 전체 엔드포인트를 노출하려면 src/main/resources/application.properties에 다음과 같이 와일드 카드를 지정하면 된다
```txt
management.endpoints.web.exposure.include=*
```

---

### lecture 514. Spring Boot Actuator - Applying Security to Actuator Endpoints

앞서 /actuator 이하의 엔드포인트로 접속하면 앱의 다양한 정보가 그대로 노출되는 것을 확인했다
* 그러나 실제 앱에서 이런 정보들이 노출되는 것은 보안상 좋지 않다
* Spring Security를 추가하면 보호하고 싶은 정보를 감춰준다
  - ex. /health나 /info 등 감추고 싶은 정보를 감출 수 있다

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Secured Endpoints
* 스프링 시큐리티를 추가하면 /actuator 이하의 엔드포인트로 접속했을 때 로그인 프롬프트를 띄워준다
* 기본 Username과 Password
  - Username : user
  - Password : 콘솔의 로그에서 알려준다
* Username, Password 변경하기
  - application.properties에서 다음과 같이 수정하면 된다
  ```txt
  spring.security.user.name=scott(유저네임)
  spring.security.user.password=ti(비밀번호)
  ```

스프링 시큐리티 커스터마이징
* spring boot actuator를 위해 스프링 시큐리티를 커스터마이징 할 수 있다
* 데이터베이스를 사용할 수도 있고, 암호화된 비밀번호를 사용할 수도 있다
* 기존 스프링 시큐리티의 기능들을 그대로 사용할 수 있다고 생각하면 된다
* 매쳐를 등록해 특정 엔드포인트는 어드민 유저만 사용할 수 있도록 만들 수도 있다

특정 엔드포인트 제외하기
* application.properties에서 다음과 같이 추가하면 특정 엔드포인트를 감출 수 있다
  ```txt
  management.endpoints.web.exposure.exclude=health,info
  ```

spring actuator documentation
* [spring actuator docs 링크](www.luv2code.com/actuator-docs)

개발 실습 과정
1. pom.xml에 spring-boot-starter-security를 추가한다
2. /mappings 엔드포인트에 보안을 적용한다
3. health, info 엔드포인트를 사용 불가능하게 만든다