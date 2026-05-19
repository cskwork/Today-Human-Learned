+++
title = "Q Developer Transform — 레거시 마이그레이션 자동화"
date = "2025-09-21"
description = "Amazon Q Developer Transform은 Java 버전 업그레이드와 Oracle-to-PostgreSQL SQL 변환을 AI가 자동으로 처리하는 코드 변환 에이전트다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Amazon Q Developer Transform은 Java 버전 업그레이드와 Oracle-to-PostgreSQL SQL 변환을 AI가 자동으로 처리하는 코드 변환 에이전트다.

---

## Q Developer Transform을 왜 쓰는지 감 잡기

기업에는 10년 이상 된 Java 8 애플리케이션이 많다. 이를 Java 21로 올리려면 deprecated API를 하나씩 찾아 교체하고, 의존성 버전을 맞추고, 변경 이후 테스트를 다시 짜야 한다. 규모에 따라 수주에서 수개월이 걸린다.

Q Developer Transform은 이 과정을 자동화한다. IDE 또는 CLI에서 명령 하나로 코드 분석, 변환 계획 생성, 자동 코드 수정까지 진행한다. DB 마이그레이션도 DMS Schema Conversion 메타데이터를 활용해 애플리케이션 내 SQL까지 함께 변환한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 코드 분석 → 변환 계획 생성 → 자동 코드 수정 → 빌드 및 테스트`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Q Developer Transform | Amazon Q Developer 안에 있는 코드 변환 에이전트. IDE와 CLI 모두 지원한다. |
| Deprecated API | 더 이상 권장하지 않는 구버전 함수나 클래스. 새 버전으로 올리면 컴파일 오류가 난다. |
| DMS Schema Conversion | AWS 데이터 마이그레이션 서비스의 스키마 변환 도구. DB 구조를 분석해 메타데이터를 만든다. |
| 임베디드 SQL | Java 코드 안에 문자열로 작성된 SQL 쿼리. 자동 변환 대상 중 가장 번거로운 부분이다. |
| /transform 명령 | IntelliJ나 VS Code의 Amazon Q 채팅창에 입력하면 변환 프로세스가 시작되는 명령어. |

## 예를 들어 설명하면

Java 8에서 Java 21로 올릴 때 날짜 처리 코드가 자동으로 바뀌는 예시다.

```java
// 변환 전 — Java 8 방식 (Deprecated)
Date date = new Date();
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");

// 변환 후 — Java 21 방식
LocalDate date = LocalDate.now();
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
```

Oracle SQL 변환 예시도 직관적이다.

```java
// 변환 전 — Oracle 방언
String query = "SELECT * FROM employees WHERE ROWNUM <= 10";

// 변환 후 — PostgreSQL 방언
String query = "SELECT * FROM employees LIMIT 10";
```

IDE에서는 `/transform` 명령 하나로, CLI에서는 아래 명령으로 실행한다.

```bash
q-transform java-upgrade \
  --source-version 8 \
  --target-version 21 \
  --project-path ./my-app \
  --output ./transformed-app
```

## 이 단계에서 중요한 판단 기준

Q Developer Transform는 Maven 기반 Java 프로젝트에서 효과가 가장 크다. Gradle 프로젝트나 Java 외 언어는 지원 범위를 먼저 확인해야 한다. 자동 변환 후에도 반드시 전체 테스트를 돌려 회귀를 검증한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Q Developer Transform는 Java 버전 업그레이드와 DB SQL 변환을 수동 작업 없이 자동화해, 수개월짜리 마이그레이션을 수일 안으로 줄인다.**

## 나중에 더 깊게 들어가면

- Java 21의 Virtual Threads와 Record Classes가 기존 코드에 미치는 영향
- Jenkins Pipeline에 `/transform` CLI를 통합해 대규모 포트폴리오 일괄 처리하기
- DMS Schema Conversion으로 저장 프로시저와 함수까지 변환하는 과정

---

**원본:** [Q Developer Transform, 엔터프라이즈 마이그레이션이 이렇게 쉬워도 되나요 — https://memoryhub.tistory.com/782]
