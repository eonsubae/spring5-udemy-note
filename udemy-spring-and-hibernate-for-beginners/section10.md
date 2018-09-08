# Section 10. Spring Configuration with Java Code

### lecture 85. Spring Configuration with Java Code - Overview

XML 없이 자바 코드로 설정하기
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.ComponentScan;

@Configuration
@ComponentScan("com.luv2code.springdemo")
public class SportConfig {

}
```

### lecture 87. Defining Spring Beans with Java Code - Overview

메서드를 사용해 빈을 내보내기
```java
@Configuration
public class SportConfig {

    @Bean
    public FortuneService happyFortuneService() {
        return new HappyFortuneService();
    }

    @Bean
    public Coach swimCoach(FortuneService fortuneService) {
        SwimCoach mySwimCoach = new SwimCoach( happyFortuneService() );

        return SwimCoach;
    }
}
```
* 메서드명인 swimCoach가 빈의 id가 된다
* component scan을 사용하지 않는다
  - 설정 클래스에서 개별적으로 빈을 정의한다
* 의존하는 빈을 메서드를 이용해 등록한 후 생성자로 넘겨준다
  - SwimCoach가 HappyFortuneService를 주입받고 있다

전체 코드
```java
// SportConfig.java

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.luv2code.springdemo")
public class SportConfig {

	@Bean
	public FortuneService sadFortuneService() {
		return new SadFortuneService();
	}
	
	@Bean
	public Coach swimCoach() {
		SwimCoach mySwimCoach = new SwimCoach(sadFortuneService());
		return mySwimCoach;
	}
}

// JavaConfigDemoApp.java

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class JavaConfigDemoApp {

	public static void main(String[] args) {
        
		// read spring config file
		AnnotationConfigApplicationContext context =
				new AnnotationConfigApplicationContext(SportConfig.class);
		
		// get the bean from spring container 
		Coach theCoach = context.getBean("swimCoach", Coach.class);
		
		// call a method on the bean
		System.out.println(theCoach.getDailyWorkout());
		System.out.println(theCoach.getDailyFortune());
		
		// close the context
		context.close();
	}

}

// SwimCoach.java

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class SwimCoach implements Coach {
	
	private FortuneService fortuneService;

	public SwimCoach(FortuneService theFortuneService) {
		this.fortuneService = theFortuneService;
	}
	
	@Override
	public String getDailyWorkout() {
		return "Swimming";
	}

	@Override
	public String getDailyFortune() {
		return fortuneService.getFortune();
	}

}

// SadFortuneService.java

public class SadFortuneService implements FortuneService {

	@Override
	public String getFortune() {
		return "Today is a sad day";
	}

}

```
* ClassPathXmlApplicationContext가 아닌 AnnotationConfigApplicationContext을 사용한다

---

### lecture 90. Injecting Values from Properties File - Overview

파일 생성
```txt
foo.email = myeasycoach@luv2code.com
foo.team = Awesome Java Coders
```
* 파일명은 sport.properties다

스프링 설정 클래스에서 파일 불러오기
```java
@Configuration
@PropertySource("classpath:sport.properties")
public class SportConfig {
  // ...
}
```

파일에서 값들을 참조하기
```java
public class SwimCoach implements Coach {

  @Value("${foo.email}")
  private String email;

  @Value("${foo.team}")
  private String team;
}
```