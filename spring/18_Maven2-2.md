### 3. Database 연결

### Mybatis 관련 xml 파일 추가

<img src="C:\Users\aodeh\git\Study-spring\img\mybatis.jpg" style="zoom:20%;" />



- `action-mybatis.xml`

>데이터베이스 연동 시 반환되는 값을 저장할 빈이나 트랜잭션, 데이터 소스 등 마이바티스 관련 정보를 설정한다.

<img src="C:\Users\aodeh\git\Study-spring\img\Spring_beans.png" style="zoom:67%;" />

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



![img](C:\Users\aodeh\git\Study-spring\img\mybatis2.png)

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

**my-batis 참조 !!** 

https://coderbycode.tistory.com/52

https://dadmi97.tistory.com/79

