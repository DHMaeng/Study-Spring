# MVC 활용2_여러개 요청

### 1. `input.jsp`

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>여러개 요청</title>
</head>
<body>
<a href = "../controller?command=1">첫번째 레코드 가져오기</a><br>
<a href = "../controller?command=2">두번째 레코드 가져오기</a>
</body>
</html>
```

?를 사용하여 command라는 key값과 1,2이라는 value를 보낸다. 



---

### 2. 주소로 호출

![mvc2_input](..\img\mvc2_input.png)

- 첫번째 레코드 : `http://localhost:9090/web_week6?controller?command=1`
- 두번째 레코드 : `http://localhost:9090/web_week6?controller?command=2`



---

### 3. `ControllerServlet.java`

```java
	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		String command = req.getParameter("command");//client가 보내는 데이터를 키값(이름)을 통해서 가져온다.
		System.out.println("work");			
		System.out.println(command);//확인
		BusinessService bs = new BusinessService();
		HttpSession storage = req.getSession();
		String url = null;
		if(command.equals("1")) { //command가 1일 때
			String result = bs.checkWorkingDatabase();
			storage.setAttribute("result", result);
			url = "./day27/output_view.jsp";	
		} else if(command.equals("2")) { //command가 2일 때
			ArrayList<String> data = bs.getJobs();
			storage.setAttribute("data", data);
			url = "./day27/output_view2.jsp";
		}
		resp.sendRedirect(url);
	}
```



---

### 4. `output_view2.jsp`

- 첫번째

  ```
  PR_REP,Public Relations Representative,4500,10500 
  ```

- 두번째

  ```
  AD_PRES,President,20080,40000
  AD_VP,Administration Vice President,15000,30000
  AD_ASST,Administration Assistant,3000,6000
  FI_MGR,Finance Manager,8200,16000
  FI_ACCOUNT,Accountant,4200,9000
  AC_MGR,Accounting Manager,8200,16000
  AC_ACCOUNT,Public Accountant,4200,9000
  SA_MAN,Sales Manager,10000,20080
  SA_REP,Sales Representative,6000,12008
  PU_MAN,Purchasing Manager,8000,15000
  PU_CLERK,Purchasing Clerk,2500,5500
  ST_MAN,Stock Manager,5500,8500
  ST_CLERK,Stock Clerk,2008,5000
  SH_CLERK,Shipping Clerk,2500,5500
  IT_PROG,Programmer,4000,10000
  MK_MAN,Marketing Manager,9000,15000
  MK_REP,Marketing Representative,4000,9000
  HR_REP,Human Resources Representative,4000,9000
  PR_REP,Public Relations Representative,4500,10500
  ```

  