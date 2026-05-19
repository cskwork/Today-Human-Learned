+++
title = "Node.js — 브라우저 밖에서 실행되는 JavaScript"
date = "2024-06-06"
description = "Node.js는 JavaScript를 서버에서 실행할 수 있게 해주는 런타임으로, 비동기 I/O 덕분에 동시 요청을 적은 자원으로 처리한다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Node.js는 JavaScript를 서버에서 실행할 수 있게 해주는 런타임으로, 비동기 I/O 덕분에 동시 요청을 적은 자원으로 처리한다.

---

## Node.js를 왜 쓰는지 감 잡기

원래 JavaScript는 브라우저 안에서만 동작했다. 웹 페이지의 버튼 클릭을 처리하고 화면을 바꾸는 것이 전부였다. 2009년 Node.js가 등장하면서 JavaScript가 서버에서도 실행될 수 있게 됐다. 덕분에 프론트엔드와 백엔드를 같은 언어로 작성할 수 있고, 채팅이나 실시간 알림처럼 동시 연결이 많은 서비스에 특히 강하다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 클라이언트 요청 → Node.js 이벤트 루프 수신 → 비동기 처리(파일·DB·네트워크) → 완료 시 응답`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 런타임 (Runtime) | 코드를 실행할 수 있는 환경. Node.js는 브라우저 없이도 JavaScript를 실행한다. |
| V8 엔진 | Google이 만든 JavaScript 실행 엔진. Node.js가 이 엔진 위에서 동작한다. |
| 비동기 I/O | 파일 읽기나 네트워크 호출이 끝날 때까지 기다리지 않고 다른 작업을 먼저 처리하는 방식. |
| 이벤트 루프 | "어떤 작업이 끝났나?" 를 계속 확인하며 콜백을 실행하는 Node.js의 핵심 구조. |
| NPM | Node.js 생태계의 패키지 저장소. 오픈소스 라이브러리를 명령어 하나로 설치한다. |

## 예를 들어 설명하면

Express로 간단한 웹 서버를 만드는 최소 예시다.

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Hello, world!');
});

app.listen(3000, () => {
    console.log('Server running at http://localhost:3000/');
});
```

`node server.js` 로 실행하면 브라우저에서 `localhost:3000`을 열었을 때 텍스트가 응답된다. 이 서버는 요청 하나를 처리하는 동안 다른 요청도 동시에 받을 수 있다. 스레드를 여러 개 쓰지 않고도 가능한 것이 비동기 I/O의 핵심이다.

## 이 단계에서 중요한 판단 기준

Node.js는 동시 연결이 많고 I/O 대기가 긴 서비스(API 서버, 채팅, 스트리밍)에 적합하고, CPU를 집중적으로 쓰는 작업(영상 인코딩, 복잡한 수치 계산)에는 적합하지 않다.

## 한 줄 요약 — 이것만 기억하면 된다

**Node.js는 JavaScript를 서버로 확장시킨 런타임이며, 비동기 이벤트 루프로 적은 스레드로 많은 요청을 소화한다.**

## 나중에 더 깊게 들어가면

- 콜백 지옥과 Promise, async/await — 비동기 코드를 읽기 좋게 쓰는 방법
- 클러스터 모드 — CPU 코어를 여러 개 활용해 성능을 올리는 방법
- Node.js 스트림 — 대용량 파일을 메모리에 한꺼번에 올리지 않고 처리하는 방식

---

**원본:** [NodeJS Introduced — https://memoryhub.tistory.com/196](https://memoryhub.tistory.com/196)
