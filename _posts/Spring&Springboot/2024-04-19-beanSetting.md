---
layout: single
title: 스프링 DI 설정하기
categories: ['Spring & Spring boot']
tag: [Backend, Java, 자바, 서버, 서블릿, 스프링]
toc: true
---


스프링 DI 패턴에 사용되는 Bean 객체의 값을 설정하는 방법들을 알아보자.

# 📘 XML

### <constructor-arg> 태그

해당 태그를 통해 빈 객체의 생성자에 인자 값을 넘겨줄 수 있다.

### <property> 태그

빈 객체는 의존성 주입을 위한 객체로, 값을 가져오는 getter 메서드는 고려하지 않기 때문에 jsp의 setProperty 태그와 비슷하게 동작한다.

# 📘 자동 와이어링 방식

참조 값에만 적용이 가능한 방식이다.

## 📖 XML 활용

요즘은 잘 활용되지 않는 방식이다.

### 1. byName 방식

```xml
<bean id="bean" class="basic08.MessageBeanImpl" autowire="byName">
	<property name="name" value="임꺽정" />
	<property name="age" value="30" />
	<!-- <property name="outputter" ref="outputter" /> -->
</bean>

<bean id="outputter" class="basic08.FileOutputter">
	<property name="filePath" value="src/main/java/basic08/result.txt" />
</bean>
```

basic08.MessageBeanImpl 클래스에서 setOutputter에 해당하는 outputter라는 이름을 가진 참조값을 찾아 자동으로 연결해주는 방식이다.

### 2. byType 방식

```xml
<bean id="bean" class="basic08.MessageBeanImpl" autowire="byType">
	<property name="name" value="임꺽정" />
	<property name="age" value="30" />
	<!-- <property name="outputter" ref="outputter" /> -->
</bean>

<bean id="outputter" class="basic08.FileOutputter">
	<property name="filePath" value="src/main/java/basic08/result.txt" />
</bean>
```

basic08.MessageBeanImpl 클래스에서 setOutputter의 파라미터 타입과 일치하는 빈 객체를 찾아서 자동으로 연결해주는 방식이다. 빈 객체의 아이디 값을 항상 프로퍼티 명과 일치시켜 주기는 어려운데, 그때 사용하기 좋은 방식이다.

## 📖 Annotation 활용

### @Resource

xml 빈 태그의 autowire=”byName”과 같은 동작을 하는 어노테이션이다. 현재 잘 활용되지 않는다.

### @Autowired

xml 빈 태그의 autowire=”byType”과 같은 동작을 하는 어노테이션이다.

```java
@Autowired
public void setOutputter(FileOutputter outputter) {
	this.outputter = outputter;
}

```

`AnnotationConfigApplicationContext`가 아닌 `ClassPathXmlApplicationContext`를 사용하는 경우 까먹지 말고 xml에 `<context:component-scan base-package="basic08"></context:component-scan>` 를 추가해주어야한다.