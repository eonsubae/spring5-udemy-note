# Section 74. Spring Boot - Build a REST CRUD API with Hibernate - Real-Time Project

### lecture 519. REST CRUD Real-Time Project Overview and Database Set Up

API 요구사항
* Employee 관리를 위한 REST API를 생성해야 한다
* REST clients는 다음 동작들을 할 수 있어야 한다
  - Get a list of employees(GET /api/employees)
  - Get a single employee by id(GET /api/employees/{employeeId})
  - Add a new employee(POST /api/employees)
  - Update an employee(PUT /api/employees)
  - Delete an employee(DELETE /api/employees/{employeeId})

개발 실습 과정
1. 데이터베이스 개발 환경 설정
2. 스프링 이니셜라이저를 이용해서 스프링 부트 프로젝트 생성
3. Get a list of employees
4. Get a single employee by id
5. Add a new employee
6. Update an employee
7. Delete an employee

어플리케이션 아키텍쳐
* Employee REST Controller <---> Employee Service <---> Employee DAO(hibernate) <---> databse

employee.sql
* employee 테이블을 employee.sql 파일에 생성한다
  - id INT(11)
  - first_name VARCHAR(45)
  - last_name VARCHAR(45)
  - email VARCHAR(45)
* sql script는 다음 링크에서 받는다
  - [sql script](www.luv2code.com/spring-boot-employee-sql-script)
```sql
CREATE DATABASE  IF NOT EXISTS `employee_directory`;
USE `employee_directory`;

--
-- Table structure for table `employee`
--

DROP TABLE IF EXISTS `employee`;

CREATE TABLE `employee` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(45) DEFAULT NULL,
  `last_name` varchar(45) DEFAULT NULL,
  `email` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=latin1;

--
-- Data for table `employee`
--

INSERT INTO `employee` VALUES 
	(1,'Leslie','Andrews','leslie@luv2code.com'),
	(2,'Emma','Baumgarten','emma@luv2code.com'),
	(3,'Avani','Gupta','avani@luv2code.com'),
	(4,'Yuri','Petrov','yuri@luv2code.com'),
	(5,'Juan','Vega','juan@luv2code.com');
```

---

### lecture 520. Create Project with Spring Initializr

web, jpa, mysql, devtools를 선택해 패키지를 만들고 import 한다

---

### lecture 521. Integrating Hibernate and JPA

