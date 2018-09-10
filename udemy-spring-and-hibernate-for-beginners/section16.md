# Section 16. Spring MVC Form Validation - Validating Number Ranges and Regular Expressions

### Validating a Number Range - Overview

일정 범위 숫자 검증
* Free Passes라는 필드에서 숫자를 입력받을 것이다
* 0에서 10 사이의 숫자를 입력받아야만 한다

개발과정
1. Customer 클래스에 검증 룰 추가
2. HTML 폼에 에러 메시지 출력
3. 컨트롤러 클래스에 검증 등록
4. 확인 페이지 갱신

Customer 클래스에 검증 룰 추가
```java
import javax.validation.constraints.Max;
import javax.validation.constraints.Min;

// (...)

// freePasses 필드 추가
@Min(value=0, message="must be greater than or equal to zero")
@Max(value=10, message="must be less than or equal to 10")
private int freePasses;

// (...)

// 게터 세터 메서드 추가
public int getFreePasses() {
		return freePasses;
}
public void setFreePasses(int freePasses) {
	  this.freePasses = freePasses;
}
```

HTML 폼에 에러 메시지 출력
```jsp
<!-- customer-form.jsp의 submit 버튼 위에 추가 -->
Free passes: <form:input path="freePasses" />
<form:errors path="freePasses" cssClass="error" />
```

컨트롤러 클래스에 검증 등록
* 앞서 작성한 것에 추가적인 로직은 필요없다

확인 페이지 갱신
```jsp
<!-- customer-confirmation.jsp에 추가 -->
Free passes: ${customer.freePasses}
```

---

### lecture 154. Applying Regular Expressions - Overview

정규 표현식을 이용한 검증
* 문자열에 검색 패턴을 적용
  - 이 패턴은 문자열을 찾거나 매칭하는데 사용한다
* 정규 표현식은 언어 자체의 기능이다. 이미 어느 정도 지식이 있다고 가정한다
* <a href="https://docs.oracle.com/javase/tutorial/essential/regex/">자바 정규표현식</a>

정규표현식을 이용한 우편 번호 검증
* 우편 번호를 입력받는 폼을 추가한다
* 사용자는 5자의 아라비아 숫자인 문자열을 입력해야만 한다
  - 이를 검증하는 정규표현식을 작성할 것이다

개발과정
1. Customer 클래스에 검증 규칙 추가
2. HTML 폼에 에러 메시지 출력
3. 확인 페이지 갱신

Customer 클래스에 검증 규칙 추가
```java
import javax.validation.constraints.Pattern;

// 우편 번호 필드 추가
@Pattern(regexp="^[a-zA-Z0-9]{5}", message="only 5 chars/digits")
private int postalCode;

// 게터 세터 메서드 추가
public int getPostalCode() {
		return postalCode;
}
public void setPostalCode(int postalCode) {
	  this.postalCode = postalCode;
}
```

HTML 폼에 에러 메시지 출력
```jsp
<!-- customer-form.jsp에 추가 -->
Postal code: <form:input path="postalCode" />
<form:errors path="postalCode" cssClass="error" />

<br />
```

확인 페이지 갱신
```jsp
<!-- customer-confirmation.jsp에 추가 -->
<br />

Postal Code: ${customer.postalCode}
```

---

### lecture 156. How to make Integer Field Required: freePasses

freePasses를 정수 필드로 만들기
```java
// Student.java

@NotNull(message="is required")
@Min(value=0, message="must be greater than or equal to zero")
@Max(value=10, message="must be less than or equal to 10")
private int freePasses;
```
* 기존 코드에 @NotNull 어노테이션을 추가했다
* 그런데 int값에 @NotNull을 사용하면 형변환 실패 에러가 발생한다
* 필드와 게터 세터의 int를 Integer로 변경하면 문제가 해결된다

---

### lecture 157. How to Handle String input for Integer Fields - Custom Message

freePasses 필드에 문자열 값이 들어오면
* 형변환 실패 에러가 발생한다
* 이처럼 Integer 필드에 문자열 값이 들어왔을때를 위한 처리가 필요하다

개발과정
1. 커스텀 에러 메시지를 만든다
   * src/resources/meesages.properties
2. 스프링 설정 파일에 커스텀 에러 메시지를 로드한다
   * WebContent/WEB-INF/spring-mvc-demo-servlet.xml

커스텀 에러 메시지 만들기
```txt
typeMismatch.customer.freePasses=Invalid number
```
* typeMismatch는 에러 타입이다
* customer는 스프링 모델 attribute 이름이다
* freePasses는 필드명이다
* Invalid number는 커스텀 메시지다

스프링 설정 파일에 커스텀 에러 메시지를 로드하기
```xml
<!-- Load custom message resources -->
<bean id="messageSource"
    class="org.springframework.context.support.ResourceBundleMessageSource">
    
    <property name="basenames" value="resources/messages" />

</bean>
```
* value에 resources/messages를 입력하면 스프링이 자동으로 뒤에 .properties를 추가해준다

---

### lecture 159. How to Handle String input for Integer Fields - Deep Dive

CustomController에서 바인딩한 결과 객체를 검사하기
```java
@RequestMapping("/processForm")
public String processForm(@Valid @ModelAttribute("customer") Customer theCustomer,
		                  BindingResult theBindingResult) {
	
	System.out.println("Last name: " + theCustomer.getLastName());
	
	System.out.println("Binding result: " + theBindingResult);
	
	System.out.println("\n\n\n\n");
	
	if (theBindingResult.hasErrors()) {
		return "customer-form";
	} else {
		return "customer-confirmation";
	}
}
```
* theBindingResult의 로그를 출력해본다

```txt  
Binding result: org.springframework.validation.BeanPropertyBindingResult: 1 errors
Field error in object 'customer' on field 'freePasses': rejected value [abdasb]; codes [typeMismatch.customer.freePasses,typeMismatch.freePasses,typeMismatch.java.lang.Integer,typeMismatch]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [customer.freePasses,freePasses]; arguments []; default message [freePasses]]; default message [Failed to convert property value of type 'java.lang.String' to required type 'java.lang.Integer' for property 'freePasses'; nested exception is java.lang.NumberFormatException: For input string: "abdasb"]
```
* 위의 에러가 출력된다
* codes 뒤에 있는 배열의 typeMismatch.customer.freePasses는 스프링에서 자동으로 제공해준다
* 이를 앞서 만든 파일로 오버라이딩 해줘서 커스텀하게 사용했던 것이다

---