# Section 21. Hibernate CRUD Features: Create, Read, Update and Delete

### lecture 185. Creating and Saving Java Objects - Part 1

하이버네이트를 이용해 자바 객체를 저장하는 방법을 알아보자
1. 하이버네이트 설정 파일 추가
2. 자바 클래스에 애노테이션 적용
3. 데이터베이스 사용을 위한 자바코드 작성
* 앞서 Section 20에서 1과 2를 학습한 바 있다. 이 섹션에서는 3에 대해서 알아본다

Two Key Players
* SessionFactory 클래스
  - 하이버네이트 설정 파일을 읽고 세션 객체들을 생성한다
  - 무거운 객체다
  - 앱에서 단 한번만 생성한다
* Session 클래스
  - JDBC 연결을 래핑한다
  - 객체들을 검색하고 저장하는데 사용한다
  - 짧은 기간에 사용하고 없어지는 객체다
  - SessionFactory에서 검색할 수 있다

Java Code Setup
```java
public static void main(String[] args) {
  
  SessionFactory factory = new Configuration()
                          .configure("hibernate.cfg.xml")
                          .addAnnotatedClass(Student.class)
                          .buildSessionFactory();

  Session session = factory.getCurrentSession();

  try {

    // now use the session objects to save/retrieve Java objects
  } finally {
    factory.close();
  }
}
```
* 앞서 설명한대로 SessionFactory 객체는 한 번만 생성한다
* configure 메서드에 설정파일명을 지정하지 않으면 hibernate.cfg.xml을 기본으로 지정한다

Save a Java Object
```java
try {

    // create a student object
    Student tempStudent = new Student("Paul", "Wall", "paul@luv2code.com");

    // start transaction
    session.beginTransaction();

    // save the student
    session.save(tempStudent);

    // commit the transaction
    session.getTransaction().commit();
    
} finally {
    factory.close();
}
```