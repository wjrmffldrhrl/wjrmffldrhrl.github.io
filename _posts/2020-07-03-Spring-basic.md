---
title:  "[Spring]Spring 개념과 의존성 주입 실습"
categories:
  - Spring
tags:
  - spring
  - spring-boot
  - java
header:
  teaser: /assets/images/spring_title.png
  image: /assets/images/spring_title.png
---  
스프링의 개념과 간단한 의존성 주입을 해보자

# Spring Framework란?

자바 플랫폼을 위한 오픈소스 애플리케이션 프레임워크로서 스프링(Spring)이라고 불린다.

쉽게 말해 자바를 사용하는 환경에서 애플리케이션을 개발하기 위한 틀이라고 생각하면 된다.

# Spring Framework 구조

## POJO(Plain Old Java Object) Programming

복잡하고 제한이 많은 기술을 사용하는 것이 아닌 순수한 자바 객체만을 사용한 프로그래밍을 필요로 한다.

![pojo]({{ site.baseurl }}/assets/images/spring1/POJO.png)

Spring에서 이러한 방식으로 생성된 객체, 즉 POJO를 Beans라고 부른다.

## IoC (Inversion of Control)

제어의 역전

개발자가 직접 객체를 언제 생성하고 없앨지 결정하는 것이 아닌 IoC Container에게 맡긴다는 뜻이다.

POJO 객체의 생성에서 생명주기의 관리까지를 IoC Container에게 담당시킴으로써 개발에 있어서 편의성과 재사용성의 극대화를 추구하는 개념이다.

![di]({{ site.baseurl }}/assets/images/spring1/DI.png)

IoC 구현 방법에는 DL, DI 두가지가 있다.

### DL (Dependency Lookup)

의존성 검색

컨테이너에서 제공하는 API를 이용해 사용하고자 하는 Bean을 저장소에서 Lookup하는 것을 말한다.

### DI (Dependency Injection)

의존성 주입

각 객체간의 의존성을 컨테이너가 자동으로 연결해주는 것으로 개발자가 Bean 설정파일에 의존관계가 필요한 정보를 추가해주면 컨테이너가 자동적으로 연결해준다. 

Setter Injection, Constructor Injection(생성자 주입)의 2가지 방법이 있다.

⭐ 오늘 실습할 핵심 내용 ⭐

```java
// Setter Injection
public class BeanExample {
 
    private MyBean beanOne;
   
    public void setBeanOne(MyBean beanOne){
        this.beanOne = beanOne;
    }
}
```

```java
// Constructor injection
public class BeanExample {
 
    private int beanOne;
    
    public BeanExample(int beanOne){
        this.beanOne = beanOne;
    }
}
```
# Training

Spring에서의 Bean의 생성과 의존성 주입의 과정을 실습해보자.

![dir]({{ site.baseurl }}/assets/images/spring1/dir.png)

# DTO 생성

## Score.java

```java
package spring.day0625.quiz;

public class Score {

	// 맴버 변수 3개
	// 디폴트 생성자
	// 3과목 모두 인자로 받는 생성자 
	// 각각의 setter getter 생성
	private int java;
	private int jsp;
	private int spring;
	
	public Score() {
		
	}

	public Score(int java, int jsp, int spring) {
		this.java = java;
		this.jsp = jsp;
		this.spring = spring;
	}
	public int getJava() {
		return java;
	}
	public void setJava(int java) {
		this.java = java;
	}
	public int getJsp() {
		return jsp;
	}
	public void setJsp(int jsp) {
		this.jsp = jsp;
	}
	public int getSpring() {
		return spring;
	}
	public void setSpring(int spring) {
		this.spring = spring;
	}
	
	
}
```

## Info.java

```java
package spring.day0625.quiz;

public class Info {

	// 멤버변수 이름,나이
	// 디폴트 생성자
	// 이름, 나이를 인자로 받는 생성자 만들기
	// setter getter
	
	private String name;
	private int age;
	
	public Info() {
		
	}
	
	public Info(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	
	
}
```

