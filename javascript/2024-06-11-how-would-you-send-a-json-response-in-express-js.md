# Express.js에서 JSON 응답 보내기

> **TL;DR**
> `res.json(객체)`를 호출하면 Content-Type을 application/json으로 설정하고 JavaScript 객체를 JSON 문자열로 변환해 클라이언트에 보낸다.

---

## JSON 응답을 왜 쓰는지 감 잡기

서버가 클라이언트에 데이터를 전달할 때 가장 보편적인 형식이 JSON이다. HTML은 사람이 보는 화면용이고, JSON은 앱 간 데이터 교환용이다. 프론트엔드 JavaScript, 모바일 앱, 다른 서버가 모두 JSON을 쉽게 파싱할 수 있다.

Express는 `res.json()` 메서드 하나로 이 변환과 헤더 설정을 자동으로 처리한다. 직접 `JSON.stringify()`를 호출하거나 Content-Type 헤더를 손으로 설정할 필요가 없다.

`핵심 흐름: JS 객체 → res.json() → JSON 문자열 + Content-Type 헤더 → 클라이언트`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| res.json() | 객체나 배열을 JSON으로 변환해 응답하는 Express 메서드. |
| Content-Type | HTTP 응답이 어떤 형식인지 알려주는 헤더. res.json()은 이걸 자동으로 application/json으로 설정한다. |
| HTTP 상태 코드 | 응답이 성공(200), 생성(201), 오류(400, 500)인지 나타내는 숫자. res.status(코드).json()으로 함께 보낸다. |
| JSON | JavaScript Object Notation. 키-값 쌍의 텍스트 형식. 언어에 관계없이 파싱 가능. |
| res.send() | 텍스트나 버퍼를 보낼 때 쓴다. 객체를 넘겨도 되지만 Content-Type 자동 설정은 res.json()이 더 명확하다. |

## 예를 들어 설명하면

기본 사용과 상태 코드를 함께 보내는 두 가지 패턴이 가장 자주 쓰인다.

```js
const express = require('express');
const app = express();

const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
];

// 기본 JSON 응답
app.get('/users', (req, res) => {
  res.json(users); // 200 OK + Content-Type: application/json
});

// 상태 코드와 함께
app.post('/users', (req, res) => {
  const newUser = { id: 3, name: 'Charlie' };
  res.status(201).json(newUser); // 201 Created
});

// 에러 응답
app.get('/users/:id', (req, res) => {
  const user = users.find(u => u.id === Number(req.params.id));
  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }
  res.json(user);
});

app.listen(3000);
```

`res.status(코드).json(객체)` 패턴으로 상태 코드와 JSON을 함께 보내는 것이 API 설계의 기본이다.

## 이 단계에서 중요한 판단 기준

성공이면 200 또는 201, 클라이언트 잘못이면 400 계열, 서버 문제면 500을 상태 코드로 설정하고 항상 JSON 형식의 에러 객체를 반환하라.

## 한 줄 요약 — 이것만 기억하면 된다

**`res.status(코드).json(객체)`가 Express API 응답의 표준 패턴이다.**

## 나중에 더 깊게 들어가면

- API 응답 포맷 통일 (success, data, error 필드를 항상 포함하는 봉투 패턴)
- `res.json()`과 `res.send()`의 차이와 각각 적합한 상황
- 큰 데이터셋을 JSON 스트리밍으로 응답하는 방법

---

**원본:** [How would you send a JSON response in Express.js? — https://memoryhub.tistory.com/273](https://memoryhub.tistory.com/273)
