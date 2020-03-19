# Section 72. Spring Boot - Running Spring Boot Apps from the Command Line

### lecture 515. Running Spring Boot Apps from the Command Line - Overview

IDE를 사용하지 않고 스프링 부트 앱을 실행하기
* 스프링 부트로 만든 앱은 내장 서버가 있으므로 서버 설정 역시 필요없다
* CLI를 이용한 실행
  - 두 가지 옵션을 설정하는 것이 가능하다
    1. java -jar 사용하기
    2. 스프링 부트 메이븐 플러그인 사용하기
       - mvnw spring-boot:run

java -jar 사용하기
* java -jar 앱이름(jar파일 이름).jar

스프링 부트 메이븐 플러그인 사용하기
* mvnw는 메이븐 프로젝트를 실행해주는 명령어다
  - 메이븐을 추가로 직접 설치할 필요는 없다
  - 적절한 버전의 메이븐이 없으면 자동으로 설치해준다
* pom.xml에 스프링 이니셜라이저가 생성해준 플러그인이 있다
  ```xml
  (...)
  <build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
  (...)
  ```

개발 실습 과정
1. IDE를 종료하기
2. mvnw package를 이용해서 앱을 패키징하기
3. java -jar로 앱을 실행하기