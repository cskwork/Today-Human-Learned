+++
title = "Spring Bean — 스프링이 객체를 관리하는 방식"
date = "2024-06-02"
description = "Spring Bean은 개발자가 직접 `new`로 생성하는 대신, 스프링 컨테이너가 대신 만들고 관리하는 객체다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Spring Bean은 개발자가 직접 `new`로 생성하는 대신, 스프링 컨테이너가 대신 만들고 관리하는 객체다.

---

## Spring Bean을 왜 쓰는지 감 잡기

보통 Java 코드에서 객체가 필요하면 `new Car(new Engine())`처럼 직접 생성한다. 문제는 이 방식이 코드 곳곳에 퍼지면, 나중에 `Engine`을 다른 구현체로 바꾸거나 테스트용 가짜 객체로 교체할 때 수십 군데를 고쳐야 한다는 점이다.

스프링은 이 문제를 "중앙 관리자"를 두는 방식으로 해결한다. 개발자는 어떤 객체가 필요하다고 선언만 하면, 스프링 IoC 컨테이너가 객체를 만들고 필요한 의존성을 주입한 뒤 생애주기까지 책임진다.

이렇게 컨테이너가 관리하는 객체를 **Spring Bean**이라고 부른다.

`핵심 흐름: 설정 선언 → IoC 컨테이너 시작 → Bean 생성 → 의존성 주입 → 앱 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Spring Bean | 스프링 컨테이너가 직접 만들고 관리하는 Java 객체 |
| IoC 컨테이너 | "내가 객체 만들 테니 넌 로직만 짜라"고 하는 스프링의 중앙 관리자 |
| 의존성 주입(DI) | 컨테이너가 필요한 객체를 알아서 끼워 넣어주는 방식 |
| @Bean | "이 메서드가 반환하는 객체를 Bean으로 등록하라"는 어노테이션 |
| @Configuration | "이 클래스에 Bean 정의들이 들어 있다"고 알려주는 어노테이션 |

## 예를 들어 설명하면

`Car`는 `Engine` 없이 동작할 수 없다. 스프링 없이는 `new Car(new Engine())`을 직접 써야 한다.
스프링을 쓰면 설정 클래스에 선언만 해두면 된다.

```java
@Configuration
public class AppConfig {

    @Bean
    public Engine engine() {
        return new Engine();
    }

    @Bean
    public Car car() {
        return new Car(engine()); // 컨테이너가 engine Bean을 주입
    }
}
```

이후 `Engine`을 `ElectricEngine`으로 바꾸고 싶으면 `engine()` 메서드 한 곳만 수정하면 된다.
`Car` 코드는 건드리지 않아도 된다.

## 이 단계에서 중요한 판단 기준

여러 곳에서 공유되거나 교체 가능성이 있는 객체라면 Bean으로 등록해서 컨테이너에 맡기고, 단순 데이터 보관용 객체(DTO, VO)는 Bean으로 만들지 않는다.

## 한 줄 요약 — 이것만 기억하면 된다

**Spring Bean은 `new` 대신 스프링 컨테이너가 생성·관리하는 객체이며, 의존성 교체와 테스트를 쉽게 한다.**

## 나중에 더 깊게 들어가면

- Bean 스코프(singleton vs prototype): 컨테이너가 하나만 만드는지, 요청마다 새로 만드는지
- @Component, @Service, @Repository: @Bean 없이 클래스 레벨에서 자동 등록하는 방법
- @Autowired와 생성자 주입: 의존성을 주입하는 세 가지 방식의 차이와 권장 방식

---

**원본:** [Spring Bean Introduced — https://memoryhub.tistory.com/180](https://memoryhub.tistory.com/180)
