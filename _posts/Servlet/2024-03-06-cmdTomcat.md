---
layout: single
title: Tomcat으로 cmd창에서 웹프로젝트 실행하기
categories: Java
tag: [Backend, Java, 자바]
toc: true
---


# ⚙️ 톰캣 초기 설정하기

📘 **톰캣 설치하기**

[https://tomcat.apache.org/](https://tomcat.apache.org/)

📘 **환경변수 설정하기**

cmd 창에서 톰캣을 실행하기 위한 방법들을 먼저 소개한다.

1. 어디에서나 톰캣을 실행할 수 있도록 Path 변수에 bin 폴더의 위치를 추가해준다. 이를 통해서 아무데서나 startup 명령어를 통해 톰캣을 실행할 수 있다.
    
    ex) C:\Users\BackEnd\tomcat9\bin
    
2. CATALINA_HOME이라는 시스템 변수를 새로 추가해서, 톰캣 폴더가 설치된 경로를 추가해준다.
    
    ex) C:\Users\BackEnd\tomcat9
    
3. startup 명령어를 통해 서버를 실행시킨 상태에서, 크롬 브라우저를 열고 127.0.0.1:8080 또는 localhost:8080으로 접속해서 제대로 접속 되는지 확인한다. 
    
    ⚠️8080를 포트 번호로 사용하는 프로그램이 여럿 깔려 있다면 뜬금없는 사이트가 뜰 수 있다. 나의 경우에는 오라클이 이미 깔려 있어 오라클로 접속이 되어, 오라클의 포트 번호를 바꿔주었다.
    
    **오라클 포트 번호 바꾸기**
    
    `EXEC DBMS_XDB.SETHTTPPORT(9090);` 
    
    `SELECT DBMS_XDB.GETHTTPPORT() FROM DUAL;` 
    
    9090으로 포트 번호가 바뀌었음이 확인되고, 이제는 톰캣의 페이지로 잘 이동이 되는 것을 확인했다.
    
4. servlet 객체를 만들기 위해서는 관련 라이브러리가 필요한데, 설치한 톰캣 폴더 밑 lib 폴더에 이미 설치가 되어있다. 이것을 classpath 변수에 추가해준다.
    
    ex) C:\Users\BackEnd\tomcat9\lib\servlet-api.jar
    
    만약 이렇게 환경 변수를 추가해주었는데도 서블릿 클래스를 상속한 자바 파일이 컴파일 되지 않는다면, cmd 창에서 직접 추가해주어도 된다.
    
    `set classpath=C:\Users\acorn\BackEnd\tomcat9\lib\servlet-api.jar`
    
    `echo %classpath%`
    

# 📖 웹 프로젝트의 구조

![webStructure.png]({{"../../../../assets/images/servlet/cmdTomcat/webStructure.png" | relative_url}})

신경써서 봐야할 것은 WEB-INF 폴더이다. 

## 📁 WEB-INF

- 프론트엔드 부분과 백엔드 부분을 나누는 기준이 되는 폴더
- 사용자에게 보여줄 수 없는 내용들을 정리해놓은 폴더로, 이 부분이 백엔드 부분이 된다.
- 즉, 프론트엔트 부분(html, css, image 등)은 이 폴더 밖에 위치해야 한다.
- 세 가지 하위 요소를 가지고 있다.
    
    ### 📂 classes
    
    클래스 파일들이 모여 있는 폴더
    
    ex) servlet 클래스
    
    ### 📂 lib
    
    소스 코드에서 필요로 하는 라이브러리들이 있는 파일
    
    ex) servlet-api.jar
    
    ### 📝 web.xml
    
    - 배포 디스크립터로서, 전반적인 동작을 제어하고 설정하는 모든 웹 어플리케이션이 반드시 한 개 가지고 있어야 하는 파일
    - 어플리케이션의 클래스, 리소스, 구성 등을 기술하는 곳
    - 서블릿 객체를 생성하고, 경로를 매핑한다.
    - 404와 같은 오류에 대해서 에러 페이지를 설정
    

# 📖 톰캣으로 웹 프로젝트 실행하기

### 1. Tomcat 설치 폴더의 webapps 폴더에 프로젝트 올려놓기

### 2. server.xml에 프로젝트 위치 등록하기

Tomcat 폴더 > conf 폴더 > server.xml 파일을 열어서 다음의 내용을 작성하면 프로젝트가 톰캣에 연결이 된다.

```xml
<Context path="외부에서 접근하기 위한 가상 경로"
				 docBase="실제 프로젝트 작업 경로"
				 workDir="실제 프로젝트 작업 경로\work" />
```

path와 docBase는 필수 속성이다.

## 예제) 폼 태그로 값 입력 받아 페이지에 출력하기

### 구조

| web03 |  |  |  |
| --- | --- | --- | --- |
|  | index.html |  |  |
|  | WEB-INF |  |  |
|  |  | classes |  |
|  |  |  | ReqTest.class |
|  |  | src |  |
|  |  |  | ReqTest.java |
|  |  | web.xml |  |

### 프로젝트 위치 등록

### 📝server.xml

톰캣이 설치된 폴더 > conf > server.xml 파일을 열어 프로젝트 위치를 등록해주자.

