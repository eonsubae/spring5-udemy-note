# Section5. Spring Dependency Injection - XML Configuration

### lecture 29. Spring Dependency Injection - Overview

DI의 정의
* 의존성을 제공하는 책임을 다른 객체에 위임하는 것

자동차의 예
* 자동차를 온디맨드로 만든다고 가정하면
* 자동차에는 엔진, 시트 등의 여러 부품이 있다
  - 자동차는 이 부품들에 의존한다
  - 따라서 자동차를 만들기 위해서는 이 부품들을 자동차에 주입해야 한다
* 프로그래밍
  - 외부 엔티티에 의존성 생성을 위임

앞서 만들었던 Coach 예제
* 코치 객체는 스프링이 제공하는 여러 설정들을 사용한다
  - 여러 설정들은 헬퍼 객체라고 할 수 있다
  - 여기서 헬퍼 객체를 의존성이라고 생각하면 된다
  - 앞서 코치 객체를 직접 만들지 않고 여러 의존성들을 주입받아서 사용했다
  - 스프링 프레임워크는 여러 의존성들을 제공해준다
  
자주 사용하는 의존성들
* Constructor Injection
* Setter Injection
* Auto-wiring
  - 차후 다룰 것이다

Constructor Injection 과정
* 의존하는 인터페이스와 클래스를 정의한다
* 주입을 위해 클래스에 생성자를 만든다
* 스프링 설정 파일에 의존성 주입을 설정한다

Step1. Define the dependency interface and class
```java
public interface FortuneService {

    public String getFortune();

}

public class HappyFortuneService implements FortuneService {

    public String getFortune() {
      return "Today is your lucky day!";
    }

}
```
* getFortune을 구현해야 하는 FortuneService 인터페이스를 생성하고
* 이를 구현한 HappyFortuneService를 만들었다

Step2. Create a constuctor in your class for injections
```java
public class BaseballCoach implements Coach {
  
  private FortuneService fortuneService;

  public BaseballCoach(FortuneService theFortuneService) {
    fortuneService = theFortuneService
  }
}
```
* 생성자를 호출해서 FortuneService 의존성을 주입받고 있다

Step3. Configure the dependency injection in Spring config file
```xml
<bean id="myFortuneService"
    class="com.luv2code.springdemo.HappyFortuneService">
</bean>

<bean id="myCoach"
    class="com.luv2code.springdemo.BaseballCoach">
        <constructor-arg ref="myFortuneService" />
</bean>
```
* 빈에 의존성을 등록한다
  - BaseballCoach는 HappyFortuneService을 주입받으므로 이를 설정해줘야한다
  - <constructor-arg ref="..." />로 생성자에서 의존성을 주입받는 것을 설정하고있다

---

### lecture 30. Spring Dependency Injection - Behind the Scenes

앞서 작성했던 xml 코드를 다시 보자
```xml
<bean id="myFortuneService"
    class="com.luv2code.springdemo.HappyFortuneService">
</bean>

<bean id="myCoach"
    class="com.luv2code.springdemo.BaseballCoach">
        <constructor-arg ref="myFortuneService" />
</bean>
```
* 위 코드를 작성하면 스프링 프레임워크는 보이지 않는 곳에서 특정 작업을 수행한다

위 코드를 가지고 Spring이 해주는 것
```java
HappyFortuneService myFortuneService = new HappyFortuneService();
BaseballCoach myCoach = new BaseballCoach(myFortuneService);
```
* myFortuneService 객체를 생성해서 BaseballCoach의 생성자에 넘겨준다

---

### lecture 31. Spring Dependency Injection - Write Some Code - Part 1
```java
// FortuneService.java
public interface FortuneService {

	public String getFortune();
	
}

// HappyFortuneService.java
public class HappyFortuneService implements FortuneService {

	@Override
	public String getFortune() {
		return "Today is your lucky day!";
	}

}

// Coach.java
public interface Coach {
	
    public String getDailyWorkout();
    
    public String getDailyFortune(); // 추가
}
```
* FortuneService, HappyFortuneService 파일을 생성해 코드를 작성한다
* 그리고 Coach 인터페이스에 getDailyFortune을 추가한다
* 이제 Coach를 구현한 클래스들에 getDailyFortune을 추가해야 한다

