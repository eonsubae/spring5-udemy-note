# Section 70 - Spring Boot 

### lecture 504. What is Spring Boot

스프링 부트의 필요성
* 기존 스프링 애플리케이션 작성시의 불편함
  - 어떤 메이븐 아키텍쳐를 사용할 것인가?
  - 어느 메이븐 의존성들이 필요한가?
  - XML과 자바 중 어떤 설정을 할 것인가?
  - Tomcat, JBoss 등의 서버를 어떻게 설치할 것인가
* 이처럼 수많은 설정 때문에 중요한 목적인 개발에 집중하기 힘들어하는 사람들이 많았다

스프링 부트
* 설정을 최소화 시켜준다
  - props 파일들과 JAR classpath로 자동설정
* 의존성 충돌(메이븐 또는 그레이들)을 해소시켜준다
* 내장 HTTP서버를 제공해 빠르게 돌려볼 수 있다

스프링 이니셜라이저
* [스프링 이니셜라이저 사이트](http://www.start.spring.io)
* 사이트에서 의존성들을 설정할 수 있다
* 설정한 의존성에 맞게끔 메이븐 혹은 그레이들 프로젝트를 생성한다
* 생성한 프로젝트를 IDE에서 임포트한다

스프링 부트 앱 실행하기
* 스프링 부트로 생성한 앱은 내장 서버가 있어 추가적인 설치가 필요없다
* IDE나 CLI를 이용해 실행하면 된다
```cmd
java -jar 앱이름.jar
```
* 물론 기존 방식으로 배포하는 것도 가능하다
  - ex. WAR 파일을 외부 서버에 배포하기

스프링 부트에 대해 자주하는 질문들
* 스프링 부트가 스프링 MVC나 스프링 REST 등을 대체하는 것인가?
  - 스프링 부트는 설정을 줄여주는 것일뿐이므로 스프링의 기존 기술들과 경쟁관계가 아니다. 여전히 기존 기술들을 사용한다
* 스프링 부트는 속도가 더 빠른가?
  - X. 설정 최소화일 뿐 속도가 빨라지지 않는다
* 스프링 부트에 맞는 특별한 IDE를 필요한가?
  - X. 하지만 스프링 팀에서 제공하는 STS라는 IDE가 있긴 하다
  - STS와 같은 몇몇 IDE가 유용한 도구들을 지원해주는 이점은 있다

---

### lecture 505. Creating a Project with Spring Boot Initializr

스프링 이니셜라이저를 이용한 개발 과정
* 스프링 이니셜라이저 사이트에서 프로젝트의 설정을 한다
* zip 파일을 다운받는다
* 다운받은 zip 파일의 압축을 푼다
* IDE에서 프로젝트를 임포트한다

스프링 부트 버전 설정
* SNAPSHOT 버전은 피하는 것이 좋다(알파 버전이므로 안전성이 낮다)
* SNAPSHOT이 아닌 최신 버전을 사용하자

프로젝트 메타 데이터
* Group에는 com.luv2code.springboot.demo를 추가
* Artifact에는 앱이름을 추가(ex. mycoolapp)

의존성
* 원하는 의존성을 검색해서 추가한다

앱 실행하기
* 프로젝트를 임포트하고 완료될때까지 기다린다
* Run -> 자바 애플리케이션 으로 실행시킨다

---

### lecture 506 - Developing a REST API Controller with Spring Boot

앞서 만들었던 앱을 실행하면 에러가 발생한다
* 아무런 코드도 추가되어 있지 않기 때문이다(/ 에 대한 라우팅이 필요하다)

간단한 REST Controller 만들기
```java
@RestController
public class FunRestController {
  @GetMapping("/")
  public String sayHello() {
    return "Hello world! Time on server is " + LocalDateTime.now();
  }
}
```
* 다시 Run -> 자바 애플리케이션으로 실행하고 localhost:8080으로 접속하면 에러가 나지 않는다

---

### lecture 507 - Exploring the Spring Boot Project Structure - Part 1

앞서 생성한 메이븐 프로젝트의 구조를 살펴보기
* mvnw 파일
  - 메이븐 프로젝트를 실행시킬 수 있게 해주는 파일
  - 메이븐을 추가로 설치할 필요가 없다
  - 적절한 메이븐 버전이 설치되어 있지 않으면 알아서 설치해준다
  - mvnw.cmd는 MS Windows 운영체제를 위한 파일이다(> mvnw clean compile test)
  - mvnw.sh는 리눅스나 맥 운영체제를 위한 파일이다($ ./mvnw clean compile test)
* pom.xml 파일
  - 스프링 이니셜라이저 사이트에서 입력했던 메타 데이터와 의존성 설정 그리고 빌드 플러그인이 있다
  - 앞서 웹을 의존성으로 지정했기 때문에 파일 안에 다음 코드가 들어있다
  ```xml
  <dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
    <!-- ... -->
  </dependencies>
  ```
  - spring-boot-starter-web은 spring-web, sprinb-webmvc, hibernate-validator, tomcat, json 등을 합쳐놓은 컬렉션이다
  - 파일의 하단에는 빌드 플러그인들이 있다
* src/main/java
  - 자바 코드를 위치시킬 디렉토리
  - com.luv2code.springboot.demo.mycoolapp/MycoolappApplication.java에는 @SpringBootApplication 어노테이션이 있다
  - 이 애노테이션은 @EnableAutoConfiguration, @ComponentScan, @Configuration을 합쳐놓은 것이다 
  - @EnableAutoConfiguration은 스프링 부트의 자동 설정을 지원해주는 애노테이션이다
  - @ComponentScan은 현재 패키지를 스캐닝하고 하위 패키지들을 재귀적으로 스캐닝하는 것을 지원하는 애노테이션이다.
  - @Configuration은 외부 빈들을 등록하거나 다른 설정 클래스들을 임포트한다 
* src/main/resources
  - 자바 앱에 사용될 프로퍼티, 설정 파일들을 위치시킬 디렉토리

---

### lecture 508 - Exploring the Spring Boot Project Structure - Part 2

컴포넌트 스캐닝
* 베스트 프렉티스
  - 루트 패키지 안의 main 애플리케이션 클래스를 다른 패키지들 앞에 위치시키는 것이 좋다
  - 이렇게하면 패키지들을 검색할 때 명시적으로 베이스 패키지 네임을 설정할 필요가 없어진다
  - 앞서 src/main/java/com.luv2code.springboot.demo.mycoolapp 하위에 rest 패키지를 생성한 것도 이런 이유에서다
* 그런데 만약 패키지 계층구조에 속하지 않는 패키지들을 불러와야 한다면?
  - 예를 들어, org.acme.iot.utils, edu.cmu.wean 등을 불러온다면?
  ```java
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  
  @SpringBootApplication(
    scanBasePackages={"com.luv2code.springboot.demo.mycoolapp",
                      "org.acme.iot.utils",
                      "edu.cmu.wean"})
  public class MycoolappApplication {
  
  	public static void main(String[] args) {
  		SpringApplication.run(MycoolappApplication.class, args);
  	}
  
  }
  ```
  - 위와 같이 애노테이션 @SpringBootApplication안에 명시적으로 추가하면 된다

애플리케이션 프로퍼티
* src/main/resources에는 스프링 부트가 기본적으로 제공하는 프로퍼티들이 있다
  - application.properties파일은 초기에 비어있다
  - 여기에 서버의 포트 등을 추가할 수 있다(ex. server.port=8585)
  - 커스텀 프로퍼티도 추가할 수 있다(ex. coach.name=Mickey Mouse, team.name=The Mouse Crew)
  - 추가한 커스텀 프로퍼티는 @Value애노테이션을 이용해 불러올 수 있다
  ```java
  @RestController
  public class FunRestController {
    @Value("${coach.name}")
    private String coachName;

    @Value("${team.name}")
    private String teamName;
  }
  ```

정적 자료들
* src/main/resources/static은 html, css, js, image 등 여러 정적 자료들을 위치시키는 디렉토리다

주의할점
* 애플리케이션이 JAR로 패키징되어 있다면 src/main/webapp 디렉토리는 사용해선 안된다
* 이 디렉토리는 WAR패키징시에만 작동한다

템플릿
* src/main/resources/templates은 템플릿 엔진을 이용해 작성한 파일들을 위치시키는 디렉토리다
* FreeMaker, Thymeleaf, Mustache 등이 있다.
* Thymeleaf를 가장 많이 사용한다

테스트
* src/test/java/com.luv2code.springboot.demo.mycoolapp에는 유닛 테스트를 추가할 수 있다

---

### lecture 509 - Spring Boot Starters - A Curated List of Dependencies

스프링 애플리케이션을 만들 때 오류가 발생하기 쉬운 지점 - 의존성들을 관리하는 메이븐 설정
* 스프링 버전은?
* 스프링 MVC와 하이버네이트 앱의 어떤 버전들을 설치해야 하는가?
* 이처럼 각 의존성의 버전과 호환성 문제는 귀찮으면서도 오류를 발생시킬 수 있는 중요한 문제다

스프링 부트 스타터
* 메이븐 의존성 설정을 개별적으로 검색해 추가하지 않고 한번에 쉽게 해결할 수 있게 해준다
* 앞서 만든 프로젝트에 web의존성을 추가했다(pom.xml파일에 있다)
  ```xml
  <!-- pom.xml -->
  <dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		(...)
	</dependencies>
  ```
* spring-boot-starter-web은 spring-web, sprinb-webmvc, hibernate-validator, tomcat, json 등을 포함한 컬렉션이다
* 이 것만 추가하면 개발자들이 개별적으로 검색해 추가하던 의존성들을 호환가능한 버전으로 단번에 설치할 수 있다
* 스프링 부트 스타터는 web외에도 jpa, security, thymeleaf 등 30개가 넘는 패키지들을 가지고 있다
* 전체 리스트는 [스프링 부트 스타터 전체 리스트](www.luv2code.com/spring-boot-starters)에서 확인할 수 있다

---

### lecture 510 - Parents for Spring Boot Starters

spring boot starter parent
* 스프링 부트 앱은 메이븐에 기본으로 starter parent를 제공한다
```xml
(...)
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.1.4.RELEASE</version>
	<relativePath/> <!-- lookup parent from repository -->
</parent>
(...)
```
* 메이븐은 starter parent에 여러 가지를 설정한다
  - 기본 컴파일 레벨(자바 버전 - 기본으로 8이 설정된다)
  - UTF-8 인코딩
  - 기타
* 자바 버전을 바꾸고 싶다면
```xml
(...)
<properties>
		<java.version>1.8</java.version>
</properties>
(...)
```
* 여기에 설정된 버전을 수정하면된다
* spring-boot-starter-*형식의 의존성들은 버전을 기입하지 않아도 된다
* spring boot starter-parent아래에 스프링 부트의 버전만 있으면 나머지 의존성들은 starter parent의 버전을 상속받는다 