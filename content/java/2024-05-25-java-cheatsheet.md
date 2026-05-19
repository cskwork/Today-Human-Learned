+++
title = "Java CheatSheet"
date = "2024-05-25"
description = "Java의 핵심 문법 — 타입, 연산자, 제어문, 배열, OOP 4대 원칙 — 을 한 곳에 정리한 참조 카드다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Java의 핵심 문법 — 타입, 연산자, 제어문, 배열, OOP 4대 원칙 — 을 한 곳에 정리한 참조 카드다.

---

## Java CheatSheet를 왜 쓰는지 감 잡기

Java를 처음 배우면 문법이 많아 어디서부터 외워야 할지 막막하다. 모든 것을 외울 필요는 없다. 자주 쓰는 패턴 위주로 손에 익히고, 나머지는 필요할 때 찾아보면 된다.

이 카드는 Java의 핵심 문법을 "쓰는 순서대로" 정리한 빠른 참조용이다.

`핵심 흐름: 타입 선언 → 연산 → 제어 흐름 → 객체 설계(OOP)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 기본형(Primitive) | int, double, boolean처럼 값 자체를 담는 타입. 객체가 아니다. |
| 참조형(Reference) | String, 배열, 클래스처럼 실제 데이터 위치를 가리키는 타입. |
| 오버로딩(Overloading) | 같은 이름의 메서드를 매개변수 타입/개수만 다르게 여러 개 만드는 것. |
| 오버라이딩(Overriding) | 부모 클래스의 메서드를 자식 클래스에서 다시 구현하는 것. |
| 캡슐화(Encapsulation) | 필드를 `private`으로 숨기고 getter/setter로만 접근하게 해 내부 구조를 보호하는 것. |

## 예를 들어 설명하면

**기본 타입과 연산**

```java
int a = 10, b = 3;
System.out.println(a + b);  // 13
System.out.println(a / b);  // 3  (정수 나눗셈, 소수점 버림)
System.out.println(a % b);  // 1  (나머지)
double result = (double) a / b; // 3.333...  (캐스팅으로 실수 나눗셈)
```

**제어문 — 가장 자주 쓰는 형태**

```java
// for-each (배열·컬렉션 순회)
int[] nums = {1, 2, 3, 4};
for (int n : nums) {
    System.out.println(n);
}

// switch (Java 14+ 이전 방식)
switch (month) {
    case 1: System.out.println("January"); break;
    default: System.out.println("Other"); break;
}
```

**OOP — 상속과 오버라이딩**

```java
interface Shape {
    void draw();
}
class Circle implements Shape {
    @Override
    public void draw() { System.out.println("Drawing circle"); }
}
// 인터페이스 타입으로 받아서 다형성 활용
Shape s = new Circle();
s.draw();
```

## 이 단계에서 중요한 판단 기준

`int / int`는 정수 나눗셈이므로 소수점이 필요하면 반드시 한쪽을 `double`로 캐스팅하거나 `1.0`을 곱해야 한다. 이 함정을 가장 먼저 외워라.

## 한 줄 요약 — 이것만 기억하면 된다

**Java 문법의 뼈대는 "타입 선언 → 연산 → 제어 → 클래스 설계"이며, OOP 4대 원칙(상속·다형성·캡슐화·추상화)이 코드 구조의 근거가 된다.**

## 나중에 더 깊게 들어가면

- Java Generics: `List<T>`, `Map<K,V>` 등 타입 파라미터 활용법
- Java Collections Framework: ArrayList, HashMap, LinkedList 선택 기준
- Java 8+ 람다와 스트림 API: `filter`, `map`, `reduce` 패턴

---

**원본:** [Java CheatSheet — https://memoryhub.tistory.com/57](https://memoryhub.tistory.com/57)
