# Section8. Spring Configuration with Java Annotations - Dependency Injection

### lecture 59. Constructor Injection - Overview

스프링 오토와이어링
* 의존성 주입을 위해 스프링은 오토와이어링을 한다
* 스프링은 프로퍼티에 매치되는 클래스를 찾는다
  - 클래스 혹은 인터페이스
* 스프링은 찾아낸 클래스를 자동적으로 주입해준다

오토와이어링 예제
* FortuneService를 Coach의 구상 클래스에 주입한다
* 스프링은 @Component 어노테이션이 붙은 클래스를 스캔한다
* FortuneService를 구상한 클래스를 찾는다
* 만약 구상 클래스가 있다면 해당 클래스를 주입해준다

오토와이어링 주입 방식
* 생성자 주입
* 세터 메서드 주입
* 필드 주입

생성자 주입 개발 과정
* 의존하는 인터페이스와 클래스를 정의한다
* 주입을 위한 클래스에 생성자를 만든다
* @Autowired 어노테이션으로 의존성 주입을 설정한다

의존하는 인터페이스와 클래스 정의하기
```java
public interface FortuneService {

	public String getFortune();
}

import org.springframework.stereotype.Component;

@Component
public class HappyFortuneService implements FortuneService {

	@Override
	public String getFortune() {
		return "Today is your lucky day!";
	}

}
```

Coach 인터페이스에 getDailyFortune 메서드를 추가
```java
public interface Coach {

	public String getDailyWorkout();
	
	public String getDailyFortune();
}
```


주입을 위한 클래스에 생성자를 만든다
```java
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {
	
	private FortuneService fortuneService;
	
	public TennisCoach(FortuneService theFortuneService) {
		fortuneService = theFortuneService;
	}

	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

}
```

@Autowired 어노테이션으로 의존성 주입을 설정한다
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {
	
	private FortuneService fortuneService;
	
	@Autowired
	public TennisCoach(FortuneService theFortuneService) {
		fortuneService = theFortuneService;
	}

	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

}
```

getDailyForunte 메서드를 구현한다
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {
	
	private FortuneService fortuneService;
	
	@Autowired
	public TennisCoach(FortuneService theFortuneService) {
		fortuneService = theFortuneService;
	}

	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}
	
	@Override
	public String getDailyFortune() {
		return fortuneService.getFortune();
	}

}
```

---

### lecture 64. Setter Injection - Overview

세터 인젝션으로 오토와이어링하기
* Coach 구상 클래스에 FortuneService를 주입
* 스프링은 @Component 어노테이션을 사용하는 클래스를 스캐닝한다
* FortuneService의 구상클래스가 있으면 주입해준다

주입받을 클래스에 세터 메서드 만들기
```java
@Component
public class TennisCoach implements Coach {
	
	private FortuneService fortuneService;
	
  public TennisCoach() {
  }

	public void setFortuneService(FortuneService fortuneService) {
		this.fortuneService = fortuneService;
	}

  // ...
}
```

Autowired 어노테이션으로 의존성 주입
```java
@Component
public class TennisCoach implements Coach {
	
	  private FortuneService fortuneService;
	  
	  public TennisCoach() {}
	  
    @Autowired
	  public void setFortuneService(FortuneService fortuneService) {
	  	  this.fortuneService = fortuneService;
	  }

    // ...
}
```

---

### lecture 66. Method Injection

세터 메서드가 아닌 메서드로 의존성 주입하기
```java
@Autowired
public void doSomeCrazyStuff(FortuneService theFortuneService) {
		fortuneService = theFortuneService;
}
```
* 스프링은 어떤 메서드든 @Autowired 어노테이션을 사용하면 의존성을 주입해준다

---

### lecture 67. Field Injection - Overview

필드 주입
* 필드 값을 세팅해서 의존성을 주입할 수 있다
* private 필드에도 의존성 주입이 가능하다
* 필드 주입을 구현하기 위해 뒷단에서는 Reflection이라고 불리는 자바 기술을 사용한다

Autowired 어노테이션을 이용한 의존성 주입
* 필드에 직접 적용하기
* 세터 메서드는 불필요하다
```java
@Component
public class TennisCoach implements Coach {
	
	@Autowired
	private FortuneService fortuneService;
	
	public TennisCoach() {
	}

	// ...
}
```
* 다른 파일은 수정하지 않아도 이전과 같은 결과를 낸다

---

### lecture 69. Which Injection Type Should You Use?

생성자, 세터 메서드, 필드 중 어떤 주입이 가장 좋은가?
* 정답은 없다. 다소 종교적인 논쟁이다
* 코드를 작성하는 프로그래머들이 가장 편한 방법을 사용하면 된다

---

### lecture 70. Qualifiers for Dependency Injection - Overview

앞서 오토와이어링을 사용했다
* 덕분에 스프링이 자동적으로 FortuneService의 구상 클래스를 스캔해서 주입해준다
* 그런데 구상 클래스가 여러개라면 같은 방식으로는 NoUniqueBeanDefinitionException 에러가 발생한다

Qualifier
* Qualifier 어노테이션에 인자로 사용할 빈의 id를 문자열로 넘겨준다
* 생성자, 세터 메서드, 필드 주입 어느 것에든 사용할 수 있다
```java
@Component
public class TennisCoach implements Coach {
	
	@Autowired
	@Qualifier("databaseFortuneService")
	private FortuneService fortuneService;
```

---

### lecture 73. Annotations - Default Bean Names - The Special Case

스프링에서 어노테이션을 사용할 때 빈의 이름을 정하는 기본 규칙
* 어노테이션의 값이 지정되어 있지 않으면 첫번째 문자를 소문자로 변경한 문자열을 사용한다
* 그런데 첫번째, 두번째 글자가 모두 대문자라면 위의 규칙을 적용하지 않는다

---