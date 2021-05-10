### Maven 소개/이론



메이븐은 프로젝트 구조와 내용을 기술하는 선언적 접근방식의 오픈소스 빌드 툴 입니다. 
컴파일과 동시에 빌드를 수행할 수 있으며 테스트를 병행하거나 서버측 디플로이 자원을 관리할 수 있는 환경을 제공합니다. 
하지만 아무래도 개발자들에게 가장 큰 장점은 프로젝트의 종속 라이브러리들과 그 라이브러리에 영향을 미치는 Dependency 자원까지 관리 할 수 있다는 점일 것 입니다. 

즉, jar 파일을 다운받아 프로젝트에 추가할 경우 그것과 연관된 다른 종속 라이브러리 또한 다 찾아야 하는 불편함을 Maven을 통해서 일관성 있는 라이브러리간의 의존관계 (의존성) 관리를 할 수 있다는 점입니다. 
이는 단순히 라이브러리 뿐 아니라 프로젝트별 모듈의 의존성 또한 관리가 된다는 뜻이기도 합니다. 


메이븐은 프로젝트 전반의 리소스 관리와 Configuration 파일, Doc 생성 및 이와 관련한 표준 디렉터리 구조를 처음부터 일관된 형태로 구성하여 진행하기 때문에 프로젝트 관리 및 배포 역할을 하는 다른 툴 들과의 연계에서도 뛰어난 유연성을 보여줍니다. 

단점은, 버전별로 이클립스에서 구동되는 방식이 약간 호환성이 떨어진다는 점인데, 사용된 플러그인의 문제인지 메이븐 자체의 하위 호환성 문제인지는 모르겠습니다. 가끔 저장소 접근에 관한 문제도 발생한다고 알려져 있습니다. 


메이븐을 학습하기 위해서는 메이븐을 설치하고 POM (Project Object Model) 을 작성한 후 각종 빌드 스크립트 혹은 명령어를 통해 배워나가야 하지만 여기서는 기본적인 사용법과 더불어 STS를 통해 어플리케이션을 작성 한 후 어떻게 이클립스상에서 메이븐을 활용하는지에 초점을 맞춰 진행하도록 하겠습니다. 



### Maven의 기본적인 사용법





#### Maven 프로젝트의 기본 디렉토리 정책