---

### lecture 32. Spring Dependency Injection - Write Some Code - Part 2
```java
public class BaseballCoach implements Coach {
	
	// 의존성을 위해 private 필드를 정의
	private FortuneService fortuneService;
	
	// 의존성을 주입받기 위해 생성자 정의
	public BaseballCoach(FortuneService theFortuneService) {
		fortuneService = theFortuneService;
	}

	@Override
	public String getDailyWorkout() {
		return "Spend 30 minutes on batting practice";
	}

	@Override
	public String getDailyFortune() {
		// fortune을 얻기 위해 fortuneSerivce를 사용
		return fortuneService.getFortune();
	}
}

```
* 생성자에서 의존성을 주입받아 private 필드에 지정했다
* getDailyFortune 메서드에서 주입받은 필드를 사용하고 있다

```xml
<bean id="myFortune"
    class="com.luv2code.springdemo.HappyFortuneService">
</bean>
    
<bean id="myCoach"
    class="com.luv2code.springdemo.BaseballCoach">
    <constructor-arg ref="myFortune" />
</bean>
```
* bean에 생성자 의존성 등록

```java
public class HelloSpringApp {

	public static void main(String[] args) {

		// 스프링 컨테이너 파일 로딩
		ClassPathXmlApplicationContext context =
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// 스프링 컨테이너로부터 빈을 검색하기
		Coach theCoach = context.getBean("myCoach", Coach.class);
		
		// 빈의 메소드 호출하기
		System.out.println(theCoach.getDailyWorkout());
		System.out.println(theCoach.getDailyFortune());
		
		// 컨텍스트 종료
		context.close();
	}
}
```
* getDailyFortune이 잘 실행되는지 확인하자

---

### lecture 32. Spring Dependency Injection - Write Some Code - Part 3
```java
public class TrackCoach implements Coach {

	private FortuneService fortuneService;
	
	public TrackCoach() {}
	
	public TrackCoach(FortuneService fortuneService) {
		this.fortuneService = fortuneService;
	}

	@Override
	public String getDailyWorkout() {
		return "Run a hard 5k";
	}

	@Override
	public String getDailyFortune() {
		return "Just Do It: " + fortuneService.getFortune();
	}
}
```
* TrackCoach도 의존성을 주입받아 getDailyFortune을 구현했다

```xml
(...)
<bean id="myCoach"
    class="com.luv2code.springdemo.TrackCoach">
    <constructor-arg ref="myFortune" />
</bean>
```
* 마찬가지로 bean에 생성자 의존성을 등록한다
* 다시 HelloSpringApp을 실행해서 확인해보자

---

### lecture 35. Setter Injection - Overview

Setter Injection
* 스프링이 세터 메서드를 이용해서 의존성을 주입해주는 기능

Setter Injection의 과정
* 클래스에 주입을 위한 세터 메서드를 만든다
* 스프링 config 파일에 의존성 주입을 설정한다

클래스에 주입을 위한 세터 메서드 만들기
```java
public class CricketCoach implements Coach {
	
	private FortuneService fortuneService;
	
	public CricketCoach() {}
	
	public void setFortuneService(FortuneService fortuneService) {
		this.fortuneService = fortuneService;
	}

    (...)
}
```

스프링 config 파일에 의존성 주입 설정하기
```xml
<bean id="myFortuneService"
    class="com.luv2code.springdemo.HappyFortuneService">
</bean>

<bean id="myCoach"
    class="com.luv2code.springdemo.TrackCoach">
    <constructor-arg ref="myFortuneService" />
</bean>
    
<bean id="myCricketCoach"
    class="com.luv2code.springdemo.CricketCoach">        
  <property name="fortuneService" ref="myFortuneService"/>
</bean>
```
* name에 설정한 값을 세터 메서드인 setFortuneService의 이름으로 사용해야 한다
  - 이 때 첫글자는 대문자로 변경해야 한다

