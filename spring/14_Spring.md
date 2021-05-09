# Spring

new를 통해 인스턴스를 계속 생성하여 클래스들 간에 연결을 하면 다음과 같은 문제가 있다. 

- 계속 new를 통해 객체가 생성되니 메모리 문제가 발생한다.
- 인스턴스의 클래스가 변경이 있으면 연결을 했던 객체들과 수정이 필요로 한다.



그래서 컨테이너(관리자)가 애플리케이션 간의 느슨한 결합을 제어하는데 그게 IOC이고

DI는 그 클래스의 생성자나 set메소드로 필요로하는 객체를 가져오는 기능을 말한다.

이렇게 되니 코드에서 직접적인 연관 관계가 발생하지 않게 되므로 각클래스들의 변경이 자유로워 진다.  이것을 loosely coupled(느슨한 결합, 약한결합) 이라고 한다.



### 스프링의 특징 한마디로 정의!

1. 경량 컨테이너(크기와 부하의 측면)로서 자바 객체를 직접 관리

   - 각각의 객체 생성, 소멸과 같은 라이프 사이클을 관리하며 스프링으로부터 필요한 객체를 얻어올 수 있다.
   - 스프링프레임워크 = 경량 컨테이너(?)

2. IOC(Inversion of Control, 제어역행, 제어 역전)

   - 애플리케이션의 느슨한 결합을 도모.

   - 컨트롤의 제어권이 사용자가 아니라 프레임워크에 있어 필요에 따라 스프링에서 사용자의 코드를 호출하기 때문에 제어 역전이라고 표현.

3. DI(Dependency Injection, 의존성 주입)

   - 각각의 계층이나 서비스들 간에 의존성이 존재할 경우 프레임워크가 서로 연결시켜준다.

4. AOP(Aspect-Oriented Programming, 관점 지향)
   - 트랜잭션이나 로깅, 보안과 같이 여러 모듈에서 **공통적으로 사용하는 기능의 경우 해당 기능을 분리**하여 관리할 수 있다.
   - 메서드 안의 주기능과 보조기능을 분리한 후 선택적으로 메서드에 적용해서 사용한다.



---



# 의존성 주입과 제어 역전 기능

의존성 주입을 이용해 클래스를 만드는데 인터페이스를 통해서 만들게된다.

인터페이스에 우리가 만들 메소드들을 만들어 이름이 정해지게 된다.

이렇게 하면 수정이 있을 경우에도 메소드들의 이름이 일정하기 때문에 이 클래스를 호출하는 코드들을 수정할 필요가 없어지게 된다.



### 의존성 주입의 이점

- 클래스들 간의 의존 관계를 최소화하여 코드를 단순화 할 수 있다.
- 애플리케이션을 더 쉽게 유지 및 관리 할 수 있다.
- 기존 구현 방법은 개발자가 직접 코드 안에서 객체의 생성과 소멸을 제어했다. 하지만 의존성 주입은 객체의 생성, 소멸과 객체 간의 의존관계를 컨테이너가 제어한다.



**스프링에서 의존성 주입을 구현하고 외부에서 객체를 생성하려면 XML이나 애너테이션을 이용한다.**



----



### 코드로 알아보기

> 이런 플로우로 xml을 연결하여 사용, 개념을 이해하자

project19

- `PersonTest.java`

```java
package com.spring.ex01;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.xml.XmlBeanFactory;
import org.springframework.core.io.FileSystemResource;

public class PersonTest {
	//DI!!!!!!!! 믓찌네....후 new도 없고
	public static void main(String[] args) {
		//1.xml파일과 연결
        BeanFactory factory = new XmlBeanFactory(new FileSystemResource("person.xml")); 
		//3.이 키값(personService)에 해당하는거 가져와
        PersonService person = (PersonService) factory.getBean("personService");
		//아래 주석 친부분들을 외부xml 파일(bean)에서 다 한다.
        // PersonService person = new PersonServiceImpl();
		//person.setBane("kim") 
		person.sayHello(); //5.impl에 들어간 값 호출!
	}
}
```



- `person.xml`

```xml
 <bean id="personService" class="com.spring.ex01.PersonServiceImpl"> <!-- 2. xml에서 서비스랑 impl이랑 연결하네 -->
      <property name="name">
         <value>홍길동</value> <!--  key/value => name/홍길동로 값 전달 impl에 -->
      </property>
   </bean>
```



- `PersonService.java`

```java
package com.spring.ex01;

public interface PersonService {
	public void sayHello();
}
```



- `PersonServiceImpl.java`

```java
package com.spring.ex01;

public class PersonServiceImpl implements PersonService {
	private String name;
	private int age;

	public void setName(String name) {//4. key/value를 이용해 값을 받는다
		this.name = name;
	}

	@Override
	public void sayHello() { 
		System.out.println("이름: " + name);
		System.out.println("나이: " + age);
	}
}

```




## 스프링 AOP 기능

### AOP

> Aspect Oriented Programing
>
> 관점 지향 프로그래밍

 

프로그래밍을 하다보면, 공통적인 기능이 많이 발생한다.

이러한 공통기능을 모든 모듈에 적용하기 위한 방법으로 상속을 이용한다.

상속도 좋은 방법이지만, JAVA에서는 다중 상속이 불가능하다.

 

이러한 모듈을 상속받아 공통 기능을 부여하기에는 한계가 있다.

그리고, 기능 구현부분에서

핵심코드와 공통기능코드가 섞여있어서

보기에도 불편하고, 효율성이 떨어진다.

 

이러한 이유로 AOP가 등장했다.

AOP방법은 핵심 기능과 공통 기능을 분리 시켜놓고,

공통 기능을 필요로 하는 핵심 기능들에서 사용하는 방식이다.

(핵심기능은 변화하지만, 공통기능은 다시 적용이 가능하다.)

 

즉, AOP는 핵심기능과 공통기능을 분리시킨다는 부분을 주의깊게 보자



---

 

### 주요용어

- Aspect : 공통기능
- Advice : Aspect의 기능 자체
- Jointpoint : Advice를 적용해야 되는 부분(ex : 필드, 메소드 / 스프링에서는 메소드만 해당)
- Pointcut : Jointpoint의 부분으로 실제로 Advice가 적용된 부분
- Weaving : Advice를 핵심기능에 적용하는 행위



스프링에서 AOP 구현방법 : proxy를 이용한다.

```
client(호출부) -> proxy -> Target(핵심기능)
```
- step1. client가 프록시한테 요청해서 공통기능을 실행
- stpe2. 프록시가 다시 Target으로 가서 핵심기능을 실행
- step3. 다시 공통기능을 실행하기 위해, 프록시로 와서 공통기능 실행
(프록시가 중간에서 '대행' 역할을 한다.)



---



### 스프링에서 AOP 구현방식

- 방법1. XML 스키마 기반의 AOP구현
- 방법2. @Aspect 어노테이션 기반의 AOP 구현



출처: https://hongku.tistory.com/114 [IT에 취.하.개.]

