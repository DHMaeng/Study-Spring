# MVC(Model–view–controller) 기본 구조 생성 실습순서

### 1. 기본 스프링MVC생성/IN-OUT 확인

| 작업내용           | 실습내용      | 세부내용                                              |
| ------------------ | ------------- | ----------------------------------------------------- |
| 기본 스프링MVC생성 | 이클립스 이용 |                                                       |
| IN-OUT 확인        | IV 만들기     | View.jsp를 통해 호출시 나오는 처음 화면               |
|                    | 컨트롤러생성  | IV와 연결되어 out.jsp나 비즈니스 서비스와 연결 시킨다 |
|                    | OV 만들기     | 컨트롤러를 통해 원하는 서비스를 출력하는 화면         |

1. web browser에서 cotroller의 input.do를 호출한다.
2. servlet-context.xml의 설정으로 input.jsp로 연결되어 호출한다.
3. Form태그의 action기능으로 값과 함께 output.do가 호출된다.
4. controller의 output.do 부분으로 가게 되고 처리한 뒤 output.jsp를 호출한다.
5. output.jsp를 보여준다.



### 2. 비즈니스서비스연결

> 다알제? MODEL 부분

| 실습내용          | 세부내용                                                     |
| ----------------- | ------------------------------------------------------------ |
| VO 생성           | Value Object의 약자로 enum과 유사한 데이터의 집합이다.       |
| DAO생성           | Data Access Object 의 약자로 데이터베이스의 데이터에 접근하는 트랜잭션 객체다. |
| Service생성       | 비지니스 로직 처리하는 객체                                  |
| 컨트롤러 URL 연결 |                                                              |



### 3. Database 연결

> Mybatis가 커넥션, 쿼리 작성, 쿼리 결과 처리 작업 처리를 한다.

| 실습내용             | 세부내용                                                     |
| -------------------- | ------------------------------------------------------------ |
| 테이블 생성          | Oracle DB 연결                                               |
|                      | Sql 문으로 table 작성                                        |
| DB이용 설정 파일생성 | WEB-INF>lib>ojdbc6.jar 삽입                                  |
|                      | Web.xml 에 root-context.xml->action-mybatis.xml 로 변경      |
|                      | Spring>Action-mybatis.xml 추가                               |
|                      | Pom.xml에 mybatis.xml  태그 설정(dependency작성)             |
|                      | WEB-INF>config>jdbc>jdbc.properties 삽입                     |
|                      | Src/main/resources>Mybatis.mappers/mybatis.model 삽입        |
|                      | Mybatis.mappers>member.xml에 VO의 필드와 table 속성값 mapping |
|                      | mybatis.model>Modelconfig.xml에 경로 설정                    |
|                      | Action-mybatis와 DAO와 연결 -> sqlsession부분                |



#### 1. Action-mybatis와 DAO와 연결 -> sqlsession부분

- `DAO.java`

```java
@Autowired
	private SqlSession sqlSession;
```

- `action-mybatis.xml`

```xml
<bean id="sqlSession"
		class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg index="0" ref="sqlSessionFactory"></constructor-arg>
	</bean>
```





####  2. Pom.xml에 mybatis.xml  태그 설정(dependency작성)

- `Pom.xml`

```xml
<!-- mybatis -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.1.0</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.1.0</version>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.29</version>
		</dependency>
		<dependency>
			<groupId>jdbc.oracle</groupId>
			<artifactId>OracleDriver</artifactId>
			<version>12.1.0.2.0</version>
			<scope>system</scope>
			<systemPath>${basedir}/src/main/webapp/WEB-INF/lib/ojdbc6.jar</systemPath>
		</dependency>
```

