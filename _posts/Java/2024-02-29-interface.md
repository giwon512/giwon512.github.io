---
layout: single
title: 인터페이스의 특징과 필요성
categories: Java
tag: [Backend, Java, 자바]
toc: true
---

## 📖 인터페이스란?

> 다른 클래스를 작성할 경우 기본이 되는 틀을 제공하면서 다른 클래스 사이의 중간 매개 역할까지 담당하는 일종의 추상 클래스를 의미한다.
> 

## 📖 인터페이스의 구조

- 상수, 추상 메서드, 디폴트 메서드, 정적 메서드만을 보유할 수 있다.
    1. 상수 : 인터페이스에서 정해준 값을 바꿀 수 없고 참조만 할 수 있다.
    2. 추상 메서드 : 인터페이스에서 가이드만 제공해 준 것으로, 각 구현 클래스에서 자신에게 맞게 오버라이딩 하면 된다.
    3. 디폴트 메서드 : 인터페이스에서 제공해주는 기본적인 기능으로, 구현 클래스에서 따로 선언 없이 바로 써줘도 되고, 오버라이딩을 해도 상관없다.
    4. 정적 메서드 : 인터페이스에서 제공해주는 기본적인 기능으로 오버라이딩이 불가능하고, 무조건 그대로 갖다 쓰기만 할 수 있다.

## 📖 인터페이스의 다형성과 특징

- 다중 구현이 가능하고, 인터페이스는 인터페이스만 상속 받을 수 있다.
- 다형성을 지원하며 구현 객체의 인스턴스로 주소 값 공유가 가능하다.
- 리턴 타입으로 사용될 수 있으며 매개변수로 전달이 가능하다.
- 구현 클래스를 생성할 때 인터페이스 타입으로 선언이 가능하다.

```java
//인터페이스 타입으로 클래스 생성 가능
Animal animal = new Cat();

//인터페이스 자료형을 매개변수로 전달 가능
public void send(Animal animal){
	생략...
}

//리턴 타입을 인터페이스 자료형 리턴 가능
public Animal returnInterface(){
	Animal animal = new Cat();
	return animal;
}
```

[https://velog.io/@ung6860/JAVA인터페이스Interface에-대해-알아보자-fc81k7rr](https://velog.io/@ung6860/JAVA%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4Interface%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-fc81k7rr) 

블로그의 코드를 가져온 것으로, 이 코드만 봐도 다형성이 어떻게 지원되는지 이해하기가 쉽다.

- 모든 필드는 public static final로 선언해야 하고, 추상 메서드의 abstract 키워드는 생략 가능하다.

## 📖 인터페이스의 용도

- 자바에서 하나의 클래스는 하나의 슈퍼 클래스만 상속받을 수 있다.
    
    → 다중 상속이 지원되지 않기 때문에, 메서드의 분할이 필요하거나 다중 상속이 필요하다면 인터페이스를 사용한다.
    

참고

---

[https://velog.io/@ung6860/JAVA인터페이스Interface에-대해-알아보자-fc81k7rr](https://velog.io/@ung6860/JAVA%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4Interface%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-fc81k7rr)

[https://limkydev.tistory.com/197](https://limkydev.tistory.com/197)