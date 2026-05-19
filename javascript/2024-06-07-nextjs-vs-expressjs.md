# Next.js vs Express.js — 언제 뭘 써야 하는가

> **TL;DR**
> Express.js는 백엔드 API 서버 전용 도구이고, Next.js는 React 프론트엔드와 간단한 API를 한 프로젝트에서 다루는 풀스택 프레임워크다.

---

## 두 프레임워크를 왜 구분해야 하는지 감 잡기

둘 다 Node.js 위에서 돌아가고, 둘 다 HTTP 요청을 처리한다. 그래서 처음에는 "뭘 쓰면 되지?"라는 혼란이 생긴다.

Express.js는 라우트와 미들웨어만 제공하는 최소 도구다. 렌더링, 인증, DB 연결 등 나머지는 개발자가 직접 구성한다. Next.js는 React 기반 UI 렌더링(SSR/SSG/CSR)과 파일 기반 라우팅, API Route를 한 묶음으로 제공한다. 프론트엔드가 필요 없는 순수 API 서버라면 Express.js가 더 가볍고 유연하다. React UI와 API를 함께 개발한다면 Next.js가 설정 부담을 줄여준다.

초보자는 처음에 이렇게 이해하면 된다.

`Express.js: 백엔드 API만 → Next.js: 프론트(React) + API 함께`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 라우팅 방식 | Express는 코드로 직접 정의, Next.js는 파일 이름이 곧 URL |
| 미들웨어 생태계 | Express는 수백 개의 npm 미들웨어를 자유롭게 조합할 수 있음 |
| API Route | Next.js의 `pages/api/` 폴더에 파일을 두면 API 엔드포인트가 됨 |
| 렌더링 내장 여부 | Express는 렌더링 기능 없음(직접 연결), Next.js는 SSR/SSG/CSR 내장 |
| Opinionated vs Unopinionated | Next.js는 구조를 강제하고(opinionated), Express는 구조를 개발자에게 맡김 |

## 예를 들어 설명하면

같은 "GET /api/hello" 엔드포인트를 두 프레임워크로 만들면 차이가 바로 보인다.

```js
// Express.js
const express = require('express');
const app = express();
app.get('/api/hello', (req, res) => {
  res.json({ message: 'Hello from Express' });
});
app.listen(3000);
```

```js
// Next.js — pages/api/hello.js 파일을 만들면 끝
export default function handler(req, res) {
  res.status(200).json({ message: 'Hello from Next.js' });
}
```

Express는 서버 파일을 직접 작성하고 실행한다. Next.js는 파일을 올바른 위치에 두는 것만으로 라우트가 생긴다.

## 이 단계에서 중요한 판단 기준

프론트엔드가 React이고 팀이 풀스택을 하나의 저장소에서 관리하고 싶다면 Next.js, 프론트엔드와 완전히 분리된 API 서버가 필요하다면 Express.js를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Express.js는 API 서버를 자유롭게 구성할 때, Next.js는 React UI와 API를 하나의 프로젝트로 빠르게 개발할 때 선택한다.**

## 나중에 더 깊게 들어가면

- Next.js App Router에서 Server Actions으로 API Route를 대체하는 패턴
- Express.js에서 MVC 구조를 직접 설계하는 방법
- 두 프레임워크를 함께 쓰는 BFF(Backend for Frontend) 아키텍처

---

**원본:** [NextJS vs ExpressJS](https://memoryhub.tistory.com/206)
