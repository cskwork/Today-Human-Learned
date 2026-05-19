+++
title = "Prisma ORM — Node.js와 TypeScript를 위한 데이터베이스 도구"
date = "2024-06-12"
description = "Prisma는 `schema.prisma` 파일 하나를 기준으로 타입 안전한 DB 쿼리 코드를 자동 생성해주는 차세대 ORM이다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Prisma는 `schema.prisma` 파일 하나를 기준으로 타입 안전한 DB 쿼리 코드를 자동 생성해주는 차세대 ORM이다.

---

## Prisma를 왜 쓰는지 감 잡기

백엔드 코드에서 데이터베이스를 다룰 때 개발자는 두 가지 세계를 오가야 한다. 하나는 JavaScript 객체이고, 다른 하나는 SQL 테이블이다. 둘은 구조가 달라서 매번 변환 로직을 직접 짜야 한다. 기존 ORM들은 이 변환을 도와주지만 TypeScript 타입 정보를 제대로 활용하지 못해 런타임에 오류가 터지는 일이 잦았다.

Prisma는 `schema.prisma`라는 파일에 데이터 구조를 선언하면, 거기서 TypeScript 타입과 쿼리 함수까지 자동으로 만들어 준다. 코드 편집기에서 자동완성이 되고, 타입이 맞지 않으면 컴파일 단계에서 바로 오류를 알려준다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: schema.prisma 작성 → migrate 실행 → Prisma Client로 쿼리`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| ORM | 객체(코드)와 테이블(DB) 사이를 자동으로 이어주는 다리 |
| Prisma Schema | DB 구조와 연결 정보를 적어두는 단일 설정 파일(`schema.prisma`) |
| Prisma Client | 스키마에서 자동 생성되는 타입 안전 쿼리 함수 모음 |
| Prisma Migrate | 스키마 변경 내역을 실제 DB에 SQL 없이 반영해주는 도구 |
| Prisma Studio | DB 데이터를 브라우저에서 보고 편집하는 GUI 뷰어 |

## 예를 들어 설명하면

`User`와 `Post` 테이블을 만들고 싶다면 스키마를 이렇게 선언한다.

```prisma
// prisma/schema.prisma
datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}

model Post {
  id       Int    @id @default(autoincrement())
  title    String
  author   User?  @relation(fields: [authorId], references: [id])
  authorId Int?
}
```

이후 터미널에서 `npx prisma migrate dev --name init` 한 줄로 테이블이 생성되고 Prisma Client도 갱신된다. 코드에서는 이렇게 쓴다.

```ts
const user = await prisma.user.create({
  data: { email: 'alice@example.com', name: 'Alice' },
});
const users = await prisma.user.findMany({ include: { posts: true } });
```

`findMany`의 반환값이 이미 TypeScript 타입으로 추론되므로 별도로 인터페이스를 선언할 필요가 없다.

## 이 단계에서 중요한 판단 기준

TypeScript 프로젝트에서 DB 쿼리 타입 오류를 런타임이 아니라 편집기에서 잡고 싶다면 Prisma가 가장 빠른 선택이다.

## 한 줄 요약 — 이것만 기억하면 된다

**스키마 파일 하나로 DB 구조, 마이그레이션, 타입 안전 쿼리를 모두 관리하는 것이 Prisma의 핵심이다.**

## 나중에 더 깊게 들어가면

- Prisma의 관계 쿼리 (`include`, `select`, 중첩 create)
- `prisma migrate deploy`를 CI/CD 파이프라인에 연결하는 방법
- Prisma와 PostgreSQL/MySQL 조합에서의 성능 최적화

---

**원본:** [? Prisma ORM - Node.js와 TypeScript를 위한 데이터베이스 도구](https://memoryhub.tistory.com/285)
