+++
title = "@ComponentScan에서 basePackages를 지정하는 방법"
date = "2024-05-29"
description = "`@ComponentScan(basePackages = \"패키지경로\")`로 탐색 범위를 명시하며, 지정하지 않으면 해당 설정 클래스가 위치한 패키지가 기본 탐색 범위가 된다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> `@ComponentScan(basePackages = "패키지경로")`로 탐색 범위를 명시하며, 지정하지 않으면 해당 설정 클래스가 위치한 패키지가 기본 탐색 범위가 된다.

---

## basePackages 지정을 왜 하는지 감 잡기

Component Scan은 패키지 단위로 탐색한다. 범위를 너무 넓게 잡으면 관계없는 클래스까지 빈으로 등록되고, 너무 좁히면 필요한 빈이 누락된다. `basePackages` 속성으로 정확히 어느 패키지를 볼지 명시하면 탐색이 예측 가능해지고 애플리케이션 시작 속도도 유지된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: @Configuration 클래스 작성 → @ComponentScan에 패키지 지정 → Spring이 그 범위만 탐색 → 해당 패키지의 빈 등록`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| basePackages | 탐색할 패키지 경로를 문자열로 지정하는 @ComponentScan 속성 |
| basePackageClasses | 문자열 대신 클래스를 지정하는 방식. 리팩터링 시 오타 위험이 없다 |
| @Configuration | 이 클래스가 Spring 설정을 담고 있음을 나타내는 어노테이션 |
| 기본 패키지 (Default Package) | basePackages 생략 시 @ComponentScan이 붙은 클래스의 패키지 |
| 와일드카드 패턴 | `com.example.*`처럼 하위 패키지를 일괄 포함시키는 표기법 |

## 예를 들어 설명하면

패키지 구조가 아래와 같을 때 두 패키지만 탐색하고 싶다.

```
com.example.app
com.example.services
com.example.controllers  <- 이건 탐색 제외
```

설정 클래스에 두 패키지를 배열로 넘긴다.

```java
@Configuration
@ComponentScan(basePackages = {"com.example.app", "com.example.services"})
public class AppConfig { }
```

`basePackages`를 아예 생략하면 `AppConfig`가 위치한 패키지(예: `com.example`)를 기본으로 탐색한다. 실무에서는 Spring Boot의 `@SpringBootApplication`이 이 기본 동작을 이용하므로, 메인 클래스를 최상위 패키지에 두는 관행이 생겼다.

## 이 단계에서 중요한 판단 기준

문자열 경로는 오타가 나도 컴파일 에러가 나지 않는다. 패키지 경로가 바뀔 가능성이 있다면 `basePackageClasses`에 해당 패키지의 마커 클래스를 지정해 리팩터링 안전성을 높이는 편이 낫다.

## 한 줄 요약 — 이것만 기억하면 된다

**`@ComponentScan(basePackages = {...})`로 탐색 범위를 명시하고, 생략하면 설정 클래스의 패키지가 자동으로 탐색 범위가 된다.**

## 나중에 더 깊게 들어가면

- `basePackageClasses`를 이용한 타입 안전 패키지 지정
- `includeFilters`, `excludeFilters`로 특정 클래스나 어노테이션 제외
- Spring Boot에서 @SpringBootApplication의 기본 스캔 동작 원리

---

**원본:** [How do you specify the base packages for component scanning in a Spring configuration class? — https://memoryhub.tistory.com/149](https://memoryhub.tistory.com/149)
