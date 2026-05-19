+++
title = "Spring Framework — Java 애플리케이션 개발의 뼈대"
date = "2024-05-28"
description = "Spring은 의존성 주입(DI)과 관점 지향 프로그래밍(AOP)을 핵심으로, Java 앱의 객체 관리와 횡단 관심사를 프레임워크가 대신 처리해주는 도구 모음이다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Spring은 의존성 주입(DI)과 관점 지향 프로그래밍(AOP)을 핵심으로, Java 앱의 객체 관리와 횡단 관심사를 프레임워크가 대신 처리해주는 도구 모음이다.

---

## Spring Framework를 왜 쓰는지 감 잡기

Java로 앱을 만들 때 객체끼리 의존 관계를 코드 안에 직접 연결하면, 테스트하거나 교체할 때마다 코드를 대거 수정해야 한다. 로깅, 보안 확인, 트랜잭션 처리 같은 공통 로직도 메서드마다 반복된다. Spring은 이 두 문제를 DI와 AOP로 해결한다. 개발자는 비즈니스 로직에만 집중하고, 나머지는 프레임워크에 맡긴다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 객체(Bean) 정의 → Spring 컨테이너가 생성·주입 → 부가 기능(AOP)은 자동 적용 → 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| DI (의존성 주입) | 객체가 필요한 다른 객체를 직접 만들지 않고 외부(컨테이너)에서 받는 방식 |
| AOP (관점 지향 프로그래밍) | 로깅·보안처럼 여러 곳에 반복되는 처리를 비즈니스 로직과 분리해 관리하는 기법 |
| Bean | Spring 컨테이너가 생성하고 관리하는 객체 |
| Spring Boot | 설정 최소화 + 내장 서버로 Spring 앱을 빠르게 시작할 수 있게 해주는 래퍼 |
| ApplicationContext | Bean을 저장하고 의존성을 연결하는 Spring의 중앙 컨테이너 |

## 예를 들어 설명하면

DI 없이 작성하면 Car가 Engine을 직접 생성한다. 테스트 시 Engine을 교체하려면 Car 코드를 열어야 한다.

```java
// DI 없음 — 강결합
public class Car {
    private Engine engine = new Engine(); // Car가 직접 생성
}

// DI 적용 — 느슨한 결합
public class Car {
    private final Engine engine;

    @Autowired
    public Car(Engine engine) { // Spring이 주입
        this.engine = engine;
    }
}
```

AOP는 메서드 실행 전후에 자동으로 끼어드는 코드를 분리한다. 로깅을 비즈니스 로직에서 완전히 빼낼 수 있다.

```java
@Aspect
public class LoggingAspect {
    @Before("execution(* OrderService.placeOrder(..))")
    public void logBefore() { System.out.println("주문 시작"); }

    @After("execution(* OrderService.placeOrder(..))")
    public void logAfter()  { System.out.println("주문 완료"); }
}
```

## 이 단계에서 중요한 판단 기준

새 프로젝트에서 Spring을 시작할 때는 Spring Boot를 택한다. 설정 파일 없이 `@SpringBootApplication` 하나로 내장 서버까지 구동되어 초기 진입 비용이 훨씬 낮다.

## 한 줄 요약 — 이것만 기억하면 된다

**Spring은 DI로 객체 간 결합을 낮추고, AOP로 공통 처리를 분리해 Java 앱을 테스트하기 쉽고 유지하기 쉬운 구조로 만든다.**

## 나중에 더 깊게 들어가면

- Spring MVC: 웹 요청을 Controller → Service → Repository 계층으로 처리하는 구조
- Spring Data JPA: JPA 기반 데이터 접근을 인터페이스 선언만으로 처리하는 모듈
- Spring Security: 인증(Authentication)과 인가(Authorization)를 필터 체인으로 처리하는 보안 모듈

---

**원본:** [Spring Framework Introduced — https://memoryhub.tistory.com/116](https://memoryhub.tistory.com/116)
