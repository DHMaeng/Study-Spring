## MVC 활용1 `ArrayList<String>` 으로 출력

![image-20210503131012779](..\img\MVC_design.png)

---

### project 구조

![mvcproject](..\img\mvc_project.png)

---

### 세부 코드 (+순서)

##### 1. `JobsDAO.java`

> database에 접근하여 jobs table을 가져와 ArrayList에 저장

```java
public ArrayList<String> selectJobsAll() throws SQLException {
		ArrayList<String> info = new ArrayList<String>();
		String sql = "select * from jobs";
		Connection con = ConnectionManager.getConnection();
		Statement stmt = con.createStatement();
		ResultSet rs = stmt.executeQuery(sql);
		while(rs.next()) {
			info.add(rs.getString(1) +"," + rs.getString(2) + "," + rs.getInt(3) + "," + 			 rs.getInt(4));
		}
		ConnectionManager.closeConnection(con, stmt, rs);
		return info;
	}
```



##### 2. `BusinessService.java`

>JobsDAO()를 호출하는 LIst를 출력으로 가지는 getJobs() 메소드

```java

	public  ArrayList<String> getJobs() {
		ArrayList<String> data = null;
		JobsDAO dao = new JobsDAO();
		try {
			data = dao.selectJobsAll();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return data;
	}
```



##### 3. `ControllerServlet.java`

> getJobs()메소드로 부터 데이터를 받아 session에 올린 뒤 `out_view2.jsp`에 보냄

```java
HttpSession session = req.getSession();
ArrayList<String> data = bs.getJobs();
session.setAttribute("data", data);
res.sendRedirect("./day27/output_view2.jsp");
```



#### 4.  `output_view2.jsp`

> input.jsp 제출을 실행하면 마지막으로 이 화면을 출력한다.

![mvc_output](..\img\mvc_output.png)