# Section 17. Spring MVC Form Validation - Creating Custom Validation Rules

### lecture 161. Custom Form Validation - Overview - Part 1

커스텀 밸리데이션
* 비즈니스 로직에 기반한 커스텀 밸리데이션 작성하기
  - ex. LUV로 시작하는 Course Code
  - 스프링 MVC는 우리가 작성한 커스텀 밸리데이션을 호출한다
  - 커스텀 밸리데이션은 불리언 타입의 값을 반환한다

커스텀 자바 애노테이션 생성
* @CourseCode 만들기
```java
@CourseCode(value="LUV", message="must start with LUV")
private String courseCode;
```

개발 과정
1. 커스텀 밸리데이션 규칙 생성
2. Customer 클래스에 밸리데이션 규칙 추가
3. HTML 폼에 에러 메시지 보이기
4. 확인 페이지 갱신

커스텀 밸리데이션 규칙 생성
1. @CourseCode 애노테이션 생성

```java
@Constraint(validatedBy = CourseCodeConstraintValidator.class)
@Target( { ElementType.METHOD ElementType.FIELD } )
@Retention(RetentionPolicy.RUNTIME)
public @interface CourseCode {

  // define default course code
  public String value() default "LUV";

  // define default error message
  public String message() default "must start with LUV";

  // define default groups
	public Class<?>[] groups() default {};
	
	// define default payloads
	public Class<? extends Payload>[] payload() default {};
}
```
* @interface는 자바에 새로 생긴 특별한 타입의 인터페이스다
  - 이것을 사용해서 애노테이션을 만들 수 있다
* @Constraint는 비즈니스 로직을 담은 헬퍼 클래스를 가리킨다
* @Target은 작성한 애노테이션을 어디에 적용할 것인지 지정한다(메서드 혹은 필드 혹은 둘 다)
* @Retention은 이 어노테이션을 얼마나 오래 유지할 것인지를 지정한다
  - RetentionPolicy.RUNTIME은 런타임동안 지속한다는 의미다
* public String value() default "LUV";
  - LUV를 value의 기본 값으로 지정한다
* public String message() default "must start with LUV";
  - must start with LUV message의 기본 값으로 지정한다

2. CourseCodeConstraintValidator 작성
  - 비즈니스 로직을 위한 밸리데이션을 담은 헬퍼 클래스

```java
package com.luv2code.springdemo.mvc.validation;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

public class CourseCodeConstraintValidator
    implements ConstraintValidator<CourseCode, String> {

	private String coursePrefix;
	
	@Override
	public void initialize(CourseCode theCourseCode) {
		coursePrefix = theCourseCode.value();
	}
	
	@Override
	public boolean isValid(String theCode, 
			           ConstraintValidatorContext theConstraintValidatorContext) {
		boolean result;
		
		if (theCode != null) {
		    result = theCode.startsWith(coursePrefix);
		} else {
			return true;
		}
		return result;
	}

}
```

---

Customer 클래스에 밸리데이션 규칙 추가

```java
// ... Customer.java
@CourseCode
private String courseCode;

public String getCourseCode() {
	return courseCode;
}
public void setCourseCode(String courseCode) {
	this.courseCode = courseCode;
}
// ...
```

---

HTML 폼에 에러 메시지 보이기

```jsp
<!-- customer-form.jsp 하단에 추가 -->
Course code: <form:input path="courseCode" />
<form:errors path="courseCode" cssClass="error" />

<br />
```

---

확인 페이지 갱신

```jsp
<!-- customer-confirmation.jsp 하단에 추가 -->
Course Code: ${customer.courseCode}
```

---

### lecture 168. FAQ: Spring MVC Custom Validation - Possible to validate with multiple strings?

하나의 애노테이션에 다중 밸리데이션 통합하기
* CourseCode.java에서 value가 문자열 타입의 배열을 사용하도록 갱신
```java
// define default course code
public String[] value() default {"LUV"};
```

* CourseCodeConstraintValidator.java가 문자열 타입의 배열을 검증하도록 갱신
```java
@Override
public boolean isValid(String theCode, 
                    ConstraintValidatorContext theConstraintValidatorContext) {
    boolean result = false;
        
    if (theCode != null) {
        
        //
        // loop thru course prefixes
        //
        // check to see if code matches any of the course prefixes
        //
        for (String tempPrefix : coursePrefixes) {
            result = theCode.startsWith(tempPrefix);
            
            // if we found a match then break out of the loop
            if (result) {
                break;
            }
        }
    }
    else {
        result = true;
    }
    
    return result;
}
```

* Customer.java에서 CourseCode 밸리데이션을 사용하는 필드를 문자열 타입의 배열로 갱신
```java
@CourseCode(value={"TOPS", "LUV"}, message="must start with TOPS or LUV")
private String courseCode;
```

---