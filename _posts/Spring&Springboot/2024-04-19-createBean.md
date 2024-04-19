---
layout: single
title: 스프링 빈 객체 생성 방법
categories: ['Spring & Spring boot']
tag: [Backend, Java, 자바, 서버, 서블릿, 스프링]
toc: true
---


스프링의 모든 객체는 Bean 객체로 다루어지며, 이 객체를 생성하는 방법은 여러가지가 있다.

# 📖 XML을 통한 Bean 객체 생성

resource 폴더 내의 config.xml에서 `bean` 태그 또는 `context`나 `aop`와 같은 전용 태그를 통해서 객체를 생성하는 방법이다.

# 📖 Annotation을 이용한 Bean 객체 생성

어노테이션을 통해 객체를 자동 인식하게 하는 방법이다.

### 빈 스캐닝(Bean scanning)

특정 어노테이션이 붙은 클래스를 자동으로 찾아서 bean 객체로 생성하여 등록해주는 방식

### 📄ApplicationContext Interface

- 스프링 컨테이너로, BeanFactory 인터페이스의 하위 인터페이스이다.
- ClassPathXmlApplicationContext 클래스는 이 인터페이스를 구현하고, xml로 생성된 객체를 조회할 때 사용된다.
- AnnotationConfigApplicationContext 클래스 또한 이 인터페이스를 구현하고, 어노테이션을 통해 생성된 객체를 조회할 때 사용된다. 범위를 지정해주지 않으면 프로젝트 내의 모든 범위에서 어노테이션을 찾는다.

### 📄 Code(AnnotationConfigApplicationContext 사용)

```java
//MessageBeanKr.java
@Component
public class MessageBeanKr implements MessageBean{
....

//MessageBeanEn.java
@Component("msgEn")
public class MessageBeanEn implements MessageBean{
....

//App.java
public class App 
{
	private static ApplicationContext ctx;
    public static void main( String[] args )
    {
        //ctx = new ClassPathXmlApplicationContext("config/basic06_config.xml");
	    	ctx = new AnnotationConfigApplicationContext("basic06");
    	
        MessageBean bean = ctx.getBean("messageBeanKr", basic06.MessageBeanKr.class);
        bean.sayHello();
        
        bean = ctx.getBean("msgEn", MessageBeanEn.class);
        bean.sayHello();
    }
}
```

- 따로 객체 이름을 지정해주지 않은 경우, 객체 이름은 클래스 이름(첫 글자는 소문자로 변함)이 된다.
- 더 이상 xml 파일은 필요하지 않다.

### 📄 Code(ClassPathXmlApplicationContext 사용)

파일을 수정하기가 곤란한 상황이라 AnnotationConfigApplicationContext 클래스를 사용하지 못하는 경우에는 xml 파일에 다음과 같이 컴포넌트 스캐너를 등록해주어도 똑같이 동작한다.

```xml
//config.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="basic06"></context:component-scan>

</beans>
```

# 📖 Java 코드를 통한 Bean 객체 생성

@Configuration 어노테이션을 통해서 설정을 할 수 있는 클래스를 선언할 수 있다.

### ApplicationContextConfigure.java

```java
@Configuration
public class ApplicationContextConfigure {
	@Bean
	public MessageBean getMessageKr() {
		return new MessageBeanKr("steve", "25");
	}
	
	@Bean
	public MessageBean getMessageEn() {
		MessageBeanEn en = new MessageBeanEn();
		en.setName("john");
		en.setAge("30");
		return en;
	}
}
```

@Bean 어노테이션으로 넘겨줄 객체를 반환하는 getter 메서드를 지정해주면 된다.

Consumer class에서 `ctx.getBean("getMessageKr", MessageBean.class)` 와 같이 메서드 이름으로 바로 접근해주면 된다.