## MyData.java

```java
package spring.day0625.quiz;

public class MyData {

	// Score, Info Class 맴버 변수로 선언 
	// Score, Info를 각각 주입받는 생성자.
	// 출력 메서드 writeData 생성
	// 이름, jsp, Spring 출력
	
  // 위에서 생성한 DTO를 주입받는다. 
	private Score score;
	private Info info;
	
	
	public MyData() {
		
	}
	
	public MyData(Score score, Info info) {
		this.score = score;
		this.info = info;
	}

	public Score getScore() {
		return score;
	}

	public void setScore(Score score) {
		this.score = score;
	}

	public Info getInfo() {
		return info;
	}

	public void setInfo(Info info) {
		this.info = info;
	}
	
	public void writeData() {
		System.out.println("name : " + this.info.getName());
		System.out.println("age : " + this.info.getAge());
		System.out.println("java : " + this.score.getJava());
		System.out.println("jsp : " + this.score.getJsp());
		System.out.println("spring : " + this.score.getSpring());
	}
}
```

# DI (Dependency Injection)

Bean을 생성해서 의존성 주입을 진행한다.

## AppContextQuiz.xml

### Bean 생성

Bean을 생성할 때  `<bean>` 태그를 사용하며 이름과 해당 클래스를 지정해주면 된다.

```xml
<bean name="score1" class="spring.day0625.quiz.Score"></bean>
```

이때 정한 이름은 다른 Bean에서 사용하거나  ApplicationContext를 이용해서 class 내부로 불러올 수 있다.

```xml
<!-- Bean에서 Bean 사용 -->
<bean name="myData1" class="spring.day0625.quiz.MyData">
		<!-- 생성된 bean을 사용하고 싶을때는 ref를 사용 -->
		<!-- ref의 값에 생성한 bean의 name 값을 넣는다 -->
		<constructor-arg ref="score1"/> 
		<constructor-arg ref="info1"/>
	</bean>
```

```java
// Class에서의 사용
public class MyDataMain {
	public static void main(String[] args) {
		
		ApplicationContext app = 
				new ClassPathXmlApplicationContext("spring/test2/appContextQuiz.xml");
		
		// xml파일에서 생성한 bean을 name값으로 호출한다.
		MyData mydata = app.getBean("myData1",MyData.class);
		
		mydata.writeData();
	}
}
```

### 생성자를 이용한 주입

```xml
<!-- 생성자 주입   -->
	<bean name="score1" class="spring.day0625.quiz.Score">
		<!-- 값을 넣으면 순서대로 생성자에 주입된다. -->
		<constructor-arg value="30"/>
		<constructor-arg value="40"/>
		<constructor-arg value="50"/>
	</bean>
	
	<bean name="info1" class="spring.day0625.quiz.Info">
		<constructor-arg value="jsh"/>
		<constructor-arg value="25"/>
	</bean>
	
	<bean name="myData1" class="spring.day0625.quiz.MyData">
		<!-- 생성된 bean을 사용하고 싶을때는 ref를 사용 -->
		<constructor-arg ref="score1"/>
		<constructor-arg ref="info1"/>
	</bean>
```

`<constructor-arg />`태그를 사용하면 생성자 주입을 할 수 있다. 

이때 DTO에서 만든 생성자를 통해 의존성 주입이 이루어진다.

```java
	<bean name="info1" class="spring.day0625.quiz.Info">
		<constructor-arg value="jsh"/>
		<constructor-arg value="25"/>
	</bean>
```

```java
public class Info {

	private String name;
	private int age;
	
	public Info() {
		
	}
	
	// 해당 생성자로 값이 전달된다.
	// 값은 value에 전달한 순서대로 주입이된다.
	public Info(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	// setter getter 생략
	// ...
}
```

### Setter를 이용한 주입

