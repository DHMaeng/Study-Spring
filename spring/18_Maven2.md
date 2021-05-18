# 메이븐과 스프링 STS 사용법

### 중요!

spring legacy project(spring MVC project) 생성시 컨텍스트 이름이 프로젝트 이름이 아니고 Top level package의 이름이 된다.

<img src="../img/path.jpg" style="zoom:50%;" />

예를들어 다음과 같은 프로젝트에서 web browser 호출시

`http://localhost:9090/testspring/` 라고 호출해야 한다.



그리고 servers폴더의 server.xml에 들어가게 되면 path가 나온다 다른 이름을 원한다면 이것을 수정하면 된다.

```xml
<Context docBase="springtest" path="/testspring" reloadable="true" source="org.eclipse.jst.jee.server:springtest"/>
```



## 프로젝트 만들기!! 순서도 중요!

### 1. 기본 스프링MVC생성

만들면 다음과 같이 기본적으로 생성이 된다.

<img src="../img/maven2.jpg" style="zoom:50%;" />

- `web.xml`

> ContextLoaderListener를 이용해 애플리케이션이 실행될 때 action-mybatis.xml을 읽도록 설정

```xml
	<!-- Creates the Spring Container shared by all Servlets and Filters -->
   <listener> <!-- 환경설정정보 어떤 상황이 되면 자동으로 실행 -->
      <listener-class>
         org.springframework.web.context.ContextLoaderListener
     </listener-class>
   </listener>
	
	<!-- MyBatis Bean 관련 설정파일 등록 -->
	<context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>
          /WEB-INF/config/action-mybatis.xml
      </param-value>
   </context-param>  

	<!-- 한글 필터 기능. 한글 깨짐 현상 방지 -->
	<filter> <!-- 한글 인코딩 문제 -->
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<!-- CharacterEncodingFilter() -->
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern> <!-- 모든 경로 설정 -->
	</filter-mapping> 
	
	<servlet>
		<servlet-name>action</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>action</servlet-name>
		<url-pattern>*.do</url-pattern> <!-- 모든것.do 가 들어오면 위에 있는 클래스 호출-->
	</servlet-mapping>

</web-app>


```



- `servlet-context.xml`

>~.do 경로를 /WEB-INF/views/member/~.Jsp 로 변경

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:p="http://www.springframework.org/schema/p" xmlns:aop="http://www.springframework.org/schema/aop"
xmlns:context="http://www.springframework.org/schema/context"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans   
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
      <property name="prefix" value="/WEB-INF/views/member/" /> 
      <property name="suffix" value=".jsp"/>
   </bean>

```



### 1-2. Controller

<img src="../img/spring_annotation3.jpg" style="zoom:50%;" />

#### 1. `MemberControllerImpl.java`

> service/dao/vo를 사용할 경우 이렇게 연결해 줘야하고 서비스 부분을 사용할 시 역순으로 호출하기때문에코딩시 vo/dao/serivce/controller 순으로 작성하자. 

```java
@Autowired
private MemberService memberService;-> @Service("memberService") //memberService.java
@Autowired
private MemberVO memberVO ; -> @Component("memberVO") //memberVO.java 각각 이렇게 연결
```

1. `*Form.do`

<img src="../img/memberform.jpg" style="zoom:50%;" />

```java
	@RequestMapping(value = "/member/memberForm.do", method =  RequestMethod.GET) // 1 memberForm.jsp 연결
	public ModelAndView form(HttpServletRequest request, HttpServletResponse response) throws Exception {
		String viewName = getViewName(request); //request에 memberForm.do가 드감
		ModelAndView mav = new ModelAndView(); //ModerAndView 인스턴스 생성
		mav.setViewName(viewName); //뷰의 이름 설정 ModerAndView에 뷰의 경로를 담는다.
		return mav; //ModerAndView 객체를 리턴
}
```



뷰의 이름(경로)를 설정해주려면

setViewName() 메소드를 이용한다.

```
mav.setViewName("뷰의 경로");
```

 

데이터를 보낼때는

addObject() 메소드를 이용한다.

```
mav.addObject("변수 이름", "데이터 값");
```



2. `addMember.do`

<img src="../img/addmember.jpg" style="zoom:50%;" />

```java
	@Override
	@RequestMapping(value="/member/addMember.do" ,method = RequestMethod.POST)
	public ModelAndView addMember(@ModelAttribute("member") MemberVO member,
			                  HttpServletRequest request, HttpServletResponse response) throws Exception {
		request.setCharacterEncoding("utf-8");
		int result = 0;
		result = memberService.addMember(member); 
		ModelAndView mav = new ModelAndView("redirect:/member/listMembers.do");
		return mav;
	}
