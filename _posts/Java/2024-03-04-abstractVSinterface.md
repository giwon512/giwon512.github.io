---
layout: single
title: 인터페이스의 특징과 필요성
categories: Java
tag: [Backend, Java, 자바]
toc: true
---


> 추상 클래스와 인터페이스를 각각 공부해보아도 차이점을 명확히 설명할 수 없어 찾아본 결과 납득하기 쉽게 정리해 놓은 블로그가 있어 이를 바탕으로 자세히 알아보았다.
> 

## 📔 추상 클래스

[https://giwon512.github.io/java/abstract/](https://giwon512.github.io/java/abstract/)

→ 하위 클래스들이 공통으로 가지는 멤버들을 통합하여 가지고 있는 느낌으로, 하위 클래스들은 추상 클래스의 기능을 이어받고 확장해 나가는 느낌을 가진다.

→ 논리적인 상속 구조가 있음

## 📔 인터페이스

[https://giwon512.github.io/java/interface/](https://giwon512.github.io/java/interface/)

→ 인터페이스에 정의된 메서드를 각 클래스의 목적에 맞게 기능을 구현해 나간다.

# 추상 클래스 vs 인터페이스

---

## 📖 추상 클래스를 사용하는 경우

- 상속 받을 클래스들의 **중복되는 멤버를 통합**할 때
- 멤버에 public 이외의 접근자 선언이 필요한 경우
- 상태 변경을 위한 메서드가 필요하여 non-static, non-final 선언이 필요한 경우
    
    → 인터페이스는 public static final로만 선언이 가능하기에 위의 두 경우에는 추상클래스를 사용할 수밖에 없다.
    
- 요구사항과 함께 구현 세부 정보의 일부 기능만 지정했을 때

## 📖 인터페이스를 사용하는 경우

- 다중 상속을 통한 추상화 설계를 해야 할 때
    
    →자바에서 클래스는 다중 상속을 지원하지 않는다.
    
- 필요에 의해서 서로 관련이 적은 클래스들끼리 형제 타입처럼 묶어버릴 때
- 특정 타입의 행동을 명시하고 싶지만 어디서 그 행동이 구현될 지는 신경쓰지 않는 경우

### 예시

```java
//중복되는 멤버들을 animal 추상 클래스에 모아서 정의한다.
abstract class Animal {
	String name;
	int age;
	abstract void sleeping();	
}

//날 수 있는 기능을 따로 구현한다. 이를 Animal 추상클래스에 같이 넣게 되면,
//날 수 없는 사람도 일단 해당 기능을 빈 내용으로 구현해야만 하는 문제가 생긴다.
interface Flyable {
	public static final int speed = 10;
	void flying();
}

//마찬가지로 말하는 기능도 따로 구현한다.
interface Talkable {
	void talking();
}

//인터페이스로는 다중 상속이 가능하다.
class Parrot extends Animal implements Flyable, Talkable{
	
	Parrot(String name, int age){
		this.name = name;
		this.age = age;
	}
	@Override
	void sleeping() {System.out.println("서서 잔다.");}
	@Override
	public void flying() {System.out.println("앵무새는 날 수 있다.");}
	@Override
	public void talking() {System.out.println("앵무새는 말할 수 있다.");}

}

//앵무새와 박쥐 클래스는 날 수 있는 기능을 상속받아 마치 형제처럼 관리될 수 있다.
class Bat extends Animal implements Flyable{
	
	Bat(String name, int age){
		this.name = name;
		this.age = age;
	}
	@Override
	void sleeping() {System.out.println("매달려서 잔다.");}
	@Override
	public void flying() {System.out.println("박쥐는 날 수 있다.");}
}

class People extends Animal implements Talkable{
	
	People(String name, int age){
		this.name = name;
		this.age = age;
	}
	@Override
	void sleeping() {System.out.println("누워서 잔다.");}
	@Override
	public void talking() {System.out.println("사람은 말할 수 있다.");}

}

class Test {
	public static void main(String[] args) {
		Parrot parrot = new Parrot("bird", 5);
		Bat bat = new Bat("bat", 3);
		People people = new People("tom", 12);
		
		parrot.sleeping();
		bat.sleeping();
		people.sleeping();
		parrot.flying();
		parrot.talking();
		bat.flying();
		people.talking();
		
	}
}
```

참고

---

[https://inpa.tistory.com/entry/JAVA-☕-인터페이스-vs-추상클래스-차이점-완벽-이해하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-vs-%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)