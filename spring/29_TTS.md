# 번역 again

`main`

```java
public static void main(String[] args) {
		// TODO Auto-generated method stub
		System.out.println("Hello");
		//한글(나는 당신을 사랑합니다)을 영어로 번역하는 코드를 작성하여 출력하시오
		//네이버 클로바를 이용하면 된다.
		String words = "나는 당신을 사랑합니다.";
		AITest1 at = new AITest1();
		String result = at.translate(words);
		System.out.println(result);
	}
```



`translate(String words)`메소드

> json part 추가

```java
JSONObject jsonObj = new JSONObject(res.toString());
    		JSONObject text1 = (JSONObject)jsonObj.get("message");
    		text1 = (JSONObject)text1.get("result");
    		text2 = (String)text1.get("translatedText");
```



`json-20140107.jar` 파일 추가

> spring에선 pom.xml에 dependency로 추가했었다.

```java
프로젝트 폴더 오른쪽 버튼 -> bulid path -> configure bulid path -> Classpath 클릭 -> add External JARs 
```



# tts

### 1. View

![](../img/tts.PNG)

`ttsResult.jsp`

> resources/ 로 경로 수정

```jsp
<head>
		<meta charset="UTF-8">
		<title>OCR</title>
		<script src="<c:url value='resources/js/jquery-3.6.0.min.js'/>"></script>
		<script src="<c:url value='resources/js/tts.js'/>"></script>
	</head>
```



`tts.js`

![](C:/Users/aodeh/git/Study-spring/img/ai8.PNG)

> json으로 우리가 원하는 데이터만 뽑아내기

```js
			success:function(result){
				var data = JSON.parse(result);
				var field1 = (data.images[0].fields[0].name)+":"+data.images[0].fields[0].inferText;
				var field2 = (data.images[0].fields[1].name)+":"+data.images[0].fields[1].inferText;
				$('#resultDiv').text(field1+","+field2);
			},
```





### 2. controller

`HomeController.java`

> `ttsResult.jsp` 연결

```java
@RequestMapping(value = "/ttsResult", method = RequestMethod.GET)
	public String tts(Locale locale, Model model) {
		
		return "ttsResult";
	}
```



`AIController.java`

> service 호출 filepathName과 language넘겨줌

```java
result = aiService.clovaTextToSpeech(filePathName, language);
```



### 3. service

`AIService.java`

> controller에서 부터 만들면 된다!  -> service -> serviceImpl 순으로 만든 다음 코드 작성



`clovaTextToSpeech 메소드`

> ID, scret값 설정

```java
String clientId = "~";//애플리케이션 클라이언트 아이디값";
        String clientSecret = "~";//애플리케이션 클라이언트 시크릿값";
```

> file 내용을 보내야 하기 때문에 파일을 읽어서 stringbuffer에 넣는 작업

```java
try {
        	File file = new File(filePathName);
        	FileReader fr = new FileReader(file);
        	BufferedReader br1 = new BufferedReader(fr);
        	StringBuffer sb = new StringBuffer();
        	String temp = null;
        	while((temp=br1.readLine())!= null) {
        		sb.append(temp);
        	}
        	br1.close();
        	fr.close();
            String text = URLEncoder.encode(sb.toString(), "UTF-8"); // 13자
```

>controller에서 보내준 language 설정

```java
 String postParams = "speaker="+language+"&volume=0&speed=0&pitch=0&format=mp3&text=" + text;
```

> mp3 파일명과 저장할 경로를 설정

```java
 // 랜덤한 이름으로 mp3 파일 생성
                String tempname = Long.valueOf(new Date().getTime()).toString();
                result = "tts_"+tempname+".mp3";
                File f = new File("c:/ai/"+result);
                f.createNewFile();
                OutputStream outputStream = new FileOutputStream(f);
                while ((read =is.read(bytes)) != -1) {
                    outputStream.write(bytes, 0, read);
                }
                outputStream.flush();
                outputStream.close();
                is.close();
```

> result 선언 후 값을 받아 리턴시켜준다.

```java
String result = null;
...
    
result = response.toString();
...
    
return result;
```



### 4. AI



### 5. 나머지 환경설정

`server.xml`

> <Context docBase="c:\\ai\\" path="/ai" reloadable="true"/> 추가

```xml
<Context docBase="spring-ai-maeng" path="/spring-ai-maeng" reloadable="true" source="org.eclipse.jst.jee.server:spring-ai-maeng"/>
      <Context docBase="c:\\ai\\" path="/ai" reloadable="true"/>
      </Host>
```



`servlet-context.xml`

> <resources mapping="/ai/**" location="c:\\ai\\" />  추가

```xml
<!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
	<resources mapping="/resources/**" location="/resources/" />
	<resources mapping="/ai/**" location="c:\\ai\\" />
```