```



3. `listMember.do`

<img src="../img/listmember.jpg" style="zoom:50%;" />

```java
	@Override
	@RequestMapping(value="/member/listMembers.do" ,method = RequestMethod.GET)
	public ModelAndView listMembers(HttpServletRequest request, HttpServletResponse response) throws Exception {
		String viewName = getViewName(request);
		List membersList = memberService.listMembers();
		ModelAndView mav = new ModelAndView(viewName);
		mav.addObject("membersList", membersList);
		return mav;
	}
```



4. `removeMember.do`

<img src="../img/removemember.jpg" style="zoom:50%;" />

```java
	@Override
	@RequestMapping(value="/member/removeMember.do" ,method = RequestMethod.GET)
	public ModelAndView removeMember(@RequestParam("id") String id, 
			           HttpServletRequest request, HttpServletResponse response) throws Exception{
		request.setCharacterEncoding("utf-8");
		memberService.removeMember(id);
		ModelAndView mav = new ModelAndView("redirect:/member/listMembers.do");
		return mav;
	}
```



5. `searchMember.do`

<img src="../img/update.png" style="zoom:50%;" />

```java
@Override
	@RequestMapping(value="/member/searchMember.do" ,method = RequestMethod.GET)
	public ModelAndView searchMember(@RequestParam("id") String id, 
			           HttpServletRequest request, HttpServletResponse response) throws Exception{
		request.setCharacterEncoding("utf-8");
		MemberVO vo = memberService.searchMember(id);
		ModelAndView mav = new ModelAndView("forward:/member/updateForm.do");
		mav.addObject("member",vo);
		return mav;
	}
```



6. `updateMember.do`

```java
@Override
	@RequestMapping(value="/member/updateMember.do" ,method = RequestMethod.GET)
	public ModelAndView updateMember(@ModelAttribute("member") MemberVO member,
			                  HttpServletRequest request, HttpServletResponse response) throws Exception {
		request.setCharacterEncoding("utf-8");
		int result = 0;
		result = memberService.updateMember(member);
		ModelAndView mav = new ModelAndView("redirect:/member/listMembers.do");
		return mav;
	}
```





### 1-3 IN-OUT 확인

1. `memberForm.jsp`

> controller의 return값 mav에 있는 *Form.do을 통해 호출된다
>
> controller와 post/get 매칭해줘야하고 .jsp를 보여준 뒤 submit을 하면 action form속성을 이용해 addMember.do랑 연결된다.

```jsp
<form method="post"   action="${contextPath}/member/addMember.do">
```



2. `listMember.jsp`

```jsp
<body>
<table border="1"  align="center"  width="80%">
    <tr align="center"   bgcolor="lightgreen">
      <td ><b>아이디</b></td>
      <td><b>비밀번호</b></td>
      <td><b>이름</b></td>
      <td><b>이메일</b></td>
      <td><b>가입일</b></td>
      <td><b>삭제</b></td>
   </tr>
   
 <c:forEach var="member" items="${membersList}" >     
   <tr align="center">
      <td>
      	<a href="${contextPath}/member/searchMember.do?id=${member.id }">${member.id}</a>
      </td>
      <td>${member.pwd}</td>
      <td>${member.name}</td>
      <td>${member.email}</td>
      <td>${member.joinDate}</td>
      <td><a href="${contextPath}/member/removeMember.do?id=${member.id }">삭제하기</a></td>
    </tr>
  </c:forEach>   