```xml
<Context path="/web3" 
				 docBase="C:\Users\BackEnd\JspWork\Manual\web03" 
				 workDir="C:\Users\BackEnd\JspWork\Manual\web03\work" />
```

path에는 임의로 정한 가상 주소의 이름, docBase에는 웹 프로젝트의 폴더 경로를 입력한다.

### 📝ReqTest.java

```java
import javax.servlet.http.*;
import java.io.*;

public class ReqTest extends HttpServlet{
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
		doPost(req, resp);
	}
	
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) {
		
		PrintWriter out = null;
		try{
			req.setCharacterEncoding("euc-kr");
			resp.setCharacterEncoding("euc-kr");
			
			String name = req.getParameter("name");
			String addr = req.getParameter("addr");
		
			out = resp.getWriter();
			out.println("<html><body>");
			if(req.getMethod().equals("POST")){
				out.println("<h1>Request POST Test</h1>");
			}
			else{
				out.println("<h1>Request GET Test</h1>");
			}
			
			out.println("당신이 보낸 데이터는 다음과 같습니다<br/>");
			out.println("이름은 " + name + "이고 주소는 " + addr + "입니다.");
			out.println("</body></html>");
		}
		catch(UnsupportedEncodingException e) {}
		catch(IOException e){
			e.printStackTrace();
		}
		catch(Exception e) {}
		finally{
			out.close();
		}
	}
}
```

코드의 자세한 내용은 다음에 정리할 예정이다.

web\WEB-INF\src 위치에서 `javac -d ../classes ReqTest.java`  명령어를 입력해 classes 폴더에 클래스 파일을 넣어준다. 

### 📝web.xml

```xml
<!-- ReqTest reqTest = new ReqTest -->
<servlet>
	<servlet-name>reqTest</servlet-name>
	<servlet-class>ReqTest</servlet-class>
</servlet>

<!-- http://localhost:8080/web3 -->
<servlet-mapping>
	<servlet-name>reqTest</servlet-name>
	<url-pattern>/reqTest</url-pattern>
</servlet-mapping>
```

ReqTest 서블릿 객체를 생성하여 /reqTest라는 가상주소에 매핑하는 과정을 거친다.

### 📝index.html

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jiwon</title>
</head>
<body>
    <h1 style="color: skyblue;">Web03 프로젝트</h1>
	<a href="http://localhost:8080/web3/reqTest">ReqTest 서블릿 호출</a>
	<form action="http://localhost:8080/web3/reqTest">
		이름 : <input type="text" name="name" /><br/><br/>
		주소 : <input type="text" name="addr" /><br/><br/>
		<button>전송</button>
	</form>
	
	<form action="http://localhost:8080/web3/reqTest" method="POST">
		이름 : <input type="text" name="name" /><br/><br/>
		주소 : <input type="text" name="addr" /><br/><br/>
		<button>전송</button>
	</form>
</body>
</html>
```

get 방식과 post 방식으로 각각 폼 입력을 받아 그 결과를 출력하는 페이지를 만들었다. 코드의 자세한 설명은 다음 포스팅에서 정리할 예정이다…

`<form action="http://localhost:8080/web3/reqTest">` 을 통해 폼이 제출되었을 때 해당 위치로 이동하도록 한 것인데, `/web3`은 server.xml에서 설정한 프로젝트 폴더의 가상주소이고, `/reqTest`는 web.xml에서 매핑한 reqTest 서블릿 객체의 가상주소임을 확인할 수 있을 것이다.

### 페이지 실행

1. cmd 창에서 startup을 통해 톰캣 서버 실행
2. 크롬 브라우저에 “[http://localhost:8080/web3/index.html](http://localhost:8080/web3/index.html)” 입력

![indexpage.png]({{"../../../../assets/images/servlet/cmdTomcat/indexpage.png" | relative_url}})

1. 이름과 주소를 입력하고 전송버튼을 누르면 자동으로 서버에 값이 넘어가는 것을 확인할 수 있다.

![serverpage.png]({{"../../../../assets/images/servlet/cmdTomcat/serverpage.png" | relative_url}})

# 💡톰캣 서버가 실행이 안 될 때

web.xml 파일에 문제가 있으면 서버 창이 떴다가 자동으로 꺼져버려서 무엇이 문제인지 에러메세지조차 확인할 수 없는 경우가 많다. 이럴 땐 다음과 같은 설정을 통해 창을 새로 띄우지 않고 서버를 실행할 수 있다.

1. 톰캣이 설치된 폴더 > bin > startup.bat 파일 열기
2. `start`를 `run`으로 변경

마지막 줄에 다음과 같은 명령어가 적혀있다.

```bash
call "%EXECUTABLE%" start %CMD_LINE_ARGS%
```

run으로만 바꿔주면 꺼질 일 없이 에러를 확인할 수 있다.

```bash
call "%EXECUTABLE%" run %CMD_LINE_ARGS%
```

참고

---

[https://velog.io/@hazel_jo/JSP-스프링-1강-웹-어플리케이션-웹-프로그래밍-기초](https://velog.io/@hazel_jo/JSP-%EC%8A%A4%ED%94%84%EB%A7%81-1%EA%B0%95-%EC%9B%B9-%EC%96%B4%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98-%EC%9B%B9-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EA%B8%B0%EC%B4%88)

[https://hipdizzy.tistory.com/61](https://hipdizzy.tistory.com/61)