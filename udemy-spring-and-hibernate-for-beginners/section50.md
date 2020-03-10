# Section 50. Spring Security - Adding Logout Support

로그아웃 기능을 추가하기

개발과정

1. 스프링 시큐리티 설정에 로그아웃 지원을 추가하기
2. JSP 코드에 로그아웃 버튼 추가하기
3. 로그인 폼이 로그아웃 메시지를 보여주도록 수정하기

스프링 시큐리티 설정에 로그아웃 지원을 추가하기

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
	    http.authorizeRequests()
	            .anyRequest().authenticated()
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

- 앞선 섹션에서 작성했던 configure 메서드 코드에 and로 연결해 logout기능을 추가했다
- permitAll로 누구나 접근가능하도록 지정했다

JSP 코드에 로그아웃 버튼 추가하기

home.jsp

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<html>
<head>
<meta charset="UTF-8">
<title>luv2code Company Home Page</title>
</head>
<body>
	<h1>Home Page</h1>

	<form:form action="${pageContext.request.contextPath}/logout"
		method="POST">
		<input type="submit" value="Logout" />
	</form:form>
</body>
</html>
```

- 로그인과 마찬가지로 contextPath를 이용해 로그인 작업을 수행할 경로에 폼을 POST메소드로 제출한다
- 로그아웃이 진행되면 스프링 시큐리티는 유저의 http 세션과 세션쿠키 등을 무효화 시킨다
- 그 다음 유저를 로그인 페이지로 이동시킨다
- 이 때 이동된 로그인 페이지 uri에는 ?logout 쿼리스트링이 포함되어 있다
- 이 쿼리스트링을 로그아웃 메시지를 보여주는 데 사용할 수 있다

로그인 폼이 로그아웃 메시지를 보여주도록 수정하기

- logout 파라미터(쿼리스트링)이 있는지 체크한다
- logout 파라미터가 있으면 로그아웃 메시지를 보여준다

fancy-login.jsp

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>

<html lang="en">

<head>

<title>Login Page</title>
<meta charset="utf-8">
<meta name="viewport"
	content="width=device-width, initial-scale=1, shrink-to-fit=no">

<!-- Reference Bootstrap files -->
<link rel="stylesheet"
	href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">

<script
	src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.0/jquery.min.js"></script>

<script
	src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>

</head>

<body>

	<div>

		<div id="loginbox" style="margin-top: 50px;"
			class="mainbox col-md-3 col-md-offset-2 col-sm-6 col-sm-offset-2">

			<div class="panel panel-info">

				<div class="panel-heading">
					<div class="panel-title">Sign In</div>
				</div>

				<div style="padding-top: 30px" class="panel-body">

					<!-- Login Form -->
					<form:form
						action="${pageContext.request.contextPath}/authenticateTheUser"
						method="POST" class="form-horizontal">

						<!-- Place for messages: error, alert etc ... -->
						<div class="form-group">
							<div class="col-xs-15">
								<div>

									<c:if test="${param.error != null}">
										<div class="alert alert-danger col-xs-offset-1 col-xs-10">
											Invalid username and password.
										</div>
									</c:if>

									<c:if test="${param.logout != null}">

										<div class="alert alert-success col-xs-offset-1 col-xs-10">
											You have been logged out.
										</div>

									</c:if>

								</div>
							</div>
						</div>

						<!-- User name -->
						<div style="margin-bottom: 25px" class="input-group">
							<span class="input-group-addon"><i
								class="glyphicon glyphicon-user"></i></span> <input type="text"
								name="username" placeholder="username" class="form-control">
						</div>

						<!-- Password -->
						<div style="margin-bottom: 25px" class="input-group">
							<span class="input-group-addon"><i
								class="glyphicon glyphicon-lock"></i></span> <input type="password"
								name="password" placeholder="password" class="form-control">
						</div>

						<!-- Login/Submit Button -->
						<div style="margin-top: 10px" class="form-group">
							<div class="col-sm-6 controls">
								<button type="submit" class="btn btn-success">Login</button>
							</div>
						</div>

					</form:form>

				</div>

			</div>

		</div>

	</div>

</body>
</html>
```