</table>
<a  href="${contextPath}/member/memberForm.do"><h1 style="text-align:center">회원가입</h1></a>
</body>
```



3. `update.jsp`

> value= "${member.id}"를 통해 자신이 수정하고자 하는 값을 보여준다. 이 값을 수정하고 updateMember.do로 보내게 된다.

```jsp
<body>
	<form method="get"   action="${contextPath}/member/updateMember.do">
	<h1  class="text_center">회원 가입창</h1>
	<table  align="center">
	   <tr>
	      <td width="200"><p align="right">아이디</td>
	      <td width="400"><input type="text" name="id" value="${member.id}"></td>
	   </tr>
	   <tr>
	      <td width="200"><p align="right">비밀번호</td>
	      <td width="400"><input type="password" name="pwd" value="${member.pwd}"></td>
	    </tr>
	    <tr>
	       <td width="200"><p align="right">이름</td>
	       <td width="400"><p><input type="text" name="name" value="${member.name}"></td>
	    </tr>
	    <tr>
	       <td width="200"><p align="right">이메일</td>
	       <td width="400"><p><input type="text" name="email" value="${member.email}"></td>
	    </tr>
	    <tr>
	       <td width="200"><p>&nbsp;</p></td>
	       <td width="400"><input type="submit" value="수정하기"><input type="reset" value="다시입력"></td>
	    </tr>
	</table>
	</form>
</body>
```



---



### 2. 비즈니스서비스연결

- `MemberVO.java`

```java
@Component("memberVO")
public class MemberVO {
	private String id;
	private String pwd;
	private String name;
	private String email;
	private Date joinDate;
	
	..생략
```



- `MemberDAO.java`

> **무엇을 할것인가? 에 따른 입출력을  sql쿼리를 고려하여 잘생각해봐야 한다!!!!!!!!!**
>
> D(delete)L(insert)U(update)는 테이블에 변경을 가하는 메소드인데 변경된 라인수가 몇 개인지 출력해줘야 하기 때문에 int의 출력값을 가진다.



#### **SqlSession 클래스에서 제공하는 여러가지 메소드**

| **메소드** [출력 메소드(입력)]         | **기능**                                                     |
| -------------------------------------- | ------------------------------------------------------------ |
| **List selectList(query_id)**          | id에 대한 select 문을 실행한 후 여러 레코드를 List로 반환    |
| **List selectList(query_id, 조건)**    | id에 대한 select 문을 실행하면서 사용되는 조건도 전달        |
| **T selectOne(query_id)**              | id에 대한 select 문을 실행한 후 지정된 타입으로 한 개의 레코드 반환 |
| **T selectOne(query_id, 조건)**        | id에 대한 select 문을 실행하면서 사용되는 조건도 전달        |
| **Map<K,V> selectMap(query_id, 조건)** | id에 대한 select 문을 실행하면서 사용되는 조건도 전달. Map 타입으로 레코드 반환 |
| **int insert(query_id, Object obj)**   | id에 대한 insert문을 실행하면서 객체의 값을 테이블에 추가    |
| **int update(query_id, Object obj)**   | obj 객체의 값을 조건문의 수정 값으로 사용해 id데 대한 update문 실행 |
| **int delete(query_id, Object obj)**   | obj 객체의 값을 조건문의 조건 값으로 사용해 id데 대한 delete문 실행 |

1. 데이터 베이스에 있는 모든 것을 가져오기 때문에  input은 필요없고 output을 list로 받아오자.

```java
public List selectAllMemberList() throws DataAccessException;
```

2. table에 있는 값을 memberVO에 담고자 하니까  input은 memberVO를 보내 table값과 매칭하고자 한다.

```java
public int insertMember(MemberVO memberVO) throws DataAccessException ;
```

3. table에 있는 한 행의 값을 지정한 id값으로 삭제하려고 하니 input은 id값이 된다.

```java
public int deleteMember(String id) throws DataAccessException;
```

4. table에 있는 한 행의 값을 지정한 id를 통해 불러오려고 하니 input은 id값이 되고 output은 memberVO가 된다.

```java
public MemberVO searchMember(String id) throws DataAccessException;
```

5. table에 있는 한행의 값을 지정한 한행으로 수정하고자 하니 input은 MemberVO값이 된다.

```java
public int updateMember(MemberVO memberVO) throws DataAccessException;
```



- `MemberDAOImpl.java`

> 데이터베이스 작업을 클레스가 아니고 xml과 연결하여 외부로 다 뺐다.
>
> member.xml로 빼내고 autowired로 연결한다.

```java
@Repository("memberDAO") //sql, connection, rs 등등 다없네
public class MemberDAOImpl implements MemberDAO {
	@Autowired
	private SqlSession sqlSession;
    
