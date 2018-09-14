# Section 18. Introduction to Hibernate

### lecture 169. Hibernate Overview

하이버네이트란?
* 데이터베이스와 자바 객체를 연결해주는 프레임워크
* 데이터베이스에 저장, 검색 등이 가능하다

하이버네이트의 장점
* 로우레벨 sql을 다룰 수 있다
* JDBC로 개발할 때 작성해야 하는 코드를 최소화해준다
* ORM을 제공해준다
  - 앱을 만들 때 매우 쉽게 저장, 검색 등의 작업을 처리할 수 있다

ORM
* 자바 클래스와 데이터베이스 테이블을 매핑해준다

코드 예제
```java
// create Java object
Student theStudent = new Student("John", "Doe", "john@luv2code.com");

// save it to database
int theId = (Integer) session.save(theStudent);

// now retrieve from database using the primary key
Student myStudent = session.get(Student.class, theId); 
```
* session은 하이버네이트의 객체 중 하나다
  - save 메서드를 호출하면 파라미터로 넘어간 자바 객체를 데이터베이스에 저장해준다
  - 저장하고 나면 pk를 반환한다
  - get 메서드로 특정 자바 객체와, pk값을 넘겨 원하는 row를 데이터베이스에서 검색할 수 있다

```java
Query query = session.createQuery("from Student");

List<Student> students = query.list();
```
* Student 객체의 목록을 데이터베이스로 부터 가져오고 있다
* 이렇게 하이버네이트는 쿼리 언어를 사용할 수 있다

---

### lecture 170. Hibernate and JDBC

하이버네이트와 JDBC의 관계
* 하이버네이트는 모든 데이터베이스와의 통신에 JDBC를 사용한다
* 하이버네이트는 JDBC의 또 다른 계층이라고 할 수 있다

---