```xml
<!-- Setter 주입 -->
	<bean name="score2" class="spring.day0625.quiz.Score">
		<property name="java" value="80"/>
		<property name="jsp" value="90"/>
		<property name="spring" value="100"/>
	</bean>
	<bean name="info2" class="spring.day0625.quiz.Info">
		<property name="name" value="hsj"/>
		<property name="age" value="52"/>
	</bean>
	<bean name="myData2" class="spring.day0625.quiz.MyData">
		<property name="score" ref="score2"/>
		<property name="info" ref="info2"/>
	</bean>
```

`<property>` 태그를 사용하면 DTO에서 선언한 setter함수를 통해 의존성 주입을 할 수 있다.

```java
public class Score {

	private int java;
	private int jsp;
	private int spring;
	
	// property 태그의 name 값에 해당하는 
  // 변수의 setter를 이용해 의존성 주입이 이루어진다.
	public void setJava(int java) {
		this.java = java;
	}
	public void setJsp(int jsp) {
		this.jsp = jsp;
	}
	public void setSpring(int spring) {
		this.spring = spring;
	}

	// getter, 생성자 생략
	// ...
	
}
```

해당 xml을 통해 의존성 주입이 성공적으로 이루어졌다면  MyData Bean은 다음과 같은 구조를 가질 것이다.

![data]({{ site.baseurl }}/assets/images/spring1/data.png)

# 실행

Main class에서 Bean을 호출하여 실행시켜보자

```java
package spring.day0625.quiz;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyDataMain {

	public static void main(String[] args) {
		
	//  ApplicationContext 객체 생성시에 Context파일의 위치를 입력해줘야 한다.
		ApplicationContext app =
				new ClassPathXmlApplicationContext("spring/test2/appContextQuiz.xml");
		
	// 생성한 Bean의 이름을 넣는다
	// <bean name="myData2" class="spring.day0625.quiz.MyData"> 에서의 name 값이다.
		MyData mydata1 = app.getBean("myData1",MyData.class);
		
		mydata1.writeData();
		
		MyData mydata2 = app.getBean("myData2",MyData.class);
		
		mydata2.writeData();
	}

}
```

### 결과

```java
name : jsh
age : 25
java : 30
jsp : 40
spring : 50
name : hsj
age : 52
java : 80
jsp : 90
spring : 100
```
# Annotation @

Bean을 생성하고 의존성을 주입하는 일련의 과정을 축약할 수 있는 기능이다.

# @Component

XML 설정 없이 자동으로 Bean을 등록한다.

```java
@Component // @Component("myDAO")
// <bean id="myDAO" 
// class="spring.day0625.annotation.MyDAO"/>
public class MyDAO implements DaoInter {

}
```

# @Autowire

자동으로 빈을 찾아서 주입 (타입으로 찾는다.)

```java
@Autowired
	// <constructor-arg ref="dao"/>
	MyDAO dao;
```

# @Resource(name="testBean")

여러개의 bean이 있어서 겹칠 가능성이 있을 때 정확하게 가리키기 위해 사용한다.

```java
//@Autowired  //자동주입
	@Resource(name = "myDao")
	MyDao dao;
```

# Context.xml

```xml
<!-- annotation을 사용하기 때문에 주석처리 -->
<!-- 	<bean id="dao" class="spring.day0625.annotation.MyDAO"/>
	<bean id="logic" class="spring.day0625.annotation.LogicController">
		<constructor-arg ref="dao"/>
	</bean> -->

<!-- context 등록 해야함 -->
<context:annotation-config/>
	
<!-- scan package 지정  wildcard can use-->
<context:component-scan base-package="spring.day0625.annotation"/>

```

# Reference

> [https://12bme.tistory.com/157](https://12bme.tistory.com/157)

> [https://gangnam-americano.tistory.com/60](https://gangnam-americano.tistory.com/60)

> [https://gangnam-americano.tistory.com/60](https://gangnam-americano.tistory.com/60)
