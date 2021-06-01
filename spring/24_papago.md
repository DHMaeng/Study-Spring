# Naver papago 이용해보기

> AI부분도 구조를 분리해서 설계할 수 있게 하자

![image-20210601172246240](../img/ai2.png)

![image-20210601172246240](../img/ai.png)

### Controller

- `translate.jsp 호출`

```java
	@RequestMapping(value = "/translate", method = RequestMethod.GET)
	public String trans(Locale locale, Model model) {
		return "translate";
	}
```



- AIController.java

```java
@Controller("aiController")
public class AIControllerImpl   implements AIController {
	@Autowired
	private AIService aiService;
	
	@Override
	@RequestMapping(value="/nmt1" ,method = RequestMethod.GET)
	@ResponseBody
	public String nmt(@RequestParam("words") String words, HttpServletRequest request, HttpServletResponse response) throws Exception {
		// TODO Auto-generated method stub
		 request.setCharacterEncoding("utf-8");
		 System.out.println("aiController "+words);
		 String result = null;
		 result = aiService.translate(words);		
	
		return result;
	}
}
```



### Service

- `AIService.java`

```java
package mc.sn.basic.ai.service;


@Service("aiService")
@Transactional(propagation = Propagation.REQUIRED)
public class AIServiceImpl implements AIService {

	@Override
	public String translate(String words) throws DataAccessException {
		// TODO Auto-generated method stub
		 StringBuffer res = null;
			String clientId = "utyz7cbwgw";//애플리케이션 클라이언트 아이디값";
	        String clientSecret = "MhEJGxjA7CGB52v5bWwlJ9Oz8hbHTsjQM4Ond6wN";//애플리케이션 클라이언트 시크릿값";
	        try {
		         String text = URLEncoder.encode(words, "UTF-8"); //사용자에게 받아온(번역할)부분 새로운 변수로 넘기기
	         String apiURL = "https://naveropenapi.apigw.ntruss.com/nmt/v1/translation";
	         URL url = new URL(apiURL);
	         HttpURLConnection con = (HttpURLConnection)url.openConnection();
	         con.setRequestMethod("POST");
	         con.setRequestProperty("X-NCP-APIGW-API-KEY-ID", clientId);
	         con.setRequestProperty("X-NCP-APIGW-API-KEY", clientSecret);
	         // post request
	         String postParams = "source=ko&target=en&text=" + text;
	         con.setDoOutput(true);
	         DataOutputStream wr = new DataOutputStream(con.getOutputStream());
	         wr.writeBytes(postParams);
	         wr.flush();
	         wr.close();
	         int responseCode = con.getResponseCode();
	         BufferedReader br;
	         if(responseCode==200) { // 정상 호출
	             br = new BufferedReader(new InputStreamReader(con.getInputStream()));
	         } else {  // 오류 발생
	             br = new BufferedReader(new InputStreamReader(con.getErrorStream()));
	         }
	         String inputLine;
	         res = new StringBuffer();
	         while ((inputLine = br.readLine()) != null) {
	             res.append(inputLine);
	         }
	         br.close();
	         System.out.println("service "+res.toString());
	     } catch (Exception e) {
	         System.out.println(e);
	     }
		return res.toString();
	}
}

```



### View

- translate.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Translate</title>
    <!-- jquery라이브러리를 네트워크를 통해서 가져옴 -->
<script type="text/javascript" src="http://code.jquery.com/jquery-latest.js"></script>
<script type="text/javascript">
	function checkResult(){
		temp = {"message":
					{"@type":"response",
					 "@service":"naverservice.nmt.proxy",
					 "@version":"1.0.0",
					 "result":
					 	{"srcLangType":"ko",
						 "tarLangType":"en",
						 "translatedText":"It's a pleasure to meet you."}}}
		alert(temp.message.result.translatedText);
	}
	function sendWords(){
		words = $('#words').val();
		alert(words);//사용자가 입력한 것을 button을 통해 가져온 것을 alert
		$.ajax({
	         type:"get",
	         url:"/spring-ai/nmt1", //이렇게 보낼께
	         contentType: "application/json", //json type을 보낼께
	         data :{"words":$("#words").val()}, //번역할 문장은 words로 보낼께
		     success:function (data,textStatus){
		    	  alert(data);//사용자가 입력한 것을 homcontroller에 버퍼에 있는 것을 alert
		    	  resultText = JSON.parse(data);
		    	  text = resultText.message.result.translatedText;
		    	  alert(text);//파파고를 통해 번역된 것을 alert
		    	  $('#message').text(text);
		     },
		     error:function(data,textStatus){
		        alert("에러가 발생했습니다.");
		     },
		     complete:function(data,textStatus){
		    	 
		     }
		  });
	}
</script>
</head>
<body>
<h1>번역페이지</h1>
<form action="" method="get">
	번역할 문장 : <input type="text" name="" id="words">
	<input type="button" value="확인" onclick="sendWords()">
	<input type="button" value="결과확인" onclick="checkResult()">
</form>
번역된 문장 : <span id="message"></span>
</body>
</html>
```



