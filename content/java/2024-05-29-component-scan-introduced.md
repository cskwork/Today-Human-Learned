+++
title = "Component Scan — Spring이 빈을 자동으로 찾는 방법"
date = "2024-05-29"
description = "Component Scan은 지정한 패키지를 뒤져서 `@Component` 계열 어노테이션이 붙은 클래스를 자동으로 Spring 컨테이너에 등록하는 기능이다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Component Scan은 지정한 패키지를 뒤져서 `@Component` 계열 어노테이션이 붙은 클래스를 자동으로 Spring 컨테이너에 등록하는 기능이다.

---

## Component Scan을 왜 쓰는지 감 잡기

Spring은 객체를 직접 `new`로 만들지 않고, 컨테이너가 대신 생성하고 관리한다. 이 객체를 빈(Bean)이라고 부른다. 초창기에는 모든 빈을 XML 파일에 하나씩 등록해야 했다. 클래스가 수십 개를 넘으면 설정 파일이 수백 줄이 된다. Component Scan은 이 반복 작업을 없애기 위해 등장했다. 패키지 경로만 알려주면 Spring이 직접 찾아서 등록한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 패키지 경로 지정 → 애플리케이션 시작 → 어노테이션 탐색 → 빈 자동 등록 → 의존성 주입 가능`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Bean | Spring 컨테이너가 관리하는 객체. 직접 `new`를 쓰지 않고 컨테이너에서 꺼내 쓴다 |
| @Component | "이 클래스를 빈으로 등록해라"는 표시. 가장 범용적인 어노테이션 |
| @Service, @Repository, @Controller | @Component를 상속한 전문화 어노테이션. 역할을 명확히 하기 위해 쓴다 |
| @ComponentScan | 어디를 탐색할지 패키지 경로를 알려주는 어노테이션 |
| IoC 컨테이너 | 빈을 생성, 보관, 연결하는 Spring의 핵심 관리 시스템 |

## 예를 들어 설명하면

패키지 `com.smart.home` 아래에 세 클래스가 있다.

```java
@Component
public class Light { }

@Service
public class Thermostat { }

@Controller
public class CameraController { }
```

설정 클래스에서 패키지를 지정한다.

```java
@Configuration
@ComponentScan(basePackages = "com.smart.home")
public class SmartHomeConfig { }
```

애플리케이션이 시작하면 Spring은 `com.smart.home`을 탐색해 세 클래스를 모두 빈으로 등록한다. 이후 어디서든 `@Autowired`나 생성자 주입으로 가져다 쓸 수 있다.

## 이 단계에서 중요한 판단 기준

어노테이션이 없는 클래스는 같은 패키지에 있어도 탐색 대상이 아니다. 빈으로 쓰려면 반드시 `@Component` 계열 어노테이션을 붙여야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**@ComponentScan으로 패키지를 지정하면, Spring이 그 안의 @Component 계열 클래스를 알아서 찾아 빈으로 등록한다.**

## 나중에 더 깊게 들어가면

- 필터를 이용한 탐색 범위 세부 제어 (`includeFilters`, `excludeFilters`)
- Spring Boot에서 @SpringBootApplication이 @ComponentScan을 포함하는 방식
- 수동 빈 등록(@Bean)과 자동 등록 충돌 시 우선순위 규칙

---

**원본:** [Component Scan Introduced — https://memoryhub.tistory.com/148](https://memoryhub.tistory.com/148)