	@Override
	public List selectAllMemberList() throws DataAccessException {
		List<MemberVO> membersList = null;
		//아래 호출하면 member.xml과 mybatis.xml(이건 미리 다 세팅이 되어있다)이 알아서 커넥션하고 쿼리 날리고 해서 정보를 가져와 리턴해준다. 호출하면 web.xml 의listener -> action-mybatis.xml의sqlssion -> sqlfactory 자세한건 밑에 코드 
		membersList = sqlSession.selectList("mapper.member.selectAllMemberList"); 
		return membersList;
	}
```



- `MemberServiceImpl.java`

```java
@Service("memberService")
@Transactional(propagation = Propagation.REQUIRED)
public class MemberServiceImpl implements MemberService {
	@Autowired
	private MemberDAO memberDAO;
	
	@Override
	public MemberVO login(MemberVO memberVO) throws DataAccessException{
		MemberVO vo = null;
		vo = memberDAO.loginById(memberVO);
		System.out.println("service in");
		return vo;
	}
}
```



---



### Mybatis 관련 xml 파일 추가

<img src="../img/mybatis.jpg" style="zoom:20%;" />



- `action-mybatis.xml`

>데이터베이스 연동 시 반환되는 값을 저장할 빈이나 트랜잭션, 데이터 소스 등 마이바티스 관련 정보를 설정한다.

<img src="../img/Spring_beans.png" style="zoom:67%;" />

action-mybatis.xml 에서는 sqlSessionFactory와 sqlSession, dataSource 객체를 xml에 기술하여 미리 생성(컨테이너에 등록)합니다. 

(1) dataSource에는 DB연결정보가 담겨있고, 

(2) sqlSessionFacory 클래스는 sqlSession을 만드는 역할을 하며, 

(3) SqlSession 클래스는 Sql문을 실제 호출해주는 역할을 한니다. SqlSessionFactory 클래스의 빈 생성만 따로 보면 dataSource객체를 생성하고, modelConfig와 mapper 위치를 지정해 주는 것을 볼 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
 xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd"
 xmlns:tx="http://www.springframework.org/schema/tx" xmlns:context="http://www.springframework.org/schema/context"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://www.springframework.org/schema/beans">


	<bean id="propertyPlaceholderConfigurer"
	class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
	<property name="locations">
	<value>/WEB-INF/config/jdbc.properties</value>
	</property>
	</bean>
	<!-- connection 관련 필요한거 미리 설정해놓는다. -->
	<!-- connection pool -->
 	<bean id="dataSource"
		class="org.apache.ibatis.datasource.pooled.PooledDataSource">
		<property name="driver" value="${jdbc.driverClassName}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
	</bean>
	
 	<!-- connection 반환하는 작업 -->
    <!-- datasource를 이용해 접속하고 resources의 myabatis와 연결 -->
 	<bean id="sqlSessionFactory"
		class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="configLocation"
			value="classpath:mybatis/model/modelConfig.xml" /> 
     		<!-- modelConfig.xml와 연결하여 Member VO 설정 -->
		<property name="mapperLocations" value="classpath:mybatis/mappers/*.xml" /> 
        <!-- 쿼리(select,insert 등등) 관리 -->
	</bean>

	<!-- Dao에 있는 sqlSession 인스턴스 생성 -->
	<bean id="sqlSession" 
		class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg index="0" ref="sqlSessionFactory"></constructor-arg>
	</bean>

	<!-- Member DAO는 개발자가 만든 클래스이므로 XML에서 설정하지 않고 자바코드에서 애너테이션으로 설정-->
	<!-- <bean id="memberDAO"
		class="com.spring.member.dao.MemberDAOImpl">
		<property name="sqlSession" ref="sqlSession"></property>
	</bean> -->

</beans>

```



- `jdbc.properties`

> 이 정보로 connection pool과 연결되어 접근한다.

```
jdbc.driverClassName=oracle.jdbc.driver.OracleDriver
jdbc.url=jdbc:oracle:thin:@localhost:1521:xe
jdbc.username=hr
jdbc.password=1234
```



![img](../img/mybatis2.png)

​																					mybatis 패키지

위의 패키지를 생성하여 모델과 sql을 기술할 **member.xml(class.xml)**과 모델들을 한꺼번에 쉽게(alias) 관리할 **modelConfig.xml** 파일을 생성합니다.



- `member.xml`

> DAO에서 빠져나와 실질적인 Query를 날리는 부분이다. Query를 잘 모르는 개발자에게 부담을 줄이고자 함.
>
> Query를 web.xml과 연결 그리고 거기서 action-mybatis.xml과 연결하여 spring에서 자동적으로 spring beans flow를 따라가게된다.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--property는 memberVO클래스의 각 변수에 들어가고 column은 table 값이다 두 요소의 값을 동일화시키자.-->
<mapper namespace="mapper.member">
	<resultMap id="memResult" type="memberVO">
		<result property="id" column="id" />
        <result property="pwd" column="pwd" />
        <result property="name" column="name" />
        <result property="email" column="email" />
        <result property="joinDate" column="joinDate" />
	</resultMap> 

