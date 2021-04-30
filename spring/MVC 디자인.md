# MVC 디자인

> 각기능이 분리되어있어 개발 및 유지보수가 편리하고 재사용성이 높아진다.
>
> 디자이너와 개발자의 작업을 분업화해서 쉽게 개발할 수 있다.

![KakaoTalk_20210430_165525834](https://user-images.githubusercontent.com/80496345/116668762-b8990c00-a9d8-11eb-954d-624e6c600a69.jpg)

- Controller
  
  - 서블릿이 컨트롤러 역할을 한다.
  
  - 클라이언트의 요청을 분석한다
  
  - 요청에 대해서 필요한 모델을 호출한다
  
  - Model에서 처리한 결과를 보여주기 위해 JSP를 선택한다
  
  - `ControllerServlet.java`
  
    `web.xml`
  
- Model

  - 데이터베이스 연동과 같은 비지니스 로직을 수행한다

  - 일반적으로 DAO와 VO클래스로 이루어져 있다

  - `BusinessService.java`

    `ConnectionManager.java`

    `JobsDAO.java`

![model](https://user-images.githubusercontent.com/80496345/116668594-88516d80-a9d8-11eb-9527-9eab293bce09.jpg)

- View
  
  - JSP가 화면 기능을 담당한다
  
  - Model에서 처리한 결과를 화면에 표시한다
  
  - `input_view.html`
  
    `output_view.jsp`
  
  

- import/export
  - export : war Export ->destination설정->export source files 체크 -> finish
  - import : war file import

