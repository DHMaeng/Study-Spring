# Tomcat?

> Tomcat(톰캣)은 apachesoftware 재단에서 만든 Java Servlet&jsp 기술 구현을 위한 Open Source이고 WAS에 속한다.



 **cf) 아파치(apache)란?**

세계에서 가장 많이 쓰는 웹 서버중 하나이며, 아파치 소프트웨어 재단에서 관리하는 HTTP 웹 서버이다. 

- Apache 아파치 ⊂ Web Server 웹서버
- Tomcat 톰캣 ⊂ WAS 와스 



### 1. WAS?

> Web Application Server의 약자로 JSP같은 동적 컨텐츠를 처리하는 서버다.

- 일반적인 html이나 사진, 플래시 같은 정적인 컨텐츠는 Web Server만 있어도 무방하지만 스크립트를 통한 동적인 페이지를 생성하게 해주고 태그, 라이브러리 등을 개발할 수 있는 등 활용도가 높은 JSP, ASP, PHP같은 서버 스크립트들이 등장하게 되었다. 이러한 스크립트들을 처리할 수있는 WAS라는 제품들을 사용한다.

- 예를들면 네이버를 실행하기 위해 웹 브라우져에서 http://www.naver.com을 부른다. 네이버에서 웹서버의 요청을 받아 HTML이나 오브젝트를 HTTP 프로토콜을 이용해 사용자에게 보내준다. 하지만 JSP같은 동적 컨텐츠를 처리하기 힘드니 WAS에게 요청을 해 WAS에서 처리하여 웹서버에 다시 보내주면 네이버 페이지가 보이게 된다.

- 기본적으로 Servlet Contatiner를 포함하고 있어 웹서버 역할도 한다.

  - **Servlet Contatiner?**

    > servlet을 compile 하여 class 파일로 변환하여 담아 놓아 관리하는 컨테이너.
    
    

### 2.  그럼 왜 톰켓 자체를 웹서버로 사용하지 않는가?

클라이언트에서 요청이 들어오는 순서

`클라이언트 - 브라우저 - 웹서버 - WAS - DB `

컴파일 등이 필요없는 정적컨텐츠는 웹서버가 간단한 로직으로 처리할 수 있으니 WAS를 거칠 필요가 없다. 그리고 서비스 별로 WAS를 두어 WebServer의 가상호스트를 사용하여 서비스별로 관리할 수 있다.