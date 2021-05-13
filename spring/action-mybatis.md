### action-mybatis.xml 사용시 확인해야 할것

-> 데이터 베이스 사용

action-mybatis.xml 사용시

web.xml 에서 action-mybatis.xml이라고 바꿔줘야하고

WEB-INF 밑에 config>jdbc>jdvc.properties 와 

​						lib>ojdbc6.jar 추가

src/main/resources>mybatis.mappers>member.xml 와

​									>mybatis.model>modelConfig.xml 추가  

modelConfig.xml에 typeAlias 경로 수정해줘야한다.