스프링에서 사용하기
```java
HappyFortuneService myFortuneService = new HappyFortuneService();
CricketCoach myCricketCoach = new CricketCoach();
myCricketCoach.setFortuneSerivce(myFortuneService);
```

### lecture 37에서 만든 SetterDemoApp 추가
```java
public class SetterDemoApp {

	public static void main(String[] args) {
		
		// load the spring configuration file
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// retrieve bean from spring container
		CricketCoach theCoach = context.getBean("myCricketCoach", CricketCoach.class);
		
		// call methods on the bean
	    System.out.println(theCoach.getDailyWorkout());
	    System.out.println(theCoach.getDailyFortune());
		
		// close the text
	    context.close();
	}
}
```
* id가 myCricketCoach인 빈을 가져와서 사용하고 있다

---

### lecture 38. Injecting Literal Values - Overview

리터럴 값을 주입하기
* 예시를 위해 이메일 주소와 팀명을 주입할 것이다

단계
* 주입을 위해 클래스에 세터 메서드를 생성한다
* 스프링 설정 파일에 주입을 설정한다

주입을 위해 클래스에 세터 메서드를 생성하기
```java
public class CricketCoach implements Coach {
	
	private String emailAddress;
	private String team;

// ...

public void setEmailAddress(String emailAddress) {
		
}
	
public void setTeam(String team) {
		
}

// ...
```
```xml
 <bean id="myCricketCoach"
        class="com.luv2code.springdemo.CricketCoach">
        
      <property name="fortuneService" ref="myFortuneService"/>
      
      <property name="emailAddress" value="thebestcoach@luv2code.com" />
        
      <property name="team" value="Sunrisers Hyderabad" />  
 </bean>
```
* emailAddress를 name으로 가지는 프로퍼티는 setEmailAddress를 호출한다
  - team도 유사하게 setTeam을 호출한다

### lecture 40. FAQ: Why do we use CricketCoach class instead of Coach interface?

```java
public class SetterDemoApp {

	public static void main(String[] args) {
		
		// load the spring configuration file
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// retrieve bean from spring container
		CricketCoach theCoach = context.getBean("myCricketCoach", CricketCoach.class);
    // ...
    }
}
```

위 코드에서 context.getBean의 파라미터로 CricketCoach 클래스를 보낸 이유
* Coach 클래스를 보내면 Coach 인터페이스 외에 자체적으로 추가한 메서드를 사용할 수 없다
* 반면 위 코드처럼 CricketCoach 클래스를 보내면 Coach 인터페이스에는 없는 emailAddress와 team의 게터와 세터 메서드 같이 다른 메서드들을 사용할 수 있다

---

### lecture 41. Injecting Values from a Properties File - Overview

프로퍼티 파일에 있는 값들을 주입하기
* 프로퍼티 파일을 생성한다
* 스프링 설정 파일에서 프로퍼티 파일을 불러온다
* 프로퍼티 파일에 있는 값들을 참조한다

프로퍼티 파일 생성하기
```
foo.email = myeasycoach@luv2code.com
foo.team = Royal Challengers Bangalore
```
* src 폴더에 sport.properties라는 파일을 생성한다
* 그리고 위의 값들을 생성한 파일에 입력해준다

스프링 설정 파일에서 프로퍼티 파일을 불러오기
```xml
<context:property-placeholder location="classpath:sport.properties" />
```
* classpath에 파일 이름을 넣어준다

프로퍼티 파일에 있는 값들을 참조하기
```xml
<property name="emailAddress" value="${foo.email}" />
<property name="team" value="${foo.team}" />
```
* ${}형식으로 파일에 작성한 key를 적어주면 된다

SetterDemoApp은 수정하지 않고도 실행하면 파일에서 참조해온 값들에 맞게 호출해준다

---