![img](https://t1.daumcdn.net/cfile/tistory/240B99395571423518)







메이븐의 프로젝트 관리 디렉토리는 최상위에 프로젝트를 기준으로 pom.xml 이라는 메이븐 프로젝트 설정 파일이 존재하며 그것을 바탕으로 프로젝트와 관련된 정보를(라이브러리 및 빌드 정보등) 기술하게 되어 있습니다. 

src 밑으로는 main 과 test 라는 디렉토리가 존재하며 각 하위에 java 와 resources 가 위치하게 됩니다. 

기본적인 메이븐의 주요 디렉토리는 아래와 같습니다. 



- src/main/java : 자바 소스 파일 위치 시킵니다. 이 하위에 org.gliderwiki ... 와 같은 패키지를 배치합니다.
- src/main/resources : 프로퍼티나 XML 등 리소스 파일이 위치합니다.
- src/main/webapp : Web Project 일 경우 WEB-INF등 웹 어플리케이션 리소스를 위치시킵니다.
- src/test/java : JUnit등의 테스트 파일이 위치하게 됩니다.
- src/test/resources : 테스트시에 필요한 resource 파일이 위치하게 됩니다.

src 위치 하위에 main 부분과 test 부분을 경로가 나뉘는 이유는 개발과 동시에 테스트 코드를 작성 하기 위함일 것입니다. 
XP 프로그래밍과 같이 로직의 개발과 테스트가 동일한 시점에 가능하다면 class package 정책에 맞춰 test 단계의 코드들 또한 같이 개발할 수 있게 하기 위함입니다. 



#### POM 파일의 기본 구성



STS 상에서 Maven 프로젝트를 생성하거나 Spring Template 로 프로젝트를 생성하게 되면 루트애 pom.xml 파일이 위치하게 됩니다. 

pom 파일에는 프로젝트 정보가 표시되며, 이름, url, 이슈트래킹 시스템에 대한 명시, CI 서버 정도등 프로젝트에 필요한 시스템에 대한 명세가 가능합니다. 

아래는 STS 에서 Maven Project를 생성했을시에 나오는 pom 파일의 내용입니다. (프로젝트 생성 부분은 추후에 설명합니다) 



[?](http://www.gliderwiki.org/wiki/105#)

```
<project xmlns=``"http://maven.apache.org/POM/4.0.0"` `xmlns:xsi=``"http://www.w3.org/2001/XMLSchema-instance"`` ``xsi:schemaLocation=``"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"``>`` ``<modelVersion>4.0.0</modelVersion>` ` ``<groupId>org.gliderwiki</groupId>`` ``<artifactId>SampleApp</artifactId>`` ``<version>0.0.1-SNAPSHOT</version>`` ``<packaging>jar</packaging>` ` ``<name>SampleApp</name>`` ``<url>http:``//maven.apache.org</url>` ` ``<properties>``  ``<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>`` ``</properties>` ` ``<dependencies>``  ``<dependency>``   ``<groupId>junit</groupId>``   ``<artifactId>junit</artifactId>``   ``<version>3.8.1</version>``   ``<scope>test</scope>``  ``</dependency>`` ``</dependencies>``</project>
```



여기서 핵심은 dependencies 항목입니다. dependencies 항목은 어플리케이션을 컴파일 하고, 테스트하고, 실행하는데 필요한 라이브러리 목록을 기술합니다. 

기본으로 생성된 메이븐 프로젝트에는 Junit 라이브러리가 test scope로 설정되어 있습니다. 
바로 이처럼 dependency 설정을 통해 의존관계에 있는 라이브러리를 추가할 수 있습니다. 

여기 예시에는 나와있지 않지만 프로젝트 정보와 별도 항목으로 빌드(build) 설정, plugin 설정, 빌드환경정보, pom의 연관정보 (의존프로젝트, 상위 하위 모듈등), 리포팅(checkStyle 등을 이용) 등의 주요 항목들을 기술하게 되어있습니다. 


각 연관 태그들에 대해서 알아보도록 합니다. 



- 프로젝트 정보 항목

- - name : 프로젝트의 이름
  - url : 사이트 URL

- 프로젝트 연관 정보

- - groupId : 프로젝트 그룹 ID, 도메인이나 특정한 정보로 식별.
  - artifactId : 프로젝트 Artifact ID 설정, 프로젝트에 의해 생성되는 Artifact 명 - version . packaging 의 형태로 Artifact 파일이 생성.
  - version : 버전 설정. 여기서는 SampleApp-0.0.1-SNAPSHOT.jar 가 되는데 0.0.1-SNAPSHOT 이 version이 됨.
  - packaging : 패키징 타입 설정. 여기서는 jar 파일로 생성됨을 의미. jar 뿐만 아니라 웹 어플리케이션을 위한 war나 JEE를 위한 ear 등의 패키징 타입을 지정할 수 있음.

- dependencies : 이 프로젝트에서 의존하는 다른 프로젝트 정보를 기술.

- - dependency : 의존하는 프로젝트 POM 정보를 기술

  - - groupId : 의존하는 프로젝트의 그룹 ID
    - artifactId : 의존하는 프로젝트의 artifact ID
    - version : 의존하는 프로젝트의 버전
    - scope : 의존하는 범위 (compile, runtime, provided, test 로 scope 가 나뉨)


여기에서 중요한것은 dependency인데 Spring 이나 JDBC처럼 의존관계가 추가 되는 라이브러리등은 해당 타겟 라이브러리만 지정해주면 Maven 이 자동으로 타겟이 의존하는 라이브러리를 설정해줍니다. 간략하게 설명하자면 해당 라이브러리를 다운받을 때 리포지토리에 관련 pom도 같이 다운로드를 받게 됩니다. 그 pom을 통해 명시된 의존 관계의 모듈들도 함께 다운로드가 되게 됩니다. 그리고 그 정보를 클래스 패스에 추가하게 됩니다. 

위에 설명한 구조를 표현하면 아래와 같습니다. 



![img](https://t1.daumcdn.net/cfile/tistory/24164C335571424C2B)









#### 의존 범위 (compile, runtime, provided, test)



의존 범위는 쉽게 생각하면 서버측 구동 자원이 로컬에만 필요할 경우 그것을 구분하기 위한 범위 설정이라고 보면 됩니다. 



- compile : 기본 scope. 컴파일 할 때 필요
- runtime : 컴파일시에는 필요하지 않지만 런타임에 필요. 배포시 포함.
- provided : 컴파일 할 때 필요하지만, 컨테이너 등에서 기본으로 제공되는 모듈임을 의미(에를들면 servlet-api.jar 같은...) 배포시에 제외됨.
- test : 테스트 단계에 필요. 배포시 제외

#### 리파지토리



메이븐은 pom 파일에 지정한 모듈들을 중앙 리파지토리에서 다운받게 됩니다. 또한 로컬 영역에 Nexus등을 이용하여 리파지토리를 구축하기도 합니다. 
로컬 리파지토리는 지정된 위치에 생성되며 일단 원격 리파지토리로부터 파일을 다운로드해서 로컬 리파지토리에 저장하면, 그 뒤로는 로컬 리파지토리를 사용하게 됩니다. 



POM 정보 찾기
http://mvnrepository.com 에서 Maven의 중앙 리파지토리에 등록된 POM 정보를 검색할 수 있습니다. 이 사이트를 통해 라이브러리의 dependency 설정을 조회하여 적용할 수 있습니다. 





![img](https://t1.daumcdn.net/cfile/tistory/274E9C3A557142651B)







위의 이미지처럼 각 라이브러리를 찾아서 dependency 영역을 pom.xml 에 추가해주면 됩니다. 

아직 설명하지 못한 남은 속성들은 프로젝트 생성 후 의존성 관리 부분과 빌드 영역에 보태어 좀 더 자세히 설명하도록 합니다. 



#### 메이븐의 라이프사이클





![img](https://t1.daumcdn.net/cfile/tistory/246C554B557142781D)







메이븐은 미리 정의 하고 있는 빌드 순서를 통해 (라이프사이클) 빌드결과 삭제, 컴파일 및 자원복사, 테스트, 압축(패키지) 배포 등의 단계를 밟게 됩니다. 
Maven에서는 clean, build, site의 세 가지 Lifecycle을 기본적으로 제공하고 있습니다. 또한 컴파일(compile), 테스트(test), 패키지(package), 배포(depooy)등의 과정은 빌드 Lifecycle에 속하게 됩니다. 
각 단계에 따른 Goals 이 존재합니다. Maven에서 기본으로 제공하는 Phase를 실행하면 해당 Phase와 연결된 플러그인의 Goal이 실행됩니다. 대표적으로 resources, compile, test, package 등이 존재합니다. 



| 단계 (Phases)    | 설명                                                         |
| ---------------- | ------------------------------------------------------------ |
| clean            | 빌드된 결과물을 제거함.                                      |
| compile          | 소스 코드 컴파일                                             |
| test             | Junit 이나 TestNG 혹은 정적분석 도구와 함께 단위 테스트를 수행 (테스트 실패시 빌드 실패) |
| package          | 배포가능한 형태 (Jar, War, Ear등) 로 컴파일 코드를 패키징    |
| integration-test | 통합테스트를 진행하고, 필요할 시 패키지를 디플로이함         |
| install          | 로컬 머신의 다른 프로젝트에서 종속 라이브러리로 사용될 수 있도록 패키지를 로컬 저장소에 설치함 |
| deploy           | 통합환경 또는 릴리즈 환경에서 다른 개발자들 혹은 프로젝트들과 공유할 수 있도록 최종 패키지를 원격 저장소에 복사 |

이밖에 더 다양한 단계가 존재하지만 가장 많이 사용되는 것만 정리해보았습니다. 
이 라이프사이클을 통해 프로젝트를 빌드하고 배포하기 위한 가상의 Maven 프로젝트를 생성하여 설명하도록 합니다. 



출처: https://unabated.tistory.com/entry/Maven-을-이용한-프로젝트-생성-및-활용 [랄라라]