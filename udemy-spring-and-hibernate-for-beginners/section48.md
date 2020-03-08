# Section 48. Spring Security - Adding Custom Login Form

스프링이 제공해주는 기본 Form이 아닌 직접 HTML, CSS로 폼을 만들어보자

개발과정

1. 커스텀 로그인 폼을 참조하도록 변경하기 위해 스프링 시큐리티 설정을 수정하기
2. 커스텀 로그인 폼을 보여주도록 컨트롤러 코드 작성하기
3. 커스텀 로그인 폼 작성하기

커스텀 로그인 폼을 참조하도록 변경하기 위해 스프링 시큐리티 설정을 수정하기

DemoSecurityConfig.java

```java
@Configuration
@EnableWebSecurity
public class DemoSecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		UserBuilder users = User.withDefaultPasswordEncoder();

		auth.inMemoryAuthentication()
		    .withUser(users.username("john").password("test123").roles("EMPLOYEE"))
	        .withUser(users.username("mary").password("test123").roles("MANAGER"))
	        .withUser(users.username("susan").password("test123").roles("ADMIN"));
	}

  @Override
	protected void configure(HttpSecurity http) throws Exception {
	    http.authorizeRequests() // Restrict access based on the HttpServletRequest
	            .anyRequest().authenticated() // Any requests to the app must be authenticated(ie logged in)
	        .and()
	        .formLogin() // Customizing the form login process
	            .loginPage("/showMyLoginPage") // Show our custom form at the request mapping
	            .loginProcessingUrl("/authenticateTheUser") // Login form should POST data to this URL for processing (check user id and password)
	            .permitAll(); // Allow everyone to see login page. No need to be logged in
	}

}
```

- 이전 섹션에서 작성했던 configure 메서드는 AuthenticationManagerBuilder 객체를 인자로 사용한다
- 이 객체는 인메모리용 인증에 자주 사용된다
- 오버라이딩한 다른 configure 메서드는 웹용 인증에 사용할 수 있는 HttpSecurity도 인자로 받을 수 있다
- HttpSecurity 객체의 authorizaeRequest 메서드는 HttpServletRequest 요청에 의한 접근을 제한한다
- 메서드 체이닝으로 anyRequest 메서드와 authenticated 메서드를 적용하면 어떤 경로의 요청이든 인증을 필요로 하게 된다
- 브라우저 로그인 폼을 커스터마이징 하려면 formLogin과 이하의 메서드들을 호출하면 된다
- loginPage의 인자로는 커스터마이징한 로그인 폼을 넘겨주는 컨트롤러의 경로를 문자열로 넘겨줘야 한다
- loginProcessingUrl의 인자로는 커스텀 폼에서 넘어온 id, password를 실제로 처리하는 컨트롤러의 경로를 문자열로 넘겨줘야 한다
- permitAll은 인증되지 않은 유저도 해당 페이지(여기서는 로그인 폼)를 볼 수 있도록 해준다

---

커스텀 로그인 폼을 보여주도록 컨트롤러 코드 작성하기

LoginController.java

```java
@Controller
public class LoginController {

	@GetMapping("/showMyLoginPage")
	public String showMyLoginPage() {
		return "plain-login";
	}
}
```

- GetMapping어노테이션의 인자인 문자열이 위 설정 폼에서 loginPage 메서드의 인자로 지정했던 문자열과 일치하는 것을 확인할 수 있다

---

커스텀 로그인 폼 작성하기

- 앞서 /authenticateTheUser 경로에서 id와 password를 체크한다고 설명한 바 있다
- 이 경로에서 실행되는 체크는 스프링 시큐리티 필터에 의해 다뤄진다
- 여기에는 어떤 코드도 추가할 필요가 없다

```jsp
<form:form action="${pageContext.request.contextPath}/authenticateTheUser" method="POST">
  <!-- ... -->
</form:form>
```

- contextPath는 contextRoot와 같은 것이다(ex. www.xxx.com/spring-security에서 spring-security에 해당)
- 커스터마이징한 로그인 폼에는 위와 같이 경로를 설정해줘야 한다
- 그리고 HTTP 메소드는 반드시 POST로 지정해야 한다

```html
User name: <input type="text" name="username" />

Password: <input type="password" name="password" />
```

- 스프링 시큐리티는 필터는 input 태그의 name이 username, password 인것을 자동적으로 id, password로 인식한다

완성된 plain-login.jsp 코드

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>

<form:form action="${pageContext.request.contextPath}/authenticateTheUser" method="POST">

	<p>
	  User name: <input type="text" name="username" />
  </p>

  <p>
    Password: <input type="password" name="password" />
	</p>

	<input type="submit" value="Login" />

</form:form>
```

에러 핸들링하기

- 만약 로그인 폼에 잘못된 인증정보를 입력해서 요청했다면 어떻게 될까
- 스프링이 기본으로 제공해주던 폼과 달리 커스터마이징한 폼에서는 어떤 에러 내용도 보여주지 않는다
- 대신 ?error라는 쿼리스트링을 파라미터로 활용해서 직접 에러를 처리할 수 있다
- 커스텀 로그인 폼을 error파라미터가 있는지 확인해서 에러메시지를 처리하도록 수정해야 한다

plain-log.jsp

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>

<html>

<head>
<meta charset="UTF-8">
<title>Custom Login Page</title>
<style>
  .failed {
    color: red;
  }
</style>
</head>

<body>
	<h3>My Custom Login Page</h3>

	<form:form
		action="${pageContext.request.contextPath}/authenticateTheUser"
		method="POST">

		<c:if test="${param.error != null}">

			<i class="failed">Sorry! You entered invalid username/password.</i>

		</c:if>

		<p>
			User name: <input type="text" name="username" />
		</p>

		<p>
			Password: <input type="password" name="password" />
		</p>


		<input type="submit" value="Login" />

	</form:form>
</body>

</html>
```
