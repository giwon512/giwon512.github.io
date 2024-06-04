# 대학 주점 주문 관리 프로젝트

📄 **github**

[https://github.com/giwon512/order-management-service](https://github.com/giwon512/order-management-service)

# 🖊️프로젝트를 시작하게 된 계기

 대학 중앙 동아리에서 임원진을 하고 있는 상황에서, 대학교 축제를 맞이하여 동아리 주점 부스를 열 계획을 세우게 되었다. 주문도 엄청 들어오는 데다가 주문 받는 사람도 여러 명이라 주문을 받아 관리하는 서비스가 없이는 주점 운영을 할 수가 없다. 축제 3일 간 가볍게 쓸 서비스를 찾을 수 없었고, 동아리 연합회에 주문 관리 서비스를 제공하겠다는 업체가 연락을 해왔지만, 수익금의 7% 내외를 수수료로 내야했기에, 이참에 직접 개발을 해보자고 생각하게 되었다.

# 🖊️프로젝트 목표

주문 관리 서비스이기 때문에, 구현 목표는 확실하다.

1. 손님들이 보는 주문 페이지는 시중의 다른 배달 어플과 비슷하게 구현(메뉴 목록, 장바구니, 주문 확인 페이지)
2. 주문 받은 내용을 확인할 수 있는 관리자 페이지 구현(현재 진행 중인 주문 내역, 총 주문 내역 페이지)
3. 외부 DB 설계 및 연결 후, 외부 서버에 최종 배포(클라우드 타입 활용)

# 📚 활용된 개발 언어 및 환경

![springboot.png]({{"../../../../assets/images/Project/OrderManagement/springboot.png" | relative_url}})

![java.png]({{"../../../../assets/images/Project/OrderManagement/java.png" | relative_url}})

![mybatis.png]({{"../../../../assets/images/Project/OrderManagement/mybatis.png" | relative_url}})

![thymeleaf-logo.png]({{"../../../../assets/images/Project/OrderManagement/thymeleaf-logo.png" | relative_url}})

![lombok.png]({{"../../../../assets/images/Project/OrderManagement/lombok.png" | relative_url}})

![tomcat.png]({{"../../../../assets/images/Project/OrderManagement/tomcat.png" | relative_url}})

![mariadb.png]({{"../../../../assets/images/Project/OrderManagement/mariadb.png" | relative_url}})

![htmlCssJs.png]({{"../../../../assets/images/Project/OrderManagement/htmlCssJs.png" | relative_url}})

![heidisql.png]({{"../../../../assets/images/Project/OrderManagement/heidisql.png" | relative_url}})

![sts4.png]({{"../../../../assets/images/Project/OrderManagement/sts4.png" | relative_url}})

![cloudtype.png]({{"../../../../assets/images/Project/OrderManagement/cloudtype.png" | relative_url}})

![postman.png]({{"../../../../assets/images/Project/OrderManagement/postman.png" | relative_url}})

# 🗂️ 프로젝트 아키텍처

![architecture.png]({{"../../../../assets/images/Project/OrderManagement/architecture.png" | relative_url}})

# 🗂️ 서비스 사이트맵 구조

![sitediagram.png]({{"../../../../assets/images/Project/OrderManagement/sitediagram.png" | relative_url}})

# 📘 페이지 상세

페이지가 그리 많지 않고 동작이 간단하기 때문에 굳이 리액트를 활용하여 restful api로 개발하지 않고, 타임리프를 이용하여 스프링 부트 프로젝트 내에서 페이지까지 구성하였다.

## 📖 로그인 페이지

실제 사용자에게는 보이지 않는 페이지로, 테이블 번호를 선택하거나 관리자 페이지로 이동할 수 있게 했다. 주점 고객들의 경우 이미 테이블 번호가 선택되어있는 메뉴 목록 페이지의 QR코드를 제공받는 구조이다.

![로그인 페이지.png]({{"../../../../assets/images/Project/OrderManagement/loginPage.png" | relative_url}})

MainController를 통해 메뉴 목록 페이지 또는 실시간 주문 관리 페이지로 이동하게 된다.

## 📖 메뉴 목록 페이지

주점에서 판매하는 메뉴들을 보여주는 메뉴판 페이지이다. 

![메뉴 목록 페이지.png]({{"../../../../assets/images/Project/OrderManagement/menuListPage.png" | relative_url}})

```java
private static List<Menu> mainMenuList = new ArrayList<>();
private static List<Menu> sideMenuList = new ArrayList<>();
private static List<Menu> selectedList = new ArrayList<>();

public void makeMenuList() {
	mainMenuList.add(new Menu(0, "입장료", 3000, "/images/ready.jpg"));
	mainMenuList.add(new Menu(1, "오뎅탕", 14000, "/images/foods/fishCake.jpeg"));
	...
	
	sideMenuList.add(new Menu(10, "김치", 1000, "/images/foods/sideKimchi.jpg"));
	sideMenuList.add(new Menu(11, "물", 1000, "/images/foods/water.png"));
	...
}
```

OrderController → MenuService를 통하여 메뉴 목록을 가져와 리스트를 보여준다.

원래는 메뉴 목록을 DB에 저장해두고 가져와서 사용하려고 했으나 당장 해당 프로젝트를 우리 주점만 사용하기도 하고, 메뉴도 그렇게 많지 않기 때문에 static으로 리스트를 선언해서 값을 저장하고, 넘겨주었다.

메뉴 DTO를 따로 만들어서 메뉴 번호, 메뉴명, 가격, 메뉴 이미지 경로 값을 다루었다.

```java
public List<Menu> setOrderMenuList(int menuNum){
	if(menuNum < 10) {
		selectedList.add(mainMenuList.get(menuNum));
	}
	else {
		selectedList.add(sideMenuList.get(menuNum - 10));
	}
	return selectedList;
}
```

```java
@GetMapping("/addList")
public String addList(HttpServletRequest request, @RequestParam(value="menuNum") int menuNum) {
	List<Menu> list = menuService.setOrderMenuList(menuNum);
	HttpSession session = request.getSession();
	if(session.getAttribute("tblNum") == null) {
		return "order/Error";
	}
	session.setAttribute("orderList", list);
	
	int totalCost = 0;
	for(Menu menu : list) {
		totalCost += menu.getPrice();
	}
	session.setAttribute("totalCost", totalCost);
	
	//이전 페이지로 이동
	String referer = request.getHeader("Referer");
	return "redirect:"+ referer;
}
```

각 메뉴를 클릭하면 `*"|location.href='@{/order/addList(menuNum=${menu.menuNum})}'|"*` 경로로 이동하면서 selectedList에 파라미터로 넘어온 메뉴 번호에 해당하는 메뉴를 추가하고, 이전 페이지로 이동한다.

## 📖 장바구니 페이지

![장바구니 페이지.png]({{"../../../../assets/images/Project/OrderManagement/cartPage.png" | relative_url}})

장바구니 페이지에서는 selectedList에 있는 값을 출력하고, 메뉴 추가와 같은 방식으로 삭제 버튼을 누르면 해당하는 인덱스의 메뉴를 리스트에서 제거하는 방식으로 코드를 구현했다.

```java
@GetMapping("/pay")
public String pay(Model model, HttpServletRequest req) {
	//주문 정보 DB에 저장
	if(req.getSession().getAttribute("tblNum") == null) {
		return "order/Error";
	}
	
	Order newOrder = menuService.getOrderInfo(req);
	if(newOrder.getCost() == 0) {
		return "order/orderList";
	}
	interFace.newOrder(newOrder);
	
	//저장된 세션 정보들 초기화 
	menuService.selectedListInit();
	
//		req.getSession().setAttribute("totalCost", 0);
	return "index";
}
```

주문 정보(주문 번호, 주문 내역, 총 금액, 테이블 번호, 주문 시간)를 담고 있는 Order DTO를 생성하였다. 주문하기를 누르면 orderController를 통해 menuService에서 주문 정보를 생성하는 `getOrderInfo` 함수를 호출하고, 그 주문 내역을 DB에 저장하는 작업을 수행한다.

### trouble shooting1

한 번 주문을 한 이후에는 메뉴 선택이 안되고 DB에 이상한 주문이 insert 되는 문제가 발생했다.

/order/pay에서 주문 내역을 DB에 보내고 메뉴를 고른 리스트를 초기화하는 작업을 수행한 후 원래 메뉴 페이지로 돌아가는 작업을 수행한다.

/order/addList에서 메뉴를 선택한 경우 리스트에 해당 메뉴를 추가하고 request 객체의 referer 프로퍼티를 통해서 기존 보고 있던 메뉴 페이지로 돌아가도록 컨트롤러를 설계를 했는데, 주문한 직후에는 referer의 값이 /order/menuList가 아닌 /order/pay가 되므로 메뉴를 선택할 때마다 주문이 발생하고, 메뉴를 고른 리스트가 초기화되어 선택이 되지 않는 것처럼 보이는 것이었다. 

따라서 주문 작업을 수행한 이후 기존 주문 내역을 보여주던 페이지를 넘어가서, 그 곳에서 menuList.html로 돌아가게 해 주문 직후에도 referer의 값이 /order/menuList가 되도록 수정을 해서 해결했다.

### trouble shooting2

웹 서비스를 휴대폰에서 사용했을 때 동작이 다르게 되는 부분이 있어서 급하게 코드를 수정한 바 있다. 안드로이드 휴대폰의 경우에는 잘 작동했다. 하지만, IOS 휴대폰의 경우에 onclick 이벤트와 ontouch 이벤트가 잘 연동이 되는 것을 확인했으나 직접 onclick으로 이벤트를 지정해주지 않은 a태그의 경우에는 터치가 되지 않는 문제를 발견했고, 주점 시작 전에 급하게 고쳐 해결하였다.

## 📖 실시간 주문 관리 페이지

![실시간 주문 관리 페이지.png]({{"../../../../assets/images/Project/OrderManagement/realTimeManager.png" | relative_url}})

주문한 내역이 실시간으로 추가되는 페이지로, 서빙까지 완료 후 완료 버튼을 누르면 리스트에서 삭제되는 동작을 구현했다. 손님들에게 보여지지 않는 페이지라 대충 만들었어도 됐을거라고 생각했는데, 바쁜 상황에서 가시성이 떨어져 불편함을 느꼈다. 또한 주문 전체 완료 버튼만 만들 것이 아니라, 주문한 메뉴 별로 완료 버튼을 만들어 어떤 메뉴가 나갔는지 체크할 수 있게 하는 게 중요해 보였고, 내가 체감한 가장 아쉬운 부분이다.

## 📖 총 주문 관리 페이지

말 그대로 총 주문 내역과 얻은 총 수익을 보여준다.

![총 주문 관리 페이지.png]({{"../../../../assets/images/Project/OrderManagement/Manager.png" | relative_url}})

실제 5월 23일 한양대 축제 주점에서의 주문 내역이다.

# 📔 서버 배포하기

원래는 aws ec2 프리티어 버전과 rds를 활용하여 배포를 하고자 했으나, rds에서 금액이 청구되는 것을 확인했다. 

![aws 청구서.png]({{"../../../../assets/images/Project/OrderManagement/aws_bill.png" | relative_url}})

구글링을 해보니 클라우드 타입이 활용하기도 훨씬 쉽고 무료로 잘 쓸 수 있을 것 같아 이것을 활용해보기로 했다.

![서버 환경 변수.png]({{"../../../../assets/images/Project/OrderManagement/serverSetenv.png" | relative_url}})

깃허브에 개인 정보가 올라가는 것을 피하기 위해서, 서버 환경 변수에 해당하는 값들을 넣어주고, `application.properties`에 다음과 같이 작성했다. 참고로 클라우드 타입에서 만든 데이터베이스도 주소를 가지고 있지만, 서버도 클라우드 타입의 같은 프로젝트에 위치하는 경우에, Internal 주소인 mariadb만 적어주어도 잘 연결되는 것을 확인했다.

```java
spring.application.name=OrderManagement

#Oracle
#spring.datasource.url=jdbc:oracle:thin:@localhost:1521:xe
#spring.datasource.driverClassName=oracle.jdbc.driver.OracleDriver
#spring.datasource.username=user1
#spring.datasource.password=1111

#MariaDB
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://${DB_url}:${DB_port}/${DB_dataBaseName}
spring.datasource.username=${DB_name}
spring.datasource.password=${DB_password}

mybatis.type-aliases-package=com.mysite.domain
```

### trouble shooting

백엔드 서버를 생성하여 연결할 때는 내 프로젝트 구조가 발목을 잡았다. 깃허브 페이지를 연동하면 알아서 jar 파일로 압축을 하고, 실행을 하는 방식을 취하는 걸로 알고 있었지만, gradlew build를 하면 나타나게 되는 그 많은 파일들을 하나도 찾아내지 못하는 문제가 발생했다. 레포지토리에서 바로 웹 프로젝트 폴더 구조가 나타나는 것이 아니라, 폴더를 하나 더 만들고 그 안에 프로젝트 구조를 넣어 두었기에 문제가 발생했다. 실제로 직접 jar 파일을 만들어 서버를 실행하고자 한다면, 깃허브 레포지토리인 order-management-service 밑의 OrderManagement 폴더 내에서(즉, build.gradle이 있는 폴더 위치까지) gradlew build를 해줘야함을 확인했고, 자동으로 배포를 할 때 서브 폴더의 경로로 /OrderManagement까지 명시함으로써 문제를 해결할 수 있었다. 지나고 보면 매우 당연한 얘기지만 배포가 안 되고 오류 날 당시에는 엄청 당황했다..

# 📝소감

 서버에 배포까지 해본 프로젝트는 처음이라 더 뜻이 깊은 것 같다. 대단한 것은 아니었지만 실제로 축제에 찾아오는 불특정 다수의 사람들이 이 서비스를 사용할 것이었기에 오류가 발생할까 봐 부담이 많이 컸다. 풀스택 개발에 배포까지 오롯이 혼자 다 해낸 프로젝트였기에 애착도 더 많은 것 같다. 다행히 주문 받는 데 아무런 문제도 나타나지 않았고, 무사히 주점을 마칠 수 있었다. 앞으로 개발자로 살면서 내가 짠 코드가 수익에 직결될 일이 많을 텐데, 책임감을 가지고 서비스를 구현하는 개발자가 되고자 마음가짐을 다잡았다.