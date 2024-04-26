---
layout: single
title: 스프링 부트 시작하기
categories: ['Spring & Spring boot']
tag: [Backend, Java, 자바, 서버, 서블릿, 스프링, 스프링부트]
toc: true
---


# ⚙️ 이클립스에서 프로젝트 만들기

Spring Tool Suite 4 IDE에서 진행했다.

1. [File] → [New] → [Spring Starter Project]
2. 이름과 패키지 이름을 정해주고 나머지 부분은 크게 건들게 없다.
    
    ![bootStart.png]({{"../../../../assets/images/Spring&Springboot/springBoot/bootStart.png" | relative_url}})
    
3. 스프링 부트 버전은 3.2.5로 했고, 추가한 Dependency는 다음과 같다.
    - Spring Web
    - Spring Boot DevTools
    - Thymeleaf

이러면 알아서 프로젝트가 생성되고, 기존의 스프링과는 다르게 번거롭게 더 이상 만들어 주어야 하는 설정 파일은 없다.

# 📔 프로젝트 실행하기

프로젝트를 만들면 src/main/resources 폴더에 기본적으로 `templates`, `static` 폴더와 [`application.properties`](http://application.properties) 파일이 만들어져 있고, `static` 폴더 밑의 `index.html`이 `localhost:8080/`경로에서 보여지는 기본 페이지이다.

[Run As]→[Spring Boot App]을 통해 서버를 돌리고 페이지를 확인할 수 있다.

### 📄application.properties

```xml
spring.application.name=BootExam5

# 타임리프 설정
spring.thymeleaf.prefix=classpath:templates/
spring.thymeleaf.suffix=.html
```

스프링 부트의 설정 파일로, 이런 식으로 값을 설정하면 경로를 입력할 때 생략할 수 있다. 위의 설정은 default 값이기 때문에, 따로 적어주지 않아도 똑같이 동작한다.

### 📁src/main/resources/📄templates/basic/text-basic.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>컨텐츠 데이터 출력하기</h1>
	
	<h3>변수 빈 값 체크하기</h3><br>
	<div th:with="firstVal=${'대한민국'}">
		<span th:text="${firstVal}"></span>
	</div>
	<br><br>
	<div th:with="firstVal=${''}, secondVal=${'bbb'}">
		<span th:text="${firstVal == '' ? secondVal : firstVal}"></span>
	</div>
	<hr>
	
	<h3>Model 또는 ModelAndView를 이용해서 데이터 전달</h3>
	<span th:text="${data1}"></span><br>
	<span th:text="'안녕하세요' + ${data1}"></span><br>
</body>
</html>
```

타임리프 문법을 이용해 페이지를 만들어보았다.

### 📁src/main/java/📄com.mysite.controller.BasicController.java

```java
@Controller
@RequestMapping("/basic")
public class BasicController {
	@GetMapping("/text-basic")
	public ModelAndView textBasic() {
		String data1 = "Hello SpringBoot";
		return new ModelAndView("basic/text-basic").addObject("data1", data1);
	}
}

```

따로 어노테이션을 스캔해 줄 태그를 작성해주지 않아도, 프로젝트를 만들 때 추가한 Thymeleaf Dependency를 통해 알아서 서블릿이 호출된다.

### 📁src/main/resources/📁static/📄index.html

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>Thymeleaf 문법 가이드</h1>
	<ul>
		<li><a href="/basic/text-basic">텍스트</a></li>
	</ul>
</body>
</html>
```

이런 식으로 간단하게 컨트롤러를 통해 페이지를 이동하는 프로젝트를 만들어보았는데, 얼마 사용해보진 않았지만 스프링과 비교해서 벌써 너무 편하다…