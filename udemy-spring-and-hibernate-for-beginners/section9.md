# Sectinon 9. Spring Configuration with Java Annotations - Bean Scopes and Lifecycle Methods

어노테이션을 이용한 빈 스코프
* 앞서 XML을 이용하는 방법을 살펴본바 있다
* 스코프는 빈의 생명주기를 참조한다
  - 빈이 얼마나 오래 살아있는가?
  - 인스턴스가 얼마나 생성되었는가?
  - 빈은 어떤 방식으로 공유되는가?

싱글턴
* 모든 빈의 기본 스코프
* 빈은 오직 하나의 인스턴스만 생성된다
* 메모리에 캐시된다
* 빈에 대한 모든 요청은 동일한 빈에 대한 공유된 참조를 반환한다

어노테이션으로 빈 스코프 지정하기
```java
@Component
@Scope("singleton")
public class TennisCoach implements Coach {
    // ...
}
```
* 싱글턴 지정


```java
@Component
@Scope("prototype")
public class TennisCoach implements Coach {
    // ...
}
```
* 프로토타입 지정
  - 각 요청마다 새로운 객체가 생성된다

---

### lecture 79. Bean Lifecycle Method Annotations - Overview

초기화 및 삭제 직전에 작업 추가하기
* @PostConstruct로 빈을 초기화시 특정 작업을 수행할 수 있다
  - 생성자와 의존성 주입이 끝난 다음에 실행된다
```java
@PostConstruct
public void doMyStartupStuff() {
    // ...		
}
```

* @PreDestroy로 빈을 삭제하기 직전에 특정 작업을 수행할 수 있다
```java
@PreDestroy
public void doMyCleanupStuff() {
	  // ...	
}
```

---

### lecture 81. HEADS UP - FOR JAVA 9 USERS - @PostConstruct and @PreDestroy

자바 버전 9 이상일 때 발생하는 에러
* javax.annotation이 기본 클래스패스에서 제거되어 이클립스가 찾을 수 없다

해결책
* <a href="http://central.maven.org/maven2/javax/annotation/javax.annotation-api/1.2/javax.annotation-api-1.2.jar">javax.annotation-api-1.2.jar</a>를 다운받는다
* JAR 파일을 프로젝트 안에 있는 lib 폴더에 넣는다
* 프로젝트를 우클릭하고 Properties를 선택한다
* 왼쪽 편에 있는 Java Build Path를 클릭한다
* 대화창 상단 중앙에 있는 Libraries를 클릭한다
* Classpath를 클릭하고 Add JARs 버튼을 누른다
* (your-project)/lib/javax.annotation-api-1.2.jar 다운받았던 파일의 경로를 지정한다
* OK를 클릭하고 Apply and Close를 누른다

---