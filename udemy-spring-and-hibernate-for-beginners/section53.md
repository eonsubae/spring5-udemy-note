# Section 53. Spring Security - Restrict Access Based on Role

권한별로 접근가능한 페이지를 나눠 적용해보기

- / 경로는 EMPLOYEE 권한만 있으면 접근할 수 있다
- /leaders 경로는 MANAGER 권한이 있어야 접근할 수 있다
- /systems 경로는 ADMIN 권한이 있어야 접근할 수 있다

개발과정

1. 위의 path마다 다른 페이지를 보내주도록 컨트롤러 코드와 뷰 페이지 작성
2. 유저 권한 수정
3. 권한에 기초해 접근 제한하기

위의 path마다 다른 페이지를 보내주도록 컨트롤러 코드와 뷰 페이지 작성

DemoController.java

```java
@Controller
public class DemoController {

	@GetMapping("/")
	public String showHome() {
		return "home";
	}

	@GetMapping("/leaders")
	public String showLeaders() {
		return "leaders";
	}

	@GetMapping("/systems")
	public String showSystems() {
		return "systems";
	}
}
```

home.jsp

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<%@ taglib prefix="security"
	uri="http://www.springframework.org/security/tags"%>
<html>
<head>
<meta charset="UTF-8">
<title>luv2code Company Home Page</title>
</head>
<body>
	<h1>Home Page</h1>

	<hr>

	<p>
	  User: <security:authentication property="principal.username" />
	  <br><br>
	  Role(s): <security:authentication property="principal.authorities" />
    </p>

    <hr>

    <p>
      <a href="${pageContext.request.contextPath}/leaders">Leadership Meeting</a>
      (Only for Manager peeps)
    </p>

    <hr>

	<form:form action="${pageContext.request.contextPath}/logout"
		method="POST">
		<input type="submit" value="Logout" />
	</form:form>

</body>
</html>
```

leaders.jsp

```jsp
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>luv2code LEADERS Home Page</title>
</head>
<body>
  <h2>luv2code LEADERS Home Page</h2>
  <hr>
  <p>
    See You in Brazil ... for our annual Leadership retreat!
    <br>
    Keep this trip a secret, don't tell the regular employees LOL :-)
  </p>
  <hr>
  <a href="${pageContext.request.contextPath}/">Back to Home Page</a>
</body>
</html>
```

systems.jsp

```jsp
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>luv2code SYSTEMS Home Page</title>
</head>
<body>
  <h2>luv2code SYSTEMS Home Page</h2>
  <hr>
  <p>
    We have our annual holiday Caribean cruise coming up. Register now!
    <br>
    Keep this trip a secret, don't tell the regular employees LOL :-)
  </p>
  <hr>
  <a href="${pageContext.request.contextPath}/">Back to Home Page</a>
</body>
</html>
```

유저 권한 수정

- 모든 유저는 EMPLOYEE권한을 가진다
- 특정 유저는 MANAGE 또는 ADMIN 권한을 가진다

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
				    .withUser(users.username("mary").password("test123").roles("EMPLOYEE", "MANAGER"))
				    .withUser(users.username("susan").password("test123").roles("EMPLOYEE", "ADMIN"));
	}


```

권한에 기초해 접근 제한하기

DemoSecurityConfig.java

- MANAGER권한을 가진 유저는 /leaders 경로에 접근할 수 있다
- ADMIN권한을 가진 유저는 /systems 경로에 접근할 수 있다

```java
@Configuration
@EnableWebSecurity
public class DemoSecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		UserBuilder users = User.withDefaultPasswordEncoder();

		auth.inMemoryAuthentication()
		        .withUser(users.username("john").password("test123").roles("EMPLOYEE"))
				.withUser(users.username("mary").password("test123").roles("EMPLOYEE", "MANAGER"))
				.withUser(users.username("susan").password("test123").roles("EMPLOYEE", "ADMIN"));
	}

	@Override
	protected void configure(HttpSecurity http) throws Exception {
	    http.authorizeRequests()
	            .antMatchers("/").hasRole("EMPLOYEE")
	            .antMatchers("/leaders/**").hasRole("MANAGER")
	            .antMatchers("/systems/**").hasRole("ADMIN")
	        .and()
	        .formLogin()
	            .loginPage("/showMyLoginPage")
	            .loginProcessingUrl("/authenticateTheUser")
	            .permitAll()
	        .and()
	        .logout().permitAll();
	}
}
```

