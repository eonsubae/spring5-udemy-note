# 서블릿

### 2.1 서블릿 시작하기

서블릿
* JVM 기반에서 웹 개발을 하기 위한 명세이자 API
* Java EE에 포함된 스펙 중의 하나로 자바에서 HTTP 요청과 응답을 처리하는 기능을 가지고 있다
* 자바 실행을 위해 JRE가 필요한 것처럼 서블릿을 실행하려면 웹 애플리케이션 컨테이너가 필요하다
  
### 2.1.1 서블릿 설정

그래들Gradle
* 메이븐, 엔트와 같이 자바에서 라이브러리를 편하게 추가할 수 있게 해주는 도구
* build.gradle 파일을 생성해서 사용한다
* JAR 파일은 build.gradle 파일에 추가하면 사용할 수 있다

### 2.1.1.1 그래들을 이용한 서블릿 설정

라이브러리 의존성 추가
* 이클립스, 인텔리제이를 사용하면 자동완성을 이용해 추가할 수 있다
* 이 장에서는 직접 사이트 검색을 통해 추가하는 방법을 알아본다

메이븐 중앙 저장소 접속
* <a href="https://mvnrepository.com">링크</a>
* 검색창에 servlet을 입력하면 상단에 Java Servlet API가 출력된다
* 특정 버전을 클릭하면 여러 탭이 있다
* Gradle 탭을 클릭해서 나온 내용을 build.gradle 파일에 추가하면 된다

### 2.2 서블릿 내부 동작

### 2.2.1 서블릿의 생명주기

생명주기
* 서블릿은 자체 생명주기를 가지며 웹 애플리케이션 컨테이너가 초기화되면 생명주기가 시작된다
* 초기화, 서비스, 소멸의 3단계로 구성

### 2.2.1.1 서블릿 초기화와 init 메서드

init 메서드
* 이름처럼 초기화를 담당하는 메서드
```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;

@WebServlet("/init")
public class InitServlet extends HttpServlet {
    @Override
    public void init() throws ServletException {
        System.out.println("init call");
    }
}
```
* HttpServlet은 추상 클래스다
  - 일반적으로 서블릿을 만들 때 이 클래스를 상속받는다
* init 메서드는 초기화에 대응해 한 번만 호출된다
* URL 매핑은 WebServlet 어노테이션을 사용한다

XML을 이용한 URL 매핑
```xml
<servlet>
    <servlet-name> Init </servlet-name>
    <servlet-class> InitServlet </servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name> Init </servlet-name>
    <url-pattern>/init</url-pattern>
</servlet-mapping>
```
* servlet 태그에는 설정 시에 별칭으로 서블릿 이름과 실제 servlet 클래스 명을 지정한다
* servlet-mapping 태그에는 서블릿 별칭과 해당 별칭에 매핑될 URL을 지정한다
* 위 xml 코드에서 name으로 별칭을 지정한 것처럼 어노테이션에서 동일한 기능을 작성하려면 name을 지정하면 된다

```java
import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebInitParam;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name="Init", urlPatterns={"/init"},
            initParams = { @WebInitParam(name = "siteName", value = "jpub") }
            )
public class InitServlet extends HttpServlet {
    private String myParam = "";

    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("init call");
        this.myParam = servletConfig.getInitParameter("siteName");
        System.out.println("입력받은 사이트 명은" + myParam + "입니다.");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
    throws ServletException, IOException {
        resp.getWrite().println("hello");
    }
}
```
* name에 별칭을 지정한다
* urlPatterns에는 다수의 URL을 지정해 매핑할 수 있다
  - 여러 개를 지정하려면 ,로 구분해 설정하면 된다
* init은 서블릿 초기화 시 한번만 동작되므로 새로고침을 해도 추가로 동작하지 않는다
  - 이런 특성을 이용해 초기화시 파라미터를 전달하려면 servletConfig를 사용한다
* servletConfig.getInitParameter 
  - web.xml 또는 WebInitParam 어노테이션의 정보를 서블릿 초기화시 전달할 수 있다

### 2.3 서블릿 활용

### 2.3.1 HTTP 요청과 응답

