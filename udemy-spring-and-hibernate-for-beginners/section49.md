# Section49. Spring Security - Bootstrap CSS Login Form

앞서 만든 jsp 코드를 부트스트랩을 이용해 꾸미기

- 나는 부트스트랩 없이도 css를 작성할 수 있으므로 자세히 정리하지 않는다
- 그리고 부트스트랩을 사용한 사이트는 전부 비슷해 보이는 효과가 있어 권장하지 않는다
- 정 필요하다면 다시 강의를 본다

bootstrap을 사용한 jsp코드

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
					<form:form action="${pageContext.request.contextPath}/authenticateTheUser"
					      method="POST"
					      class="form-horizontal">

						<!-- Place for messages: error, alert etc ... -->
						<div class="form-group">
							<div class="col-xs-15">
								<div>

									<c:if test="${param.error != null}">
										<div class="alert alert-danger col-xs-offset-1 col-xs-10">
											Invalid username and password.</div>
									</c:if>

									<!--
									<div class="alert alert-success col-xs-offset-1 col-xs-10">
										You have been logged out.
									</div>
								    -->

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

부트스트랩으로 작성한 jsp파일을 로그인 폼으로 서빙하도록 컨트롤러 수정하기

LoginController.java

```java
@Controller
public class LoginController {

	@GetMapping("showMyLoginPage")
	public String showMyLoginPage() {
		return "fancy-login";
	}
}

```
