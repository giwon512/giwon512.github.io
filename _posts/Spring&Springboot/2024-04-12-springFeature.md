---
layout: single
title: 스프링 프레임워크의 주요 특징
categories: ['Spring & Spring boot']
tag: [Backend, Java, 자바, 서버, 서블릿, 스프링]
toc: true
---


[mvnrespository.com](http://mvnrespository.com)에서 spring 키워드로 검색을 하면, 스프링에 필요한 수많은 라이브러리들이 존재하는 것을 확인할 수 있다.

그 중에서 spring context 5.3.33 버전 라이브러리를 활용해보자

### spring 라이브러리 추가하기

pom.xml에 다음 내용을 추가만 해주면 된다.

```java
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.3.33</version>
</dependency>

```

# 📖Factory 패턴 spring으로 구현하기

기존의 자바 프로그램은 팩토리 패턴을 다음과 같이 구현했다.

### MessageBeanFactory.java

```java
public class MessageBeanFactory {
	private static MessageBeanFactory factory = new MessageBeanFactory();
	
	private MessageBeanFactory() {}
	
	public static MessageBeanFactory instanceOf() {
		return factory; 
	}
	
	public MessageBean createMessage(String nation) {
		if(nation.equals("kr")) {
			return new MessageBeanKr();
		}
		else {
			return new MessageBeanEn();
		}
	}
}
```

### MessageBean.java

```java
public interface MessageBean {
	public void sayHello(String name);
}
```

### MessageBeanKr.java

```java
public class MessageBeanKr implements MessageBean{
	public void sayHello(String name) {
		System.out.println("안녕, " + name + "!!");
	}
}
```

### MessageBeanKr.java

```java
public class MessageBeanEn implements MessageBean{
	public void sayHello(String name) {
		System.out.println("Hello, " + name + "!!");
	}
}
```

### App.java

```java
public class App 
{
    public static void main( String[] args )
    {
        MessageBeanFactory factory = MessageBeanFactory.instanceOf();
        
        MessageBean bean = factory.createMessage("kr");
        bean.sayHello("jiwon");
        
        bean = factory.createMessage("null");
        bean.sayHello("steve");
        //안녕, jiwon!!
        //hello, steve!!
    }
}

```

다형성을 위해서 MessageBean 인터페이스를 상속받는 두 클래스가 있고, 팩토리 패턴을 적용해서 메인 함수 안에서는 생성자를 호출하지 않는 예제이다. 이를 spring에서는 어떻게 구현하는지 바꿔보자.

## 📝 스프링 설정 파일 추가하기

[https://docs.spring.io/spring-framework/docs/5.3.30/reference/html/core.html#spring-core](https://docs.spring.io/spring-framework/docs/5.3.30/reference/html/core.html#spring-core)

해당 페이지에서 설정 파일에 필요한 코드를 확인할 수 있다.

### config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
       
	<!-- MessageBean msgKr = new MessageBeanKr(); -->    
	<bean id="msgKr" name="kr, mskr" class="basic04.MessageBeanKr"></bean>
	
	<bean id="msgEn" class="basic04.MessageBeanEn"></bean>
</beans>
```

이 설정을 가지고 하나의 거대한 스프링이라는 메모리를 가지는 객체를 생성한다. 이 공간 자체가 팩토리가 된다. 스프링에서는 모든 객체가 빈으로 다뤄진다.

### App.java

```java
public class App 
{
	private static ApplicationContext ctx;
    public static void main( String[] args )
    {
    	//resources라는 폴더는 classpath에 이미 지정되어 있다.
        ctx = new ClassPathXmlApplicationContext("config/basic04_config.xml");
    	
        MessageBean bean = ctx.getBean("msgKr", basic04.MessageBeanKr.class);
        bean.sayHello("jiwon");
        
        bean = ctx.getBean("msgEn", basic04.MessageBeanEn.class);
        bean.sayHello("steve");      
        
        bean = ctx.getBean("kr", basic04.MessageBeanKr.class);
        bean.sayHello("steve");
        
        //안녕, jiwon!!
        //Hello, steve!!
        //안녕, steve!!  
    }
}
```

bean 태그에서 name 속성을 통해 별명을 지정해주면 getBean함수에서 id값을 지정해 준 다른 별명 값으로도 넘겨줄 수 있다.

# 📖 Spring 프레임워크의 주요 특징

## 📄IoC(Inversion of Control)

> ‘제어의 역행’을 의미한다. main함수와 같이 프로그램이 시작되는 곳에서 필요한 객체를 생성하고, 해당 객체의 메서드를 호출하는 것이 기존의 프로그램이 갖는 방식이다. 반대로 IoC는 프로그램 제어를 다른 곳에서 하게 된다. 즉, 스프링 컨테이너에서 객체를 생성하고, 공급하는 형태이다.
> 

→ 위의 코드에서 봐도 알 수 있듯이, xml 기반의 설정 파일에서 스프링 빈을 통해 객체를 관리할 수 있고, annotation을 통해 관련 설정을 대체할 수 있다.

## 📄DI(Dependency Injection)

> ‘의존성 주입’을 의미한다. 클래스 간의 의존관계를 Bean 설정에 기반해서 컨테이너가 자동으로 연결해주는 방식이다.
> 

→ 순수 자바 코드로 구현하는 경우 필요한 객체를 외부에서 초기화하고, 클래스 내부에서는 제공받은 인스턴스를 사용하기만 하면 된다. 이 또한 annotation을 통해 DI를 편하게 구현할 수 있다.

## 📄AOP(Aspect-Oriented Programming)

> ‘관점 지향 프로그래밍’을 의미한다. 횡단 관심사(여러 프로그램에 흩어져 있는 공통 기능)를 분리해서 모듈성을 증가시키는 것이 목적이다.
> 

→ 자세한 내용은 예시와 함께 정리할 예정이다.