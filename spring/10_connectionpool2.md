# ConnectionPool_2

> 앞서 얘기했던 커넥션 풀에 대한 추가 설명이다. 여기서는 Tomcat-JDBC를 사용한 예제를 살펴보자

 톰캣이 만들어 놓은 Connection pool 객체에 접근할 때는 JNDI를 사용한다.

- `JNDI(Java Naming and Directoory Interface)` : 필요한 자원을 키/값(key/value) 쌍으로 지정한 후 필요할 때 키를 이용해 값을 얻는 방법 

이것도 똑같이 Connection을 미리 만들어놔서 데이터베이스 연동 작업 발생 시  커넥션 풀에 있던 것을 준다.



----



### 1. 톰캣의 DataSource 설정 및 사용 방법

- JDBC 드라이버를 /WEB-INF/lib 폴더에 설치한다.
- ConnectionPool 기능 관련 jar 파일을 /WEB-INF/lib 폴더에 설치
- CATALINA_HOME/context.xml에 Connection 객체 생성 시 연결할 데이터베이스 정보를 JNDI로 설정
- DAO클래스에서 데이터베이스와 연동 시 미리 설정한 JNDI라는 이름으로 데이터베이스와 연결해서 작업



---



### 2. 이클립스에서 톰캣 DataSource 설정

```xml
<Resource
name="jdvc/oracle" //접근하는 암호
auth="Container"
...
```



---



### 3. JAVA 코드로 연결

> 서블릿에서 작성한다. 



---



### 4. JNDI

```java
			Context context = new InitialContext();

			//JNDI를 이용하기 위한 객체 생성

			DataSource source = (DataSource)context.lookup("java:comp/env/jdbc/oracle");

 			// lookup(): 등록된 naming 서비스로부터 자원을 찾고자할 때 사용하는 메서드

			//context 객체를 통해 이름으로 Resource를 획득한다.
			
  			//("jdbc/oracle"): JNDI 서비스에 접근하기 위한 기본 이름
			//(이 자원을 찾겠다.--> web.xml의 <res-ref-name>)

			//JNDI의 모든 이름은 기본적으로 java:comp/env에 등록되어 있다.

			//해당 영역에서 jdbc/oracle으로 설정된 이름을 획득한다.

			conn = source.getConnection();

			//source로 부터 Connection 객체를 획득한다. 

			//이 객체는 이제 Container의 DBCP에 의해 관리된다.

```



참조 : https://devbox.tistory.com/entry/JSP-%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80-1

