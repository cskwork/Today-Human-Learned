# NestJS vs ExpressJS — 언제 무엇을 쓸까

> **TL;DR**
> ExpressJS는 자유롭지만 구조를 직접 설계해야 하고, NestJS는 구조가 정해져 있어 팀 프로젝트나 대형 앱에 적합하다.

---

## 두 프레임워크를 왜 비교하는지 감 잡기

Node.js로 서버를 만들 때 가장 먼저 만나는 선택이 바로 이 둘이다. ExpressJS는 Node.js에서 가장 오래된 웹 프레임워크로, 라우팅과 미들웨어만 제공하고 나머지는 개발자가 직접 결정한다. NestJS는 ExpressJS 위에서 동작하지만 모듈, 의존성 주입, TypeScript 지원 같은 구조를 미리 잡아준다.

기능 차이보다 중요한 것은 "팀 규모와 프로젝트 수명"이다. 작고 빠른 API라면 Express, 유지보수가 길고 팀이 여러 명이라면 NestJS가 더 잘 맞는다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Express(자유 설계) vs NestJS(정해진 구조 + 자동 주입)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Middleware | 요청이 라우터에 도달하기 전에 거치는 중간 처리 함수 |
| Dependency Injection | 필요한 객체를 직접 만들지 않고 프레임워크가 대신 넣어주는 방식 |
| Opinionated | 코드 구조와 패턴을 프레임워크가 강제하는 성향 (NestJS가 이 쪽) |
| Unopinionated | 구조를 개발자가 자유롭게 결정하는 성향 (Express가 이 쪽) |
| TypeScript 지원 | 타입 오류를 실행 전에 잡아주는 기능. NestJS는 기본, Express는 별도 설정 필요 |

## 예를 들어 설명하면

같은 GET /books 엔드포인트를 두 프레임워크로 만들면 차이가 명확하다.

```js
// ExpressJS — 직접 라우트 정의
const express = require('express');
const app = express();

app.get('/books', (req, res) => {
  res.json([{ id: 1, title: '1984' }]);
});

app.listen(3000);
```

```ts
// NestJS — 역할별 파일 분리
@Controller('books')
export class BooksController {
  constructor(private readonly booksService: BooksService) {}

  @Get()
  findAll() {
    return this.booksService.findAll();
  }
}
```

Express는 파일 하나에 모든 것을 담을 수 있다. NestJS는 Controller와 Service를 반드시 나눈다. 코드가 늘어날수록 NestJS의 강제 분리가 오히려 편리해진다.

## 이 단계에서 중요한 판단 기준

혼자 빠르게 프로토타입을 만든다면 Express, 팀 개발 또는 장기 유지보수가 예상된다면 NestJS를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**ExpressJS는 자유도가 높은 도구 상자이고, NestJS는 구조가 내장된 조립 키트다.**

## 나중에 더 깊게 들어가면

- NestJS에서 Fastify로 HTTP 어댑터를 교체하는 방법
- Express 미들웨어를 NestJS에서 그대로 재사용하는 방법
- 두 프레임워크의 실제 성능 벤치마크 비교

---

**원본:** [NestJS VS ExpressJS](https://memoryhub.tistory.com/287)
