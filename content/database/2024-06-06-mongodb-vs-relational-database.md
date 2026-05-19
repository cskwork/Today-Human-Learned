+++
title = "MongoDB vs 관계형 데이터베이스 — 언제 뭘 써야 하나"
date = "2024-06-06"
description = "관계형 DB는 데이터를 고정된 표 형태로 관리하고, MongoDB는 구조가 달라도 되는 문서(JSON) 형태로 저장한다. 데이터 모양이 미리 확정된다면 관계형, 자주 바뀐다면 MongoDB가 낫다."
tags = ["database"]
categories = ["database"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 관계형 DB는 데이터를 고정된 표 형태로 관리하고, MongoDB는 구조가 달라도 되는 문서(JSON) 형태로 저장한다. 데이터 모양이 미리 확정된다면 관계형, 자주 바뀐다면 MongoDB가 낫다.

---

## 두 방식을 왜 나눠서 보는지 감 잡기

데이터베이스를 고를 때 가장 먼저 묻는 질문은 "데이터의 구조가 고정돼 있는가?"다. 사용자 테이블처럼 열(column)이 고정된 데이터는 관계형 DB가 강점이다. 반면 상품 정보처럼 옵션 수나 종류가 상품마다 다른 경우에는 고정 스키마가 오히려 불편하다. MongoDB는 이런 유연성이 필요한 상황을 위해 설계됐다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 데이터 구조 확정 여부 → 고정이면 관계형 DB → 유동적이면 MongoDB`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 스키마(Schema) | 데이터를 저장할 열의 이름과 타입을 미리 정해두는 설계도. 관계형 DB는 이게 고정이다. |
| 컬렉션(Collection) | MongoDB에서 비슷한 문서를 담는 묶음. 관계형 DB의 테이블에 해당한다. |
| 문서(Document) | MongoDB의 데이터 한 건. JSON 형태이며 안에 중첩 구조를 가질 수 있다. |
| 외래 키(Foreign Key) | 관계형 DB에서 테이블 간 관계를 연결하는 참조 값. 조인(JOIN)의 기반이다. |
| BSON | MongoDB가 내부적으로 쓰는 이진 JSON. 저장과 검색 효율을 높이기 위한 형식이다. |

## 예를 들어 설명하면

책과 저자 정보를 저장한다고 가정한다.

**관계형 DB (MySQL / PostgreSQL)**

```
Books 테이블: BookID | Title         | AuthorID | Year
Authors 테이블: AuthorID | Name       | BirthYear
```

Books의 AuthorID가 Authors 테이블을 참조한다. 구조가 명확하고, 조인으로 저자 이름을 한 번에 조회할 수 있다.

**MongoDB**

```json
// 책 문서 1: 저자 정보를 직접 내포
{ "title": "양자역학 입문", "author": { "name": "김철수", "birthYear": 1970 }, "year": 2020 }

// 책 문서 2: 장르 필드가 추가됨
{ "title": "고전역학", "author": "이영희", "year": 2019, "genres": ["물리", "교육"] }
```

두 문서의 구조가 달라도 같은 컬렉션에 저장할 수 있다. 저자 정보를 내포(embed)하거나 별도 컬렉션으로 참조하는 방식을 상황에 따라 선택한다.

## 이 단계에서 중요한 판단 기준

복잡한 조인이 자주 필요하거나 데이터 정합성(트랜잭션)이 매우 중요한 서비스라면 관계형 DB를 선택한다. 데이터 구조가 자주 바뀌거나 중첩된 계층 데이터가 많다면 MongoDB가 편하다.

## 한 줄 요약 — 이것만 기억하면 된다

**관계형 DB는 구조가 고정된 데이터를 엄밀하게 관리하고, MongoDB는 구조가 다양한 문서를 유연하게 저장한다.**

## 나중에 더 깊게 들어가면

- MongoDB의 임베딩(embedding) vs 참조(referencing) 설계 패턴
- 관계형 DB의 정규화(Normalization)와 성능 트레이드오프
- MongoDB 트랜잭션 지원 범위와 한계

---

**원본:** [MongoDB vs Relational Database — https://memoryhub.tistory.com/194](https://memoryhub.tistory.com/194)
