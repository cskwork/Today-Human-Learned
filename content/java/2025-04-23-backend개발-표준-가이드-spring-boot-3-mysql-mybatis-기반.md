+++
title = "Backend 개발 표준 가이드 — Spring Boot 3, MySQL, MyBatis 기반"
date = "2025-04-23"
description = "코드 규칙은 심미적 선택이 아니라 팀이 6개월 뒤 자기 코드를 읽을 수 있게 해주는 생존 도구다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 코드 규칙은 심미적 선택이 아니라 팀이 6개월 뒤 자기 코드를 읽을 수 있게 해주는 생존 도구다.

---

## 이 가이드를 왜 쓰는지 감 잡기

Spring Boot 프로젝트가 커지면 "누가 짰는지 모르겠는 코드"가 쌓인다. 패키지 이름이 제각각이고, API URL 스타일이 섞이고, DB 컬럼 명명이 뒤죽박죽이면 버그 하나 고치는 데 하루가 걸린다. 표준 가이드는 그 혼돈을 막는 팀 계약서다.

이 글은 Spring Boot 3, MySQL, MyBatis를 쓰는 백엔드 팀이 최소한 합의해야 할 네 가지 영역을 정리한다.

`핵심 흐름: 프로젝트 구조 → 코드 명명 → DB 스키마 → API 설계`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Package-by-Feature | 계층(controller/service)이 아니라 기능(user/order) 단위로 패키지를 나누는 방식 |
| snake_case | 단어를 밑줄로 연결하는 표기법. DB 컬럼과 파일명에 씀 |
| lowerCamelCase | 첫 단어 소문자, 이후 단어 첫 글자 대문자. Java 메서드·변수에 씀 |
| UpperCamelCase | 모든 단어 첫 글자 대문자. Java 클래스·인터페이스에 씀 |
| DECIMAL(M, D) | 금액처럼 정확한 소수점이 필요한 MySQL 타입. FLOAT·DOUBLE은 근사값이라 금융 계산에 쓰면 안 됨 |

## 예를 들어 설명하면

사용자-주문 기능이 있는 프로젝트를 Package-by-Feature로 구성하면 아래처럼 된다.

```
com.example.shop
├── user
│   ├── UserController.java      # GET /users/{id}
│   ├── UserService.java
│   ├── UserRepository.java
│   └── dto/UserResponse.java
├── order
│   ├── OrderController.java     # POST /orders
│   ├── OrderService.java
│   └── ...
└── common
    └── exception/GlobalExceptionHandler.java
```

DB 쪽 예시: 주문 테이블 컬럼은 `order_id`, `user_id`, `total_amount DECIMAL(10,2)`, `created_at TIMESTAMP`처럼 snake_case로 맞추고, API는 `GET /orders/{orderId}` — URI는 소문자 kebab-case, 응답 JSON 필드는 lowerCamelCase(`totalAmount`)로 통일한다.

## 이 단계에서 중요한 판단 기준

소규모 프로젝트는 Package-by-Layer가 빠르지만, 도메인이 3개 이상으로 늘어날 것 같다면 처음부터 Package-by-Feature로 시작하라 — 나중에 바꾸는 비용이 훨씬 크다.

## 한 줄 요약 — 이것만 기억하면 된다

**코드·DB·API 세 곳의 명명 규칙을 팀이 문서로 합의하고 일관되게 적용하는 것이 표준 가이드의 전부다.**

## 나중에 더 깊게 들어가면

- MyBatis 매퍼 XML 작성 규칙과 동적 SQL 패턴
- Spring Boot 예외 처리 전략 (`@ControllerAdvice`, 에러 응답 포맷)
- Flyway 또는 Liquibase를 활용한 DB 마이그레이션 관리

---

**원본:** [Backend개발 표준 가이드 - Spring Boot 3, MySQL, MyBatis 기반](https://memoryhub.tistory.com/562)
