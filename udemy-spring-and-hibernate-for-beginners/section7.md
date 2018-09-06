# Section7. Spring Configuration with Java Annotations - Inversion of Control

### lecture 52. Annotations Overview - Component Scanning

자바 어노테이션
* 자바 클래스에 추가하는 특별한 레이블/마커
* 클래스에 메타 데이터를 제공해준다
* 컴파일 시 또는 특별한 작업을 위한 런타임시에 동작한다

예제
```java
public class TrackCoach implements Coach {

  // ...

	@Override
	public String getDailyWorkout() {}

}
```
* 계속 작성해왔던 TrackCoach에 있는 @Override는 어노테이션이다
  - 컴파일시 컴파일러에 이 메서드가 주어진 인터페이스를 구현하는 오버라이딩된 메서드임을 알려준다
  - 그러면 컴파일러는 오버라이드가 문법에 맞게 잘 되어있는지 체크한다

왜 스프링은 어노테이션으로 설정할까?
* XML은 지나치게 코드량이 많다
* 어노테이션은 스프링 빈들에 어노테이션을 추가만하면 설정할 수 있다
* 어노테이션은 XML 설정을 최소화한다

컴포넌트 클래스들을 스캐닝하기
* 스프링은 특별한 어노테이션을 사용하는 클래스들을 스캔한다
* 스캐닝한 클래스를 자동적으로 스프링 컨테이너에 빈으로 등록한다

개발과정
* 스프링 설정 파일에 컴포넌트 스캐닝을 가능하게 만들기
* @Component 어노테이션을 자바 클래스에 추가하기
* 스프링 컨테이너로부터 빈을 검색하기

스프링 설정 파일에 컴포넌트 스캐닝을 가능하게 만들기
```xml
<beans ...>
  <context:component-scan base-package="com.luv2code.springdemo" />>
</beans>
```
* base-package에 스프링 패키지를 등록하면 스프링 프레임워크는 재귀적으로 패키지를 스캔한다 

@Component 어노테이션을 자바 클래스에 추가하기
```java
@Component("thatSillyCoach")
public class TennisCoach implements Coach {

  @Override
  public String getDailyWorkout() {
    return "Practice your backhand volley";
  }
}
```
* 어노테이션에 인자로 넘겨준 문자열은 빈의 id값이다

스프링 컨테이너로부터 빈을 검색하기
```java
Coach theCoach = context.getBean("thatSillyCoach", Coach.class);
```
* 이 부분은 xml 설정과 같다

---

### lecture 56. Default Component Names - Overview

@Component 어노테이션의 파라미터
* 앞서 TennisCoach 클래스의 어노테이션의 파라미터로 thatSillyCoach라는 문자열을 넘겨서 id를 지정해줬다
* 이를 지정하지 않으면 스프링 프레임워크는 자동적으로 id를 지정해준다
  - 클래스명의 첫글자를 소문자로 바꿔서 지정한다
  - ex. TennisCoach 클래스의 id -> tennisCoach

```java
@Component
public class TennisCoach implements Coach {

	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

}

public class AnnotationDemoApp {

	public static void main(String[] args) {
        
		// read spring config file
		ClassPathXmlApplicationContext context =
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// get the bean from spring container 
		Coach theCoach = context.getBean("tennisCoach", Coach.class);
		
		// call a method on the bean
		System.out.println(theCoach.getDailyWorkout());
		
		// close the context
		context.close();
	}

}
```

---