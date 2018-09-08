# Section 13. Spring MVC - Request Params and Request Mappings

### lecture 118. Binding Request Params - Overview

코드 예제
* 폼으로 데이터를 처리하는 새로운 메서드 작성
* 학생 이름을 입력받아 읽어온다
* 이름을 대문자로 변환한다
* 모델에 변환한 이름을 추가한다

getParameter 대체하기
```java
// ... 원본
@RequestMapping("/processFormVersionTwo")
public String letsShoutDude(HttpServletRequest request, Model model) {
		
		String theName = request.getParameter("studentName");
    // ...
}
// ...

// ... 대체본
@RequestMapping("/processFormVersionTwo")
public String letsShoutDude(@RequestParam("studentName") String theName, Model model) {
    // ...
}
// ...
```
* @RequestParam의 인자로 폼의 name 속성의 값을 입력하면 자동적으로 바인딩해준다

---

### lecture 120. Controller Level Request Mapping - Overview

리퀘스트 매핑 계층 작성하기
```java
@Controller
@RequestMapping("/funny")
public class FunnyController {
		
	@RequestMapping("/showForm")
	public String showForm() {
		// ...
	}
		
	@RequestMapping("/processForm")
	public String process(HttpServletRequest request, Model model) {
		// ... 
	}
	
}
```
* 위와 같은 코드라면 showForm은 /funny/showForm과 매칭된다
* process는 /funny/processForm과 매칭된다

---