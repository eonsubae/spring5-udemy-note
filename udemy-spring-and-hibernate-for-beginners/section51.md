# Section 51. Spring Security - Cross Site Request Forgery(CSRF)

Cross Site Request Forgery(CSRF)란

- 웹서버에 있는 자원에 접근할 때 최초접근자가 누구인지는 따지지 않는다
- 이런 점을 이용해 허위 이벤트 당첨 폼 등 다른 사이트를 경유하는 공격이 가능하다
- ex. 허위 이벤트 당첨 사이트 폼(www.aaa.com/form)의 action에 공격대상 사이트의 경로(ex. www.target.com/purchase?itemId=37128379123)를 집어넣는다
- 유저가 속아서 폼을 전송하게 되면 공격대상 사이트에 유저가 인증되어 있을시 권한탈취가 가능해진다
  - ex. 뱅킹 앱이라면 다른 사람에게 돈을 전송하게 될 수도 있다
  - ex. 이커머스 앱이라면 원하지 않는 상품을 구매하게 될 수도 있다
- see surf라고도 부른다

CSRF 방어하기

- 서버에서 생성한 토큰을 HTML form에 집어넣는다
- 후속 요청이 오면 웹앱은 로직 처리 이전에 form으로부터 전송되어진 토큰과 서버에서 생성한 토큰을 비교한다

스프링 시큐리티의 CSRF처리

- 스프링 시큐리티는 Synchronizer Token Pattern을 사용한다
  - 각각의 요청은 세션 쿠키와 랜덤으로 생성된 토큰을 포함한다
- 스프링 시큐리티는 요청이 오면 요청의 처리 이전에 토큰을 검증한다
- CSRF token은 HTTP method가 GET이 아닌 POST일 때 사용된다
- <form:form action="..." method="POST">태그가 있으면 스프링 시큐리티는 자동으로 토큰을 추가해준다
  - 만약 이 태그가 없다면 직접 csrf token을 추가해야 한다

직접 csrf token추가하는 방법

```jsp
<form action="..." method="POST">
  <input type="hidden"
         name="${_csrf.parameterName}"
         value="${_csrf.token}"/>
</form>
```

- 이 모든 과정은 스프링 시큐리티 필터에 의해 처리된다
