+++
title = "Spring IoC 컨테이너 — 제어를 뒤집으면 무엇이 달라지나"
date = "2024-06-02"
description = "IoC 컨테이너는 객체 생성과 의존성 연결을 앱 코드 대신 스프링이 담당하게 만드는 핵심 장치다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> IoC 컨테이너는 객체 생성과 의존성 연결을 앱 코드 대신 스프링이 담당하게 만드는 핵심 장치다.

---

## IoC 컨테이너를 왜 쓰는지 감 잡기

전통적인 Java 코드에서는 객체가 필요한 곳마다 직접 생성한다. 클래스 A가 클래스 B를 필요로 하면 A 안에서 `new B()`를 호출한다. 이렇게 하면 A와 B가 단단하게 묶이고(강결합), 테스트할 때 B를 가짜 객체로 바꾸기가 어렵다.

IoC(Inversion of Control, 제어의 역전)는 이 흐름을 뒤집는다. 객체가 스스로 의존성을 찾는 대신, 외부 관리자(컨테이너)가 필요한 객체를 만들어서 끼워 준다. 스프링의 IoC 컨테이너가 바로 그 외부 관리자다.

컨테이너는 설정 정보를 읽고, Bean을 만들고, 의존성을 연결하고, Bean의 생애주기를 끝까지 책임진다.

`핵심 흐름: 설정 메타데이터 로드 → Bean 생성 → 의존성 주입 → 초기화 → 사용 → 소멸`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| IoC (제어의 역전) | "내가 필요한 거 직접 만들던 것"을 "외부가 알아서 줘"로 뒤집은 원칙 |
| 의존성 주입 (DI) | 컨테이너가 Bean에 필요한 다른 Bean을 대신 연결해주는 행위 |
| 설정 메타데이터 | 컨테이너에게 "어떤 Bean을 어떻게 만들어라"고 알려주는 지시서 (XML, 어노테이션, Java 클래스 중 하나) |
| ApplicationContext | 스프링 IoC 컨테이너의 대표 인터페이스. Bean 조회, 이벤트 발행 등 부가 기능 포함 |
| Bean 생애주기 | Bean이 생성되고 → 초기화되고 → 사용되다가 → 소멸하는 전체 과정 |

## 예를 들어 설명하면

`Car`가 `Engine`을 의존할 때, 컨테이너에 설정만 선언하면 연결은 컨테이너가 처리한다.

```java
@Configuration
public class AppConfig {

    @Bean
    public Engine engine() {
        return new Engine();
    }

    @Bean
    public Car car() {
        return new Car(engine()); // 생성자 주입 — Car는 Engine이 어디서 왔는지 알 필요 없다
    }
}
```

컨테이너는 앱 시작 시 `AppConfig`를 읽고, `engine` Bean을 먼저 만든 뒤 `car` Bean에 주입한다.
`Car` 클래스 내부 코드는 `Engine`의 구현 방식을 전혀 알지 못한다.

Bean의 소멸 시점에 정리 로직이 필요하면 `@PreDestroy` 어노테이션이 달린 메서드를 컨테이너가 자동으로 호출해준다.

## 이 단계에서 중요한 판단 기준

IoC 컨테이너를 쓸 때 가장 먼저 확인할 것은 "이 Bean이 상태를 가지는가(stateful)"이다. 상태가 없으면 기본 singleton 스코프로 충분하고, 요청마다 다른 상태가 필요하면 스코프를 변경해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**IoC 컨테이너는 객체 생성과 의존성 연결 책임을 앱 코드에서 스프링으로 옮겨, 느슨한 결합과 테스트 용이성을 동시에 얻게 해주는 스프링의 핵심 엔진이다.**

## 나중에 더 깊게 들어가면

- BeanFactory vs ApplicationContext: 두 컨테이너 인터페이스의 차이와 언제 각각을 쓰는지
- Bean 스코프(singleton, prototype, request, session): 컨테이너가 같은 Bean을 몇 번 만드는지
- @PostConstruct / @PreDestroy: Bean 초기화·소멸 시 실행할 커스텀 로직을 다는 방법

---

**원본:** [Spring IoC Container Introduced — https://memoryhub.tistory.com/181](https://memoryhub.tistory.com/181)
