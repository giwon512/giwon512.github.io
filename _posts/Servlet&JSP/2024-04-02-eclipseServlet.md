---
layout: single
title: 이클립스에서 서블릿 실행하기
categories: ['Servlet & JSP']
tag: [Backend, Java, 자바, 서버, 서블릿]
toc: true
---


# ⚙️ 이클립스 톰캣 연결하기

1. [Window] → [Show View] → [Servers] 열기
2. 새로운 서버 추가하기 → [Apache] → 원하는 톰캣 버전 선택
3. 톰캣이 설치되어 있는 폴더 지정 후 [Finish]
4. 화면을 보고 싶은 파일 위치에서 ctrl + F11을 누르면 바로 그 페이지를 확인 가능

# 📖 서블릿 예제

index.html에서 폼을 통해 페이지의 쿠키와 세션 값을 저장하고, 확인하는 예제이다.

### 📄 index.html

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Insert title here</title>
	</head>
	<body>
		<form action="/ServletTest2/cook" method="POST">
			이름 : <input type="text" name="name" /><br><br>
			주소 : <input type="text" name="addr" /><br><br>
			<button>전송</button>
		</form>
		
		<br><br>
		<a href="/ServletTest2/cookConfirm">쿠키 확인하기</a>
		<hr>
		<h2> 세션 사용하기 </h2>
		<form action="/ServletTest2/sess" method="POST">
			이름 : <input type="text" name="name" /><br><br>
			주소 : <input type="text" name="addr" /><br><br>
			<button>전송</button>
		</form>
		
		<br><br>
		<a href="/ServletTest2/sessConfirm">세션 확인하기</a>
	</body>
</html>
```

이름과 주소 값을 각각 받아서 각각 쿠키와 세션 값을 저장하는 서블릿과 그 값을 확인하는 서블릿을 만들 것이다. 즉, 총 4개의 서블릿을 만든다. 위의 파일을 실행하면 다음과 같은 화면이 나타난다.

![index.png]({{"../../../../assets/images/servlet/eclipseServlet/index.png" | relative_url}})

### 📄 CookieServlet.java

```java
@WebServlet("/cook")
public class CookieServlet extends HttpServlet{
	
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		resp.setCharacterEncoding("utf-8");
		resp.setContentType("text/html;charset=UTF-8");
		
		String name = req.getParameter("name");
		String addr = req.getParameter("addr");
		
		Cookie cookName = new Cookie("name", name);
		Cookie cookAddr = new Cookie("addr", addr);
		
		cookName.setMaxAge(60 * 60);
		cookAddr.setMaxAge(60 * 60);
		
		resp.addCookie(cookName);
		resp.addCookie(cookAddr);
		
		PrintWriter out = resp.getWriter();
		
		out.println("<html><body>");
		out.println("쿠키가 잘 저장되었습니다.");
		out.println("</body></html>");
		out.close();
	}
	
}
```

- @WebServlet(”/cook”)
    
    해당 어노테이션을 사용해주면 굳이 번거롭게 web.xml에 서블릿 매핑을 해주지 않아도 바로 경로를 지정해줄 수 있다.
    
- 고민할 것 없이 get 방식과 post 방식을 모두 오버라이딩 해준다.
    
    두 함수 모두 같은 동작을 수행할 것이기 때문에 get메서드는 그대로 doPost를 호출하도록 했다.
    
- Cookie.setMaxAge(int second);
    
    쿠키의 저장 시간을 정하는 함수로, 초 단위이기 때문에 하루의 기간 동안 저장할 거라면 60 * 60 * 24를 해주면 된다.
    

### 📄 CookieConfirmServlet.java

```java
@WebServlet("/cookConfirm")
public class CookieConfirmServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		resp.setCharacterEncoding("utf-8");
		resp.setContentType("text/html;charset=utf-8");
		Cookie[] cooks = req.getCookies();
		
		PrintWriter out = resp.getWriter();
		
		out.println("<html><body>");
		out.println("쿠키 정보는 다음과 같습니다.<br>");
		for(Cookie c : cooks) {
			out.println(c.getName() + ", " + c.getValue() + "<br>");
		}
		
		out.println("</body></html>");
		out.close();
	}
	
}
```

- Cookie.getCookies()
    
    쿠키 값은 따로 접근할 수 없고, 해당 함수를 통해 배열로 불러와 활용해야 한다.
    
    ![cookie.png]({{"../../../../assets/images/servlet/eclipseServlet/cookie.png" | relative_url}})
    
    쿠키 정보를 입력하고 쿠키 확인하기 링크를 눌렀을 때의 페이지이다.
    

### 📄 SessionServlet.java

```java
@WebServlet("/sess")
public class SessionServlet extends HttpServlet{

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		resp.setCharacterEncoding("utf-8");
		resp.setContentType("text/html;charset=UTF-8");
		
		String name = req.getParameter("name");
		String addr = req.getParameter("addr");
		
		if(name == null) {
			
		}
		
		HttpSession session = req.getSession();
		session.setAttribute("name", name);
		session.setAttribute("addr", addr);
		
		PrintWriter out = resp.getWriter();
		
		out.println("<html><body>");
		out.println(session.getAttribute("name") + "님 환영합니다.");
		out.println("</body></html>");
		out.close();
	}
	
}
```

- 쿠키 값을 저장하는 코드와 크게 다를 게 없다.

### 📄 SessionConfirmServlet.java

```java
@WebServlet("/sessConfirm")
public class SessionConfirmServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		resp.setCharacterEncoding("utf-8");
		resp.setContentType("text/html;charset=utf-8");
		
		HttpSession session = req.getSession();
		
		PrintWriter out = resp.getWriter();
		
		out.println("<html><body>");
		out.println("name : " + session.getAttribute("name") + "<br>");
		out.println("address : " + session.getAttribute("addr") + "<br>");
		out.println("Connection time : " + new Date(session.getCreationTime()) + "<br>");
		out.println("Expired Time : " + session.getMaxInactiveInterval() + "<br>");
		out.println("New : " + session.isNew() + "<br>");
		
		out.println("</body></html>");
		out.close();
	}
	
}
```

- 쿠키와 다르게 설정해놓은 값들에 바로 접근할 수 있고, 이외에도 세션에 대한 정보들을 얻을 수 있다.
- Session.getMaxInactiveInterval()
    
    세션의 간격을 반환한다.
    
- Session.isNew()
    
    세션 객체가 서버에서 새로 생성된 것인지 확인한다.
    
    ![session.png]({{"../../../../assets/images/servlet/eclipseServlet/session.png" | relative_url}})
    
    세션 정보를 입력하고 세션 확인하기 링크를 눌렀을 때의 페이지이다.