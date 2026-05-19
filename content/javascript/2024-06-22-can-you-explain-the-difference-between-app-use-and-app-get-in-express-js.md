+++
title = "Express.js — app.use()와 app.get()의 차이"
date = "2024-06-22"
description = "`app.use()`는 모든 요청에 걸리는 미들웨어용이고, `app.get()`은 GET 요청의 특정 경로에만 응답하는 라우트 핸들러다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> `app.use()`는 모든 요청에 걸리는 미들웨어용이고, `app.get()`은 GET 요청의 특정 경로에만 응답하는 라우트 핸들러다.

---

## 두 메서드를 왜 구분해야 하는지 감 잡기

Express에서 요청이 들어오면 등록된 함수들을 위에서 아래로 순서대로 거친다. `app.use()`와 `app.get()`은 둘 다 이 흐름에 함수를 끼워 넣는데, 적용되는 조건이 다르다. 이 차이를 모르면 미들웨어가 예상치 못한 요청에도 실행되거나, 반대로 실행되어야 할 곳에서 실행되지 않는 버그가 생긴다.

`app.use()`는 HTTP 메서드를 가리지 않는다. GET이든 POST든 DELETE든 경로만 맞으면 실행된다. 반면 `app.get()`은 GET 요청이고 경로가 정확히 일치할 때만 실행된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 요청 도착 → app.use() 전역 미들웨어 → 경로 매칭 → app.get()/app.post() 라우트 핸들러`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Middleware | 요청과 응답 사이에 끼워 넣는 함수. 로깅, 인증, 파싱 등에 쓴다. |
| Route Handler | 특정 경로와 HTTP 메서드에만 응답하는 함수 |
| next() | 현재 미들웨어에서 다음 함수로 제어를 넘기는 호출 |
| 경로 접두사 매칭 | `app.use('/user', ...)` 는 `/user`, `/user/profile` 모두에 매칭된다 |
| 정확한 경로 매칭 | `app.get('/user', ...)` 는 `/user`에만 매칭된다. `/user/profile`은 해당 안 됨 |

## 예를 들어 설명하면

아래 코드에서 각 함수가 언제 실행되는지 추적하면 차이가 바로 보인다.

```js
const express = require('express');
const app = express();

// 1. 모든 요청에서 실행 — HTTP 메서드 무관
app.use((req, res, next) => {
  console.log('요청 시각:', Date.now());
  next();
});

// 2. /user 또는 /user/... 경로의 모든 메서드에서 실행
app.use('/user', (req, res, next) => {
  console.log('user 경로 접근:', req.method);
  next();
});

// 3. GET /user 정확히 일치할 때만 실행
app.get('/user', (req, res) => {
  res.send('GET /user 응답');
});

// 4. GET /user 이후에 선언되었으므로, GET /user 요청에는 실행 안 됨
// (이미 3번에서 응답이 완료되었기 때문)
app.use('/user', (req, res, next) => {
  console.log('이 줄은 GET /user에서 실행되지 않는다');
  next();
});

app.listen(3000);
```

GET /user 요청이 들어오면 1번 → 2번 → 3번 순으로 실행되고 응답이 완료된다. 4번은 실행되지 않는다.

## 이 단계에서 중요한 판단 기준

여러 라우트에 공통으로 적용할 로직(로깅, 인증 확인, JSON 파싱)은 `app.use()`에, 특정 엔드포인트 하나에만 응답할 로직은 `app.get()` 같은 라우트 메서드에 넣는다.

## 한 줄 요약 — 이것만 기억하면 된다

**`app.use()`는 공통 미들웨어, `app.get()`은 특정 GET 엔드포인트 — 역할에 맞게 나눠 쓰는 것이 Express 라우팅의 기본이다.**

## 나중에 더 깊게 들어가면

- `express.Router()`로 라우트를 모듈 단위로 분리하기
- 오류 처리 미들웨어 (`(err, req, res, next)` 4인자 형태)
- `app.all()`과 `app.use()`의 차이점

---

**원본:** [Can you explain the difference between app.use() and app.get() in Express.js?](https://memoryhub.tistory.com/308)
