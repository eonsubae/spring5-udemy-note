# Section 52. Spring Security - User Roles

User id와 함께 권한(role)을 보여주기

개발과정

1. Spring Security JSP tag library를 위해 POM파일 수정하기
2. Spring Security JSP tag library를 JSP 페이지에 추가하기
3. User ID 보여주기
4. User 권한(Role) 보여주기

Spring Security JSP tag library를 위해 POM파일 수정하기
pom.xml

```xml
<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-taglibs</artifactId>
  <version>${springsecurity.version}</version>
</dependency>
```

Spring Security JSP tag library를 JSP 페이지에 추가하기

home.jsp

```jsp
<%@ taglib prefix="security"
           uri="http://www.springframework.org/security/tags" %>
(...)
```

User ID 보여주기

```jsp
<%@ taglib prefix="security"
           uri="http://www.springframework.org/security/tags" %>
(...)

User: <security:authentication property="principal.username" />

```

User 권한(Role) 보여주기

```jsp
<%@ taglib prefix="security"
           uri="http://www.springframework.org/security/tags" %>
(...)

User: <security:authentication property="principal.username" />

Role(s): <security:authentication property="principal.authorities" />

```
