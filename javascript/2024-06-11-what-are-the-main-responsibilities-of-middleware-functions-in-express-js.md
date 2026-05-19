# Express.js 미들웨어 — 요청이 응답이 되기까지 거치는 관문

> **TL;DR**
> 미들웨어는 요청(req)과 응답(res) 사이에서 실행되는 함수이고, `next()`를 호출해야 다음 단계로 넘어간다.

---

## 미들웨어를 왜 쓰는지 감 잡기

클라이언트 요청이 서버에 도달하면 바로 비즈니스 로직으로 가지 않는다. 그 전에 처리해야 할 일이 많다. 요청 본문을 파싱하고, 로그를 남기고, 사용자가 로그인했는지 확인하고, 에러가 생기면 잡아서 응답한다.

이 작업들을 하나의 라우트 핸들러에 다 넣으면 코드가 금방 복잡해진다. Express는 이 작업들을 함수 단위로 쪼개 순서대로 실행하는 구조를 제공한다. 각 함수가 하나의 책임만 지고, 다음 함수로 제어를 넘긴다. 이것이 미들웨어 체인이다.

`핵심 흐름: 요청 → 미들웨어1 → 미들웨어2 → ... → 라우트 핸들러 → 응답`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 미들웨어 함수 | `(req, res, next)` 세 인자를 받는 함수. 요청 처리 중간에 끼어든다. |
| next() | 다음 미들웨어로 제어를 넘기는 함수. 호출 안 하면 요청이 거기서 멈춘다. |
| app.use() | 미들웨어를 전역으로 등록할 때 쓴다. 등록 순서가 실행 순서다. |
| 에러 핸들링 미들웨어 | `(err, req, res, next)` 네 인자. 다른 미들웨어에서 next(err)를 호출하면 이곳으로 건너뛴다. |
| 바디 파서 | 요청 본문(JSON, 폼 데이터)을 `req.body`로 파싱해주는 미들웨어. |

## 예를 들어 설명하면

로그 기록 → 인증 확인 → 에러 처리 세 가지 미들웨어가 체인으로 연결된 예제다.

```js
const express = require('express');
const app = express();

// 1. 로깅 미들웨어 — 모든 요청을 기록
function logRequests(req, res, next) {
  console.log(`${req.method} ${req.url}`);
  next(); // 다음 미들웨어로 넘긴다
}

// 2. 인증 미들웨어 — 로그인 여부 확인
function checkAuth(req, res, next) {
  if (req.headers['authorization']) {
    next(); // 인증 통과
  } else {
    res.status(401).json({ error: 'Unauthorized' }); // 여기서 응답 종료
  }
}

// 3. 에러 핸들링 미들웨어 — 반드시 인자 4개
function errorHandler(err, req, res, next) {
  console.error(err.stack);
  res.status(500).json({ error: err.message });
}

app.use(logRequests);
app.use(checkAuth);
app.use(errorHandler); // 에러 핸들러는 마지막에

app.get('/', (req, res) => {
  res.send('Hello');
});

app.listen(3000);
```

`checkAuth`에서 `next()`를 호출하지 않고 `res.status(401)`로 응답하면 체인이 거기서 끊긴다. 이처럼 미들웨어는 요청을 계속 흘려보내거나 직접 응답을 끊는 역할을 한다.

## 이 단계에서 중요한 판단 기준

미들웨어 안에서 `next()`를 호출했는지, 아니면 `res`로 응답을 보냈는지 항상 확인하라. 둘 다 빠뜨리면 요청이 영원히 대기 상태가 된다.

## 한 줄 요약 — 이것만 기억하면 된다

**미들웨어는 요청과 응답 사이의 처리 단계이고, next()로 체인을 이어가거나 res로 직접 응답해 체인을 끊는다.**

## 나중에 더 깊게 들어가면

- 특정 라우트에만 적용하는 라우터 레벨 미들웨어 (`router.use()`)
- async 미들웨어에서 에러를 잡아 next(err)로 전달하는 패턴
- 서드파티 미들웨어(helmet, cors, morgan) 활용법

---

**원본:** [What are the main responsibilities of middleware functions in Express.js? — https://memoryhub.tistory.com/272](https://memoryhub.tistory.com/272)