---

커스텀 접근 제한 페이지 만들기

- 앞서 작성한 코드 덕분에 권한이 없는 유저가 특정 자원에 접근하는 것을 막을 수 있다
- 그런데 권한이 없음을 알리는 스프링 기본 에러페이지(403 에러 페이지)는 지나치게 투박하게 생겼다
- 이 페이지를 커스터마이징하는 방법을 알아보자

개발과정

1. 접근 제한을 위한 커스텀 페이지 설정하기
2. 컨트롤러 코드와 뷰 페이지 생성하기

접근 제한을 위한 커스텀 페이지 설정하기

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
  // (...)
  .exceptionHandling()
    .accessDeniedPage("/access-denied");
}
```

- exceptionHandling을 메서드 체이닝해서 스프링 시큐리티에서 발생한 에러 핸들링에 대한 설정을 시작한다
- accessDeniedPage에 인자를 넘겨 에러를 처리할 경로를 매핑한다

컨트롤러 코드와 뷰 페이지 생성하기

LoginController.java

```java
@Controller
public class LoginController {

	@GetMapping("/showMyLoginPage")
	public String showMyLoginPage() {
		return "fancy-login";
	}

	@GetMapping("/access-denied")
	public String showAccessDenied() {
		return "access-denied";
	}
}
```

access-denied.jsp

```jsp
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>luv2code - Access Denied</title>
</head>
<body>

  <h2>Access Denied - You are not authorized to access this resource.</h2>

  <hr>

  <a href="${pageContext.request.contextPath}/">Back to Home Page</a>
</body>
</html>
```

---

역할에 따라 보이는 컨텐츠 제한하기

- EMPLOYEE권한만 가진 john에게는 /leaders경로나 /systems경로에 대한 링크가 보일 필요가 없다
- MANAGER권한을 가진 mary에게는 /systems경로에 대한 링크가 보일 필요가 없다
- ADMIN권한을 가진 susan에게는 /leaders경로에 대한 링크가 보일 필요가 없다

스프링 시큐리티 JSP Tags

```jsp
<security:authorize access="hasRole('MANAGER')">

  <p>
    <a href="${pageContext.request.contextPath}/leaders">
      Leadership Meeting
    </a>
    (Only for Manager peeps)
  </p>

</security:authorize>
```

- 위처럼 스프링 시큐리티 태그를 이용하면 특정 권한을 가진 유저에게만 컨텐츠가 보이도록 제한할 수 있다

home.jsp

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<%@ taglib prefix="security"
	uri="http://www.springframework.org/security/tags"%>
<html>
<head>
<meta charset="UTF-8">
<title>luv2code Company Home Page</title>
</head>
<body>
	<h1>Home Page</h1>

	<hr>

	<p>
	  User: <security:authentication property="principal.username" />
	  <br><br>
	  Role(s): <security:authentication property="principal.authorities" />
    </p>

    <hr>

    <security:authorize access="hasRole('MANAGER')">

    <p>
      <a href="${pageContext.request.contextPath}/leaders">Leadership Meeting</a>
      (Only for Manager peeps)
    </p>

    </security:authorize>

    <security:authorize access="hasRole('ADMIN')">

    <p>
      <a href="${pageContext.request.contextPath}/systems">IT Systems Meeting</a>
      (Only for Admin peeps)
    </p>

    </security:authorize>

	<form:form action="${pageContext.request.contextPath}/logout"
		method="POST">
		<input type="submit" value="Logout" />
	</form:form>

</body>
</html>
```
