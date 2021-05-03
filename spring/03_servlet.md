# 서블릿(servlet) 이해하기

## 1. 서블릿이란?

> 클라이언트 요청에 따라 서버쪽에 실행되는 동적인 서비스 클레스이다. 자바의 일반적인 특성을 가지지만 독자적으로 실행되지 못하고 톰캣과 같은 JSP/Servlet 컨테이너에서 실행된다



## 2. 서블릿 API 계층 구조와 기능

>서블릿은 자바로 만들어졌으니 클래스들 간에 계층 구조를 가지고 서블릿 API(Application Programming Interface) 상속 받아 사용.

![서블릿 API 계층 구조](https://user-images.githubusercontent.com/80496345/116816042-51778500-ab9b-11eb-9d5a-5326cca22c15.jpg)

`Servelt API` > `Servlet interface, servletConfig interface` > `GnericServlet` > `HttpServlet`

순서로 상속받아 사용한다.



## 3. 서블릿의 생명주기 메서드

> 실행하면 서블릿 생명주기 메서드를 가지는데 다음과 같다.

| 생명주기 단계 | 호출 메서드       | 특징                                                         |
| ------------- | ----------------- | ------------------------------------------------------------ |
| 1. 초기화     | init()            | 서블릿 요청시 맨 처음 한번만 호출되며 초기화 작업을 수행     |
| 2. 작업 수행  | doGet(), doPost() | 서블릿 요청시 매번 호출되며 실제로 요청하는 작업 수행        |
| 3. 종료       | destroy()         | 서블릿이 기능을 수행하고 메모리에서 소멸될 때 호출되며 마무리 작업을한다. |



## 4. 실습

> 사용자 정의 서블릿 클래스 만들기 -> 서블릿 생명주기 메서드 구현 ->서블릿 매핑작업 -> 웹브라우저에서 서블릿 매핑이름으로 요청하기



#### 1. 서블릿 클래스 만들기

- dynamic web project를 생성 (next 마지막창에서 web.xml 생성 체크)
- servlet package 및 클래스 생성

![servlet project 기본구성](https://user-images.githubusercontent.com/80496345/116816043-51778500-ab9b-11eb-8e15-da7354ced272.jpg)

#### 2. 서블릿 생명주기 메서드 구현

- 클래스에 원하는 API 상속( ex) HttpServlet) 하여 자바 코드 생성

```java
package mc.sn.servlet;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class FirstServlet extends HttpServlet { //httpServlet를 상속받는 얘는 라이프 사이클(초기화,작업수행,종료)을 받는다 

	@Override
	public void init() throws ServletException {
		// TODO Auto-generated method stub
		super.init();
		System.out.println("init");
	}
	
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("get");
		this.doPost(req,resp);
		
	}
	
	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// TODO Auto-generated method stub
		System.out.println("post");
		resp.setContentType("text/html");
		PrintWriter out = resp.getWriter();
		out.print("<html>");
		out.print("<head>");
		out.print("<style>");
		out.print("h1 {color : magenta;}");
		out.print("</style>");
		out.print("</head>");
		out.print("<body>");
		out.print("<h1>Welcome Servlet's World! Test</h1");
		out.print("</body>");
		out.print("</html>");
	}
	
	@Override
	public void destroy() { //내용이 수정이 되면 자동으로 실행되어 없애준다
		// TODO Auto-generated method stub
		super.destroy();
		System.out.println("destroy");
	}
}

```



#### 3. 서블릿 매핑작업 

> 브라우저에서 서블릿 이름으로 요청하는 방법은 패키지 이름이 포함된 클래스 이름 전부를 입력해야한다. 그래서 이 이름이 길어지면 불편하고 보안에도 안좋으므로 서블릿 이름 매핑

- 각 프로젝트에 있는 web.xml에서 설정한다
- <servlet> 태그와 <servlet-mapping> 태그를 이용한다.
- 여러 개의 서블릿 매핑시에는 <servlet>태그를 먼저 정의하고 <servlet-mapping> 태그를 정의한다.

```xml
<!--브라우저에서 요청하는 매핑 이름에 대해 실제로 실행하는 서블릿 클래스를 설정하는 태그-->
<servlet>
  <!-- <servlet-mapping> 태그의 <servlet-name>태그 값과 동일 -->
  <servlet-name>myservlet</servlet-name> 
  <!-- 매핑 이름에 대해 실제로 기능을 수행하는 서블릿 클래스 설정 -->
  <servlet-class>mc.sn.servlet.FirstServlet</servlet-class> 
</servlet>
<!-- 브라우저에서 요청하는 논리적인 서블릿 설정 -->
<servlet-mapping> 
  <!-- 매핑 이름으로 요청시 값이 같은 <servlet>태그 안의 <servlet-name>태그와 연결 -->
  <servlet-name>myservlet</servlet-name> 
  <!-- 브라우저에서 mc.sn.servlet.FirstServlet을 요청하는 논리적인 서블릿 이름 -->  
  <url-pattern>/first</url-pattern>
</servlet-mapping> 
```

![image-20210430165104220](https://user-images.githubusercontent.com/80496345/116668918-ebdb9b00-a9d8-11eb-8631-99318a1eae23.png)

#### 4. 톰캣에 프로젝트 실행

`Tomcat v9.0 Server at localhost [stopped,Republish]` 오른쪽 버튼 누르고 해당 프로젝트를 add한다.



#### 5. 브라우저에서 서블릿 요청

`http://ip주소:포트번호/프로젝트이름(컨텍스트이름)/서블릿매핑이름`

`http://localhost:9090/web_week5/first`



# 서블릿 기초

> 톰캣과 같은 WAS(Web Application Server, 웹 어플리케이션 서버)가 처음 나왔을때 웹 브라우저 요청을 스레드 방식으로 처리하는 기술이 서블릿이다.

- HttpServletRequest 여러가지 메서드 p178 참조

- HttpServletResponse 메서드 p179 참조

- `<form>` 태그 여러 속성

  | 속성    | 기능                                                       |
  | ------- | ---------------------------------------------------------- |
  | name    | `<form>` 태그의 이름 지정                                  |
  | method  | `<form>` 태그 안에서 데이터를 전송할 방법을 지정(GET/POST) |
  | action  | `<form> `태그에서 데이터를 전송할 서블릿이나 JSP를 지정    |
  | encType | `<form>` 태그에서 전송할 데이터의 encoding 타입을 지정     |



- `<form>` 태그로 전송된 데이터를 받아 오는 메서드

  | 메서드                                   | 기능                                                         |
  | ---------------------------------------- | ------------------------------------------------------------ |
  | String getParameter(string name)         | name의 값을 알고 있을 때 name에 대한 전송된 값을 받아오는데 사용 |
  | String[] getParameterValues(string name) | 같은 name에 대한 여러 개의 값을 얻을 때 사용                 |
  | Enumeration getParameterNames()          | name 값을 모를 때 사용                                       |

  