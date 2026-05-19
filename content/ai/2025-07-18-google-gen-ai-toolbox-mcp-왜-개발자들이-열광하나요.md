+++
title = "Google Gen AI Toolbox — AI 에이전트가 데이터베이스를 안전하게 쓰는 방법"
date = "2025-07-18"
description = "AI가 SQL을 직접 생성하는 대신, 개발자가 미리 정의한 안전한 SQL 도구들 중에서 AI가 골라 실행하는 구조가 Google Gen AI Toolbox의 핵심이다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> AI가 SQL을 직접 생성하는 대신, 개발자가 미리 정의한 안전한 SQL 도구들 중에서 AI가 골라 실행하는 구조가 Google Gen AI Toolbox의 핵심이다.

---

## Google Gen AI Toolbox를 왜 쓰는지 감 잡기

AI에게 데이터베이스를 직접 연결하면 어떤 일이 생길까. AI가 자연어를 SQL로 변환할 때 잘못된 쿼리를 생성하거나, 민감한 자격 증명을 노출하거나, 예상치 못한 데이터를 삭제할 수 있다. 이것이 기존 LLM-데이터베이스 직접 연결 방식의 근본적 위험이다.

Google Gen AI Toolbox는 다른 접근을 택한다. 개발자가 `tools.yaml` 파일에 SQL 쿼리를 미리 작성해 "도구(tool)"로 등록하면, AI 에이전트는 그 도구 목록 안에서만 선택하고 실행한다. AI가 SQL을 만드는 것이 아니라, 이미 검증된 쿼리 중 하나를 고르는 것이다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 개발자가 SQL 도구 정의 → tools.yaml 등록 → AI 에이전트가 도구 선택 → 안전하게 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Tool | AI가 실행할 수 있는 미리 정의된 작업 하나 (예: "호텔 이름으로 검색") |
| Statement | 도구 안에 담긴 실제 SQL 쿼리 — 개발자가 직접 작성한다 |
| Parameters | AI가 도구를 실행할 때 전달하는 입력값 (예: 호텔 이름 "홀리데이 인") |
| Toolset | 관련 도구들을 묶은 그룹 (예: hotel-management 세트) |
| Template Parameters | 테이블명·컬럼명 같은 구조 자체를 동적으로 바꾸는 파라미터 — 보안 위험이 있어 신중하게 써야 한다 |

## 예를 들어 설명하면

호텔 예약 에이전트를 만든다고 하자. 개발자는 아래처럼 도구를 정의한다.

```yaml
tools:
  search-hotels-by-name:
    kind: postgres-sql
    source: my-pg-source
    description: Search for hotels based on name.
    statement: SELECT * FROM hotels WHERE name ILIKE '%' || $1 || '%'
    parameters:
      - name: name
        type: string

  book-hotel:
    kind: postgres-sql
    source: my-pg-source
    description: Book a hotel by its ID.
    statement: UPDATE hotels SET booked = B'1' WHERE id = $1
    parameters:
      - name: hotel_id
        type: string
```

사용자가 "홀리데이 인을 예약해줘"라고 하면, AI는 먼저 `search-hotels-by-name`으로 ID를 찾고, 다음에 `book-hotel`을 실행한다. AI가 SQL을 만드는 순간은 없다.

## 이 단계에서 중요한 판단 기준

Template Parameters보다 일반 Parameters를 기본으로 쓴다 — 유연성보다 보안과 성능이 우선이다.

## 한 줄 요약 — 이것만 기억하면 된다

**AI가 SQL을 생성하는 것이 아니라, 개발자가 미리 작성한 안전한 쿼리 중 하나를 선택해 실행한다.**

## 나중에 더 깊게 들어가면

- authRequired 필드와 OIDC 인증 파라미터로 사용자 인증 연동하기
- MCP(Model Context Protocol)와 Gen AI Toolbox를 함께 쓰는 방법
- AlloyDB, BigQuery 등 다른 데이터 소스 연결 방법

---

**원본:** [Google Gen AI Toolbox + MCP, 왜 개발자들이 열광하나요? — https://memoryhub.tistory.com/726](https://memoryhub.tistory.com/726)
