# MERN 스택 — JavaScript 하나로 프론트부터 백엔드까지

> **TL;DR**
> MERN은 MongoDB, Express.js, React, Node.js 네 기술을 엮어 JavaScript만으로 전체 웹 서비스를 만드는 조합이다.

---

## MERN 스택을 왜 쓰는지 감 잡기

웹 서비스를 만들려면 데이터 저장, 서버 로직, 화면 표시, 이 세 가지가 모두 필요하다. 과거에는 각 영역마다 다른 언어를 썼다. MERN은 이 모든 영역을 JavaScript 하나로 처리한다. 팀이 작거나 풀스택 개발자 한 명이 전부를 담당할 때 특히 효율적이다.

MongoDB는 데이터를 JSON 형태로 저장하고, Node.js와 Express.js는 서버를 처리하며, React는 화면을 그린다. 네 기술 모두 JavaScript 생태계 안에 있으므로 언어 전환 없이 일할 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 브라우저(React) → 요청 → Express.js 라우터 → Node.js 서버 → MongoDB 데이터 조회 → 응답`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MongoDB | 테이블 대신 JSON 문서 단위로 데이터를 저장하는 NoSQL 데이터베이스. |
| Express.js | Node.js 위에서 동작하는 서버 프레임워크. URL 요청을 받아 어떤 코드를 실행할지 연결해 준다. |
| React | 화면을 구성하는 UI 컴포넌트 라이브러리. 데이터가 바뀌면 필요한 부분만 다시 그린다. |
| Node.js | 브라우저 밖에서 JavaScript를 실행하는 런타임. 서버가 Node.js 위에서 돌아간다. |
| REST API | 클라이언트와 서버가 HTTP로 데이터를 주고받는 통신 방식. Express.js가 이 창구를 만든다. |

## 예를 들어 설명하면

블로그 앱을 만든다고 가정하면 각 기술의 역할은 이렇다.

- **MongoDB**: 블로그 게시글을 `{ title, content, author, date }` 형태의 문서로 저장한다.
- **Express.js**: `GET /posts` 요청이 오면 MongoDB에서 게시글 목록을 가져와 JSON으로 응답한다.
- **React**: 응답받은 JSON을 화면에 목록으로 뿌리고, 새 글 작성 폼을 제공한다.
- **Node.js**: Express.js 앱이 실행되는 서버 환경이다.

사용자가 "새 글 작성" 버튼을 누르면 React가 Express.js로 POST 요청을 보내고, Express.js는 MongoDB에 문서를 저장한다.

## 이 단계에서 중요한 판단 기준

데이터 구조가 자주 바뀌거나 팀 전체가 JavaScript를 쓴다면 MERN이 빠른 선택이다. 복잡한 관계형 데이터(외래 키, 조인이 많은 경우)가 핵심이라면 PostgreSQL 기반 스택을 먼저 검토한다.

## 한 줄 요약 — 이것만 기억하면 된다

**MERN은 JavaScript 하나로 데이터베이스부터 화면까지 전부 만드는 풀스택 조합이다.**

## 나중에 더 깊게 들어가면

- Next.js로 React의 서버 사이드 렌더링(SSR) 더하기
- MongoDB의 Aggregation Pipeline로 복잡한 데이터 집계하기
- JWT를 이용한 Express.js 인증 미들웨어 작성

---

**원본:** [MERN Stack Introduced — https://memoryhub.tistory.com/193](https://memoryhub.tistory.com/193)