	<!-- id를 통해 사용하고 값을 resultMap에 저장한다. -->
	<select id="selectAllMemberList" resultMap="memResult">
      <![CDATA[
         select * from t_member	order by joinDate desc	 	
      ]]>
	</select>
    
    ...중략
    
    <!-- id를 통해 사용하고 값을 memberVO에 바로 접근하여 저장한다. -->
    <select id="selectMemberById" resultType="memberVO"  parameterType="String" >
      <![CDATA[
         select * from t_member
         where
         id=#{id}			
      ]]><!-- id가 정해지지 않고 #으로 설정하여 외부에서 들여온다. -->
	 </select>	
    
    
```



- `modelConfig.xml`

> 아까 action-mybatis.xml에서 연결한 부분으로 <typeAliases> 태그를 이용하여 긴 클래스명을 간략하게 사용할 수 있도록 한다. memberVO로 바로 접근 가능. 또한 모델을 등록함으로써 모델들을 관리하는 역할도 할 수 있다.

```XML
<!-- 이렇게 줄여서 사용하겠다 -->
<typeAlias type="com.spring.member.vo.MemberVO"  alias="memberVO" /> 
```



---



### Context 부연설명

web.xml에 있네

**contextConfigLocation 자세히 알기**

> **Spring에 대해 정리해보자면 스프링은 WAR 독립 웹 모듈(애플리케이션)으로 주로 개발된다. 서블릿 컨테이너에 올라가는 웹 애플리케이션이다.**

> **독립 웹 모듈로 만들어진 하나의 스프링 애플리케이션에 애플리케이션 컨텍스트는 몇 개가, 어떻게 만들어질까? 스프링을 제대로 공부했다면 두개가 만들어지는 것을 알 수 있댜.**

> **하나는 ContextLoaderListener에 의해 만들어지는 Root WebApplicationContext이고, 다른 하나는 DispatcherServlet에 의해서 만들어지는 WebApplicationContext이다. 전자는 보통 서비스 계층과 DAO를 포함한 (웹 환경에 독립적인) 빈 들을 담아두고, 후자는 DispatcherServlet이 직접 사용하는 컨트롤러를 포함한 웹 관련 빈들을 빈을 등록하는 데 사용한다. 그리고 이둘은 Parent-Child Application Context 관계로 맺어진다. 따라서 ContextLoaderListener로 만들어지는 컨텍스트를 Root WAC라고 부르는 것이다.**

> 그런데 Root WAC는 사실 없어도 된다. 당연히 스프링 앱에는 Root WAS를 등록하는 ContextLoaderListener가 필요한 것 같지만 실제론 강제되지 않는다. 대신 서블릿 레벨의 WAC에 웹용 빈들은 물론이고 서비스, DAO 계층의 빈들도 모두 넣으면 된다. 실제로 여러 개의 DispatcherServlet이 만들어질 것이 아니라면 Root WAC를 따로 등록하나 아니나 그게 그거다.

> DispatcherServlet 단위의 WAC는 보통 서블릿이름-servlet.xml이라는 이름규칙을 가지고 설정파일을 찾는다. 하지만 이는 얼마든지 바꿀 수 있다. 서블릿 이름과 상관없이 직접 지정한 여러 개의 설정파일을 읽도록 만들어도 그만이다.

> [출저] [InsideSpring (3) 스프링 밖에서 WebApplicationContext에 접근하기](http://toby.epril.com/?p=934)

References : https://dadmi97.tistory.com/79





**my-batis 참조 !!** 

https://coderbycode.tistory.com/52

https://dadmi97.tistory.com/79

