# Spring & Hibernate for Beginners

## Section 4. Spring Inversion of Control - XML Configuration

### lecture 20. What is Inversion of Control?

Inversion of Control(IoC) 
* 객체의 구조와 관리를 외부에 위임하는 접근 방식을 지칭한다
* 객체 팩토리에 객체의 생성과 관리를 위임한다

IoC의 이해를 위한 예제
* App은 BaseballCoach에게 getDailyWorkout() 함수를 통해 해야할 일을 알아낸다
* 그런데 요구사항에 따라 야구 외에도 다른 스포츠 코치로 쉽게 대체할 수 있게 만들고 싶다면?

Code demo
* MyApp.java : main method
* BaseballCoach.java
* Coach.java : interface after refactoring
* TrackCoach.java

```java
// BaseballCoach.java

public class BaseballCoach {

	public String getDailyWorkout() {
		return "Spend 30 minutes on batting practice";
	}
}

// MyApp.java

public class MyApp {

	public static void main(String[] args) {
		// create the object
		BaseballCoach theCoach = new BaseballCoach();
		
		// use the object
		System.out.println(theCoach.getDailyWorkout());
	}

}
```
* 위 코드를 실행하면 Spend 30 minutes on batting practice가 출력된다

다른 스포츠 코치로 쉽게 대체할 수 있도록 인터페이스 만들기
```java
// Coach.java
public interface Coach {
	
    public String getDailyWorkout();
    
}

// BaseballCoach.java
public class BaseballCoach implements Coach {

	@Override
	public String getDailyWorkout() {
		return "Spend 30 minutes on batting practice";
	}
}

// MyApp.java

public class MyApp {

	public static void main(String[] args) {
		// create the object
		Coach theCoach = new BaseballCoach();
		
		// use the object
		System.out.println(theCoach.getDailyWorkout());
	}

}
```
* getDailyWorkout을 구현해야 하는 Coach 인터페이스를 만들었다
* BaseballCoach는 인터페이스를 상속받아 getDailyWorkout을 오버라이딩한다
* 이제 main 함수에서는 제네릭하게 Coach를 사용할 수 있다

---

### lecture 22. Code Demo - Rough Prototype Part 2

```java
// TrackCoach.java

public class TrackCoach implements Coach {

	@Override
	public String getDailyWorkout() {
		return "Run a hard 5k";
	}

}

// MyApp.java

public class MyApp {

	public static void main(String[] args) {
		// create the object
		Coach theCoach = new TrackCoach();
		
		// use the object
		System.out.println(theCoach.getDailyWorkout());
	}

}
```
* TrackCoach 클래스를 만들기 전에 MyApp 클래스에서 BaseballCoach를 TrackCoach로 바꾸면 에러가 뜬다
  - 이 때 좌측에 있는 x 버튼을 누르면 이클립스가 자동으로 인터페이스까지 지정해서 클래스를 생성해준다
* 실행하면 Run a hard 5k가 출력된다
* 이제 쉽게 다른 스포츠 코치들로 변경할 수 있다
* 그러나 아직도 MyApp에서 new TrackCoach() 부분이 하드코딩되어있다
* config 파일을 작성해서 이를 대체할 수 있다

---

### lecture 23. Spring Inversion of Control - Overview

스프링 컨테이너
* 두가지 핵심 기능
  - 객체를 생성하고 관리(Ioc)
  - 객체의 의존성을 주입(Dependency Injection)

스프링 컨테이너를 설정하는 세 가지 방법
* XML 설정 파일(레거시지만 많은 앱들이 아직도 사용하고 있다)
* 자바 어노테이션 - 모던한 방법
* 자바 소스 코드 - 모던한 방법

스프링 개발 과정
* 스프링 Bean들을 설정한다
* 스프링 컨테이너를 만든다
* 스프링 컨테이너로부터 빈들을 검색한다

예제로 살펴보기
* Step1. 스프링 Bean 설정
```java
// File: applicationContext.xml

<beans ...>

    <bean id="myCoach"
        class="com.luv2code.springdemo.BaseballCoach">
    </bean>

</beans>
```
* bean의 id에는 별칭을 지정한다
* class에는 실행할 클래스 전체를 기록한다

* Step2. 스프링 컨테이너 만들기
  - 스프링에서 스프링 컨테이너는 ApplicationContext로 알려져있다
  - XML이나 어노테이션, 제네릭 웹앱 등을 실행하는데 특화되어 있다
```java
ClassPathXmlApplicationContext context = 
    new ClassPathXmlApplicationContext("applicationContext.xml");
```

* Stpe3. 컨테이너로부터 빈을 검색하기
```java
Coach theCoach = context.getBean("myCoach", Coach.class);
```
* 앞서 만들었던 myCoach라는 별칭을 가진 빈을 불러와서 사용한다

---

### lecture 24. FAQ: What is a Spring Bean?

스프링 빈
* 스프링 컨테이너에 의해 생성되는 자바 객체다
* <a href="https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-introduction">스프링 공식 문서</a>에 보다 자세한 설명이 있다

---