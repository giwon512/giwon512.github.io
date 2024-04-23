---
layout: single
title: 스프링 WebMVC 구조
categories: ['Spring & Spring boot']
tag: [Backend, Java, 자바, 서버, 서블릿, 스프링]
toc: true
---


# 📔 webMVC의 구성요소

![[https://velog.io/@orijoon98/Spring-Framework-Documentation4-Spring-Web-MVC](https://velog.io/@orijoon98/Spring-Framework-Documentation4-Spring-Web-MVC)]({{"../../../../assets/images/Spring&Springboot/MVCstructure/dispatcher.png" | relative_url}})

[https://velog.io/@orijoon98/Spring-Framework-Documentation4-Spring-Web-MVC](https://velog.io/@orijoon98/Spring-Framework-Documentation4-Spring-Web-MVC)

## 1. HandlerMapping

org.springframework.web.servlet.handler

웹페이지로 요청을 보내면 일단 DispatcherServlet으로 이동하는데, 이 프론트 컨트롤러는 어떤 컨트롤러에게 요청을 전달해야 하는지 알지 못한다. 이때, HandlerMapping에 어떤 컨트롤러에게 전달을 해줘야하는지 매핑 정보가 다 저장이 되어있고, HandlerMapping에서 DispatcherServlet에게 어디로 전달을 해야하는지 정보를 리턴해준다. 

### 📘BeanNameUrlHandlerMapping 클래스

원래 빈 태그에서 id 속성을 통해 객체 이름을 정하고, 다른 태그에서 가져다 쓸 수 있게 하였는데, 해당 클래스를 활용하면 빈 태그에서 name 속성을 이용하여 객체 이름을 지정해줄 수 있게 된다. 해당 클래스가 HandlerMapping 객체의 디폴트 값이기 때문에 name 속성에 다음과 같이 적어도 잘 매핑이 되는 것이다.

```xml
<!-- 생략이 되어도 똑같이 동작함 -->
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping" ></bean>
	
<bean name="/" class="controller.IndexController"></bean>
<bean name="/first" class="controller.FirstController"></bean>
<bean name="/second" class="controller.SecondController"></bean>
<bean name="/third" class="controller.ThirdController"></bean>
```

### 📘**SimpleUrlHandlerMapping** 클래스

디폴트 클래스와 비교했을 때, 설정 방법이 다를 뿐 동작은 결국 같다. 두 가지 방식을 같이 사용해서 앞의 handler mapping 방식이 실패했을 때, 뒤의 handler mapping이 동작하게 하는 방법도 가능하다.

```xml
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
	<property name="mappings">
		<props>
			<prop key="/">indexController</prop>
			<prop key="/first">indexController</prop>
			<prop key="/second">indexController</prop>
			<prop key="/third">indexController</prop>
		</props>
	</property>
</bean>

<bean id="indexController" class="controller.IndexController"></bean>
<bean id="firstController" class="controller.FirstController"></bean>
<bean id="secondController" class="controller.SecondController"></bean>
<bean id="thirdController" class="controller.ThirdController"></bean>
```

## 2. ViewResolver

org.springframework.web.servlet.view

DispatcherServlet은 컨트롤러에서 처리한 결과를 리턴받는데, 이 결과를 어떤 뷰에서 처리해야하는지도 알지 못한다. 이때 ViewResolver가 어떤 뷰를 선택해야할지 알려준다.

### 📘InternalResourceViewResolver 클래스

디폴트 값으로 아무 것도 지정하지 않을 시 자동으로 생성되는 클래스로, view resolver의 역할을 알아서 수행해준다.

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="suffix">
		<value>.jsp</value>
	</property>
	<property name="prefix">
		<value>/WEB-INF/view/</value>
	</property>
	<property name="viewClass">
		<value>org.springframework.web.servlet.view.InternalResourceView</value>
	</property>
	<property name="order" value="1"></property>
</bean>
```

뷰 경로의 앞이나 뒤 값을 기본 값으로 붙여주기 때문에 만약 경로가 `“/WEB-INF/view/result.jsp”`라면 경로에 `“result”`만 넘기면 된다. 나중에 파일들의 경로가 바뀌더라도 파일들을 찾아가며 일일이 경로를 바꿔줄 필요 없어 xml 파일에서만 값을 수정해주면 되는 장점이 있다.

뷰의 종류도 여러가지가 있는데, `InternalResourceView`가 디폴트 뷰이다.

```xml
<mvc:view-resolvers>
	<jsp prefix="/WEB-INF/view" />
	<jsp suffix=".jsp" />
</mvc:view-resolvers>
```

mvc schema를 이용하여 더 간단하게 설정할 수도 있다.

### 📘*ResourceBundleViewResolver* 클래스

따로 resources 폴더 안에 .properties 설정 파일을 만들어서 지정해서 이동할 뷰의 경로를 정해줄 수 있다.

```xml
<bean class="org.springframework.web.servlet.view.ResourceBundleViewResolver">
	<property name="basename" value="bundleview"></property>
	<property name="order" value="0"></property>
</bean>
```

HandlerMapping은 자신이 해결하지 못하는 요청에 대해서는 그 다음에 작성되어 있는 다른 HandlerMapping에게 넘기지만, `InternalResourceViewResolver`는 자신이 해결하지 못하는 경로에 대해서 다음 ViewResolver에 넘기는 것이 아니라 바로 오류를 띄워버리기 때문에 가장 마지막 순서가 되어야 한다. 따라서 setOrder 프로퍼티를 통해서 마지막 순서로 지정해주는 것이 좋다.

📄 **bundleview.properties**

```
# bundleview.properties

second.(class)=org.springframework.web.servlet.view.InternalResourceView
second.url=/WEB-INF/view/result.jsp

third.(class)=org.springframework.web.servlet.view.InternalResourceView
third.url=/WEB-INF/view/result.jsp
```

이렇게 설정 파일을 작성해주면, 뷰의 경로를 단순히 second, third로 지정해주면 된다.

## 3. View

# ⚙️ 프론트 컨트롤러 설정 파일 경로와 이름 바꾸기

원래 설정을 위한 xml 파일과 web.xml이 같은 위치에 있어야 하고, 이름도 {servlet-name}-servlet.xml의 형식으로 되어야 제대로 설정이 적용된다. 이를 따르지 않고, xml 파일의 위치와 이름을 원하는 대로 바꿀 수 있다.

### web.xml

```xml
<servlet>
	<servlet-name>app1</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
  	<param-name>contextConfigLocation</param-name>
  	<param-value>/WEB-INF/spring/bean-config.xml</param-value>
	</init-param>
</servlet>
```

`param-value` 태그에 경로와 파일 이름을 지정하면, DispatcherServlet의 설정 파일로 지정할 수 있다.

# 📖 MVC schema를 이용해서 컨트롤러 생성하기

단순히 페이지를 보여주기 위한 경로만을 전달하는 컨트롤러는 단순히 mvc 스키마를 통해서 컨트롤러를 간단하게 생성할 수 있다.

```xml
<mvc:view-controller path="/" view-name="/WEB-INF/view/index.jsp"/>
```

이 한 줄이면, 따로 클래스를 만들어서 어노테이션을 붙일 필요도, xml 파일에서 빈 태그를 사용할 필요도 없다.