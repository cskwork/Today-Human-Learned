+++
title = "Express.js — Node.js 웹 서버를 쉽게 만드는 프레임워크"
date = "2024-06-06"
description = "Express.js는 Node.js로 HTTP 서버와 API를 만들 때 반복 작업을 줄여주는 미니멀 프레임워크다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Express.js는 Node.js로 HTTP 서버와 API를 만들 때 반복 작업을 줄여주는 미니멀 프레임워크다.

---

## Express.js를 왜 쓰는지 감 잡기

Node.js에는 `http` 모듈이 기본 내장되어 있다. 그런데 이 모듈만으로 서버를 만들면 URL 패턴 처리, JSON 파싱, 에러 응답 같은 코드를 모두 직접 짜야 한다. 코드가 길어지고 실수가 늘어난다.

Express.js는 이 반복 작업을 미리 묶어놓은 라이브러리다. "이 URL로 GET 요청이 오면 이걸 돌려줘"처럼 의도를 한 줄로 표현할 수 있다. Node.js 기반 API 서버, REST 백엔드, BFF(Backend for Frontend) 등에 폭넓게 쓰인다.

초보자는 처음에 이렇게 이해하면 된다.

`요청 도착 → 미들웨어 통과 → 라우터 처리 → 응답 전송`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 라우트 (Route) | URL + HTTP 메서드 조합에 어떤 코드를 실행할지 지정하는 규칙 |
| 미들웨어 (Middleware) | 요청이 라우트에 닿기 전에 거치는 처리 단계 (로깅, 인증 등) |
| req / res | 들어온 요청 정보(req)와 응답을 만드는 도구(res) |
| `app.use()` | 특정 경로 또는 전체 요청에 미들웨어를 연결하는 메서드 |
| 에러 핸들러 | 인자가 4개인 미들웨어 `(err, req, res, next)`로 에러를 일괄 처리 |

## 예를 들어 설명하면

사용자 목록을 반환하는 간단한 API를 만든다고 가정하자.

```js
const express = require('express');
const app = express();

// 미들웨어: 모든 요청의 메서드와 URL을 기록
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next(); // 다음 단계로 넘김
});

// 라우트: GET /api/users
app.get('/api/users', (req, res) => {
  res.json([{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }]);
});

// 에러 핸들러
app.use((err, req, res, next) => {
  res.status(500).send('서버 오류가 발생했습니다.');
});

app.listen(3000);
```

요청이 들어오면 로깅 미들웨어를 통과하고, `/api/users` 라우트가 JSON을 돌려준다. 에러가 생기면 에러 핸들러가 가로챈다.

## 이 단계에서 중요한 판단 기준

Express.js는 "무엇을 어떻게 쌓을지"를 개발자가 직접 결정해야 하는 프레임워크이므로, 팀의 설계 능력이 코드 품질을 좌우한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Express.js는 Node.js HTTP 서버의 반복 코드를 라우트와 미들웨어로 정리해주는 도구다.**

## 나중에 더 깊게 들어가면

- 미들웨어 실행 순서와 `next()` 호출 타이밍
- Router 객체로 라우트를 모듈별로 분리하는 방법
- Passport.js, express-validator 같은 생태계 미들웨어 활용

---

**원본:** [ExpressJS Introduced](https://memoryhub.tistory.com/195)
