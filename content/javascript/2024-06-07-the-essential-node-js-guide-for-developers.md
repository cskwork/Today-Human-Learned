+++
title = "Node.js 핵심 개념 — 개발자를 위한 실용 정리"
date = "2024-06-07"
description = "Node.js는 JavaScript를 서버에서 실행하는 런타임이며, 비동기 이벤트 드리븐 방식으로 네트워크 I/O에 강하다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Node.js는 JavaScript를 서버에서 실행하는 런타임이며, 비동기 이벤트 드리븐 방식으로 네트워크 I/O에 강하다.

---

## Node.js를 왜 쓰는지 감 잡기

기존에 JavaScript는 브라우저에서만 실행됐다. Node.js는 Chrome의 V8 엔진을 서버 환경에 이식해서 같은 언어로 프론트엔드와 백엔드를 모두 작성할 수 있게 했다.

단일 스레드지만 논블로킹 I/O 덕분에 파일 읽기, DB 조회, 네트워크 요청처럼 대기 시간이 긴 작업을 동시에 처리한다. API 서버, 실시간 채팅, CLI 도구, 빌드 파이프라인에서 광범위하게 쓰인다.

초보자는 처음에 이렇게 이해하면 된다.

`코드 작성(JS) → Node.js 실행(V8 엔진) → 이벤트 루프가 I/O 완료 감지 → 콜백 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 모듈 (Module) | 기능을 파일 단위로 나눠 `require`나 `import`로 불러 쓰는 단위 |
| npm | Node.js의 패키지 관리자로, 외부 라이브러리를 설치·관리한다 |
| 이벤트 루프 | 비동기 작업 완료를 감지하고 등록된 콜백을 순서대로 실행하는 메커니즘 |
| `package.json` | 프로젝트 이름, 버전, 의존성 목록을 기록하는 설정 파일 |
| Express.js | Node.js에서 HTTP 서버와 API를 빠르게 만들기 위한 미니멀 웹 프레임워크 |

## 예를 들어 설명하면

Node.js의 내장 `http` 모듈만으로 서버를 만들면 어떤 코드가 필요한지 보자.

```js
const http = require('http');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello, World!\n');
});

server.listen(3000, () => {
  console.log('서버가 3000번 포트에서 실행 중');
});
```

실무에서는 이 위에 Express.js를 얹어 라우팅과 미들웨어를 추가한다. npm으로 패키지를 설치하고 `package.json`이 의존성을 추적한다.

```bash
npm install express   # express를 설치하고 package.json에 기록
```

## 이 단계에서 중요한 판단 기준

Node.js는 I/O 대기가 많은 서비스에 적합하지만, CPU를 오래 점유하는 이미지 처리나 암호화 연산이 많다면 다른 런타임이나 Worker Threads를 검토해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Node.js는 JavaScript를 서버에서 실행하며, 이벤트 루프 기반 논블로킹 I/O로 적은 자원으로 많은 요청을 처리한다.**

## 나중에 더 깊게 들어가면

- ESM(`import`/`export`)과 CommonJS(`require`) 모듈 시스템의 차이
- Mongoose, Prisma 등 ORM을 활용한 DB 연결 패턴
- Socket.IO로 실시간 양방향 통신 구현하기

---

**원본:** [The Essential Node.js Guide for Developers](https://memoryhub.tistory.com/208)
