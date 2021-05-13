# 메이븐 주의 사항

### 복붙시 이름에 대해서 고쳐야 할 사항!

예) mc.sn.~~에서 mc.sn.review로 변경시

- `modelConfig.xml`

`<typeAlias type="mc.sn.review.member.vo.MemberVO"  alias="memberVO" />`

- `servlet-context.xml`

```xml
<context:component-scan base-package="mc.sn.review" />
```

- input.jsp

```jsp
<form action="/review/login/result.do" method="post">
```



### 오류

**http: 404 페이지를 찾을 수 없습니다.**

`servlet-context.xml`의 경로와 home.jsp의 위치를 확인!



---



`loginForm.jsp` 테이블에 있는 값이 Id : hong/ pwd : 1212 한 다음에 다시 잘못된 걸로 로그인 하면 여전히 로그인이 되는데 

- `memberControllerImpl.java`

> 아래와 같이 session을 사용하면 30분간 데이터가 유지되기 때문이다. 

```java
if(memberVO != null) {
	    HttpSession session = request.getSession();
	    session.setAttribute("member", memberVO);
```

- result.jsp

> 그래서 이렇게 `session.invalidate` 를 사용하여 세션 로그아웃 기능을 수행해야 한다.

```jsp
<%
	session.invalidate(); //log out 기능
%>
```




