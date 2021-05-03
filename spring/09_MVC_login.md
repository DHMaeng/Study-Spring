# MVC 활용3 login

문제 설명

사용자로부터 ID/Password를 받아와 정보 테이블(DB)에 회원 정보 조회

![mvc3_design](..\img\mvc3_design.png)

## DAO(Data Access Object)

데이터 사용기능 담당 객체. 

DataBase 접근을 하기 위한 로직과 비지니스 로직을 분리하기 위해 사용. 

때문에 DB Connection 로직까지 설정되어있는 경우가 많음. 

DB를 사용해 데이터를 CRUD(조회, 조작)하는 기능을 전담.



## DTO(Data Transfer Object)

데이터 저장 담당 객체. 

Controller, Service, View 등 계층간 데이터 교환을 위해 사용되는 객체. 

로직을 갖지 않는 순수한 데이터 객체이며 getter, setter 메소드만을 포함. 

가변의 성격을 가짐.



## VO(Value Object)

데이터 저장 담당 객체. 

DTO와 혼용해서 쓰이지만, VO는 값(Value)을 위해 쓰이는 객체로 불변(read only)의 속성을 가짐. 

보통 getter의 기능만을 포함.



## BO(Business Object)

비즈니스 로직을 포함하는 오브젝트. 

VO인데 비즈니스관련내용을 담은 VO라고 보면될듯.



---

### 1. `login.jsp`

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Login</title>
</head>
<body>
	<form action = "../login" method = "post" encType = "UTF-8">
		아이디 : <input type = "text" name = "userId"><br>
		비밀번호 : <input type = "password" name = "pwd"><br>
		<input type = "submit" value = "로그인">
	</form>
</body>
</html>
```



### 2. `LoginServlet.java`

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.getWriter().append("Login Served at:").append(request.getContextPath());
		
		String id = request.getParameter("userId");
		String pwd = request.getParameter("pwd");
		System.out.println(id+","+pwd);
    
		LoginDAO dao = new LoginDAO();
		LoginVO vo = dao.checkMember(id, pwd);
		
		HttpSession storage = request.getSession();
		storage.setAttribute("vo",vo);
		response.sendRedirect("./day27/result.jsp");
	}
```



### 3. `LoginVO.java`

> 조회된 회원정보를 LoginVO 속성(클래스 타입?)으로 설정하기 위한 LoginVO 클래스

```java
package mc.sn.vo;

public class LoginVO {
	private String name;
	private String id;
	private String pwd;
	
	public LoginVO(String name, String id, String pwd) {
		this.name = name;
		this.id = id;
		this.pwd = pwd;
	}
	
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getPwd() {
		return pwd;
	}
	public void setPwd(String pwd) {
		this.pwd = pwd;
	}
}
```



### 4. `LoginDAO.java`

```java
public class LoginDAO {

	public LoginVO checkMember(String id,String pwd) {
		System.out.println(id+","+pwd);
		LoginVO vo = null;
		Connection con = ConnectionManager.getConnection();
		String sql = "select employee_id,first_name,last_name from employees "
					+ " where employee_id = ? and last_name= ?";
		sql = "select userid,username,birthyear from userTBL where userid = '"+id+"'";
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {
			pstmt = con.prepareStatement(sql);
			//pstmt.setInt(1, Integer.parseInt(id.trim())); //employee
			//pstmt.setString(2,pwd.trim());
			
			//pstmt.setString(1,id.trim());//char형은 setString이 안먹어서 오류가 났다.
			//pstmt.setInt(1, Integer.parseInt(pwd.trim()));//birthyear은 숫자니까
			rs = pstmt.executeQuery();
			while(rs.next()) {
				vo = new LoginVO(rs.getString(2),rs.getString(1),rs.getString(3));//가져올땐 다 string가능
			}
			
			ConnectionManager.closeConnection(con, pstmt, rs);
			
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return vo;
	}
}
```



### 5. result.jsp

```java
<%@page import="mc.sn.vo.LoginVO"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Result</title>
</head>
<body>
<%	
	LoginVO vo = (LoginVO)session.getAttribute("vo");
	if(vo == null){
		out.print("해당하는 사용자가 없습니다.");
	} else {
		out.print(vo.getName()+"님 반갑습니다.");
	}
%>	
</body>
</html>
```



### 6. 출력값

- `Employee table`

![mvc3_table](..\img\mvc3_table.png)

- `login.jsp` : id=100, pwd= King

![mvc3_login](..\img\mvc3_login.png)



- `result.jsp`

![mvc3_result](..\img\mvc3_result.png)

