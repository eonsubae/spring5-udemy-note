# Section 19. Setting Up Hibernate Development Environment

### lecture 172. Hibernate Development Environment Overview

하이버네이트를 이용한 어플리케이션을 위해 필요한 것들
* 자바 IDE
* DB 서버
* 하이버네이트 JAR 파일과 JDBC 서버

Mysql 설치과정은 다시 필요할 때 영상을 보는 것이 낫다(lecture 173)

---

### lecture 175 Setting Up Database Table

압축파일을 강의사이트에서 받는다
* 압축을 풀어보면 sql-scripts폴더에 두 개의 스크립트가 있다
  - 01-create-user
  - 02-student-tracker

01-create-user
* 애플리케이션을 위해 새로운 MySQL 유저를 생성해준다
  - user id : hbstudent
  - password : hbstudent
* MySQL 워크벤치에서 파일을 불러온다
```sql
CREATE USER 'hbstudent'@'localhost' IDENTIFIED BY 'hbstudent';

GRANT ALL PRIVILEGES ON * . * TO 'hbstudent'@'localhost';

ALTER USER 'hbstudent'@'localhost' IDENTIFIED WITH mysql_native_password BY 'hbstudent';
```
* 위와 같은 쿼리문이 작성되어 있는 것을 확인할 수 있다
* hbstudent로 id와 비밀번호를 생성하고 모든 권한을 위임하는 코드다
* 워크벤치에서 번개모양을 클릭하면 적용된다

생성한 hbstudent를 적용하기
* 워크벤치에서 기존 연결을 종료한다
* 새로운 커넥션 만들기를 누르고 다음과 같이 입력한다
  - Connection name : hbstudent
  - Username : hbstudent
* 그 다음 Test Connection을 눌러본다
  - 비밀번호를 입력하면 테스트가 성공했다는 창이 뜬다

02-student-tracker
* student 테이블을 만드는 쿼리문이 담겨있다
  - id INT(11)
  - first_name VARCHAR(45)
  - last_name VARCHAR(45)
  - email VARCHAR(45)
* 이 파일을 워크벤치에서 열어보면 다음과 같은 쿼리문이 담겨 있다
```sql
CREATE DATABASE  IF NOT EXISTS `hb_student_tracker`;
USE `hb_student_tracker`;

--
-- Table structure for table `student`
--

DROP TABLE IF EXISTS `student`;

CREATE TABLE `student` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(45) DEFAULT NULL,
  `last_name` varchar(45) DEFAULT NULL,
  `email` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=latin1;
```
* 번개무늬를 눌러서 적용시키자
* 스키마에서 우클릭을 하고 새로고침을 하면 hb_student_tracker 데이터베이스가 생성된 것을 확인할 수 있다

---

### lecture 176. Setting Up Hibernate in Eclipse

이클립스에 하이버네이트 설정하기
1. 이클립스 프로젝트 생성
2. 하이버네이트 파일 다운로드
3. MySQL JDBC Driver 다운로드
4. JAR 파일들을 이클립스 프로젝트의 build path에 추가하기

이클립스 프로젝트 생성
* Perspective를 Java로 변경하고 새 자바 프로젝트를 생성하자
  - hibernate-tutorial으로 이름을 지정
* 생성된 프로젝트에 lib라는 폴더를 새로 생성

하이버네이트 파일 다운로드
* hibernate.org 사이트에 접속해서 hibernate ORM의 more클릭
  - 최신 버전을 다운받는다
* 다운이 완료되면 압축을 풀고 lib/required의 모든 jar파일을 복사해서 앞서 만든 새 프로젝트의 lib폴더에 복사한다

MySQL JDBC Driver 다운로드
* dev.mysql.com 사이트에 접속해서 DOWNLOADS -> Community -> 스크롤을 내려서 MySQL Connectors 클릭
* 위 링크에서 Connector/J를 다운받는다
  - 다운받은 파일을 lib폴더에 추가한다

JAR file들을 build path에 추가하기
* 프로젝트 우클릭 > Properties > Java Build Path > Libraries > Classpath를 누르고 Add JARs 클릭해서 lib에 있는 파일들 전부 적용하기

---

### lecture 177. Testing Your JDBC Connection

JDBC 연결을 확인하기 위해 매우 간단한 프로그램을 작성해보자
* src 폴더에 com.luv2code.jdbc 패키지 생성
  - 패키지 내에 TestJdbc 클래스를 생성한다(main 메서드 포함)

