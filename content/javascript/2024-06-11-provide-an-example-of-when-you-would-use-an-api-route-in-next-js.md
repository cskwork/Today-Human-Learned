+++
title = "Next.js API 라우트 — 백엔드 서버 없이 서버 로직 처리하기"
date = "2024-06-11"
description = "Next.js API 라우트는 `pages/api/` 폴더 안에 파일을 만드는 것만으로 서버 함수를 생성한다. 별도 백엔드 없이 외부 API 호출, DB 조회, 인증 처리를 프론트엔드 프로젝트 안에서 처리할 수 있다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Next.js API 라우트는 `pages/api/` 폴더 안에 파일을 만드는 것만으로 서버 함수를 생성한다. 별도 백엔드 없이 외부 API 호출, DB 조회, 인증 처리를 프론트엔드 프로젝트 안에서 처리할 수 있다.

---

## API 라우트를 왜 쓰는지 감 잡기

React 컴포넌트에서 외부 API를 직접 호출하면 API 키가 브라우저에 노출된다. 그렇다고 별도 Express 서버를 운영하기에는 규모가 작다. API 라우트는 이 사이 어딘가에 있는 해법이다. Next.js 프로젝트 안에 파일 하나를 추가하면 그게 곧 서버 엔드포인트가 된다.

요청이 들어올 때만 실행되는 서버리스 함수 형태라서, 항상 켜두는 서버가 필요 없다.

`핵심 흐름: 브라우저 요청 → /api/* 경로 → Next.js 서버 함수 실행 → 외부 API/DB 접근 → JSON 응답 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| API 라우트 | `pages/api/` 안의 파일. 각 파일이 하나의 HTTP 엔드포인트가 된다 |
| 서버리스 함수 | 요청이 있을 때만 실행되는 서버 코드. 상시 실행 서버가 필요 없다 |
| req.query | URL의 쿼리 파라미터(`?city=seoul`)를 담는 객체 |
| res.status().json() | HTTP 상태 코드와 JSON 데이터를 함께 응답하는 메서드 |
| handler 함수 | API 라우트 파일에서 `export default`로 내보내는 요청 처리 함수 |

## 예를 들어 설명하면

날씨 정보를 외부 API에서 가져오는 API 라우트 예시다. API 키는 서버 코드 안에만 있으므로 브라우저에 노출되지 않는다.

```js
// pages/api/weather.js
export default async function handler(req, res) {
  const { city } = req.query;

  if (!city) {
    res.status(400).json({ error: 'city 파라미터가 필요합니다' });
    return;
  }

  try {
    const response = await fetch(
      `https://api.weatherapi.com/v1/current.json?key=${process.env.WEATHER_API_KEY}&q=${city}`
    );
    const data = await response.json();
    res.status(200).json(data);
  } catch (error) {
    res.status(500).json({ error: '서버 오류' });
  }
}
```

프론트엔드 컴포넌트는 외부 API 주소나 키를 모른 채 `/api/weather?city=Seoul`만 호출한다.

## 이 단계에서 중요한 판단 기준

API 키나 민감한 자격 증명이 필요한 외부 연동, 또는 간단한 DB 조회처럼 별도 백엔드 서버를 세우기에는 과한 작업에 API 라우트를 쓴다.

## 한 줄 요약 — 이것만 기억하면 된다

**Next.js API 라우트는 `pages/api/`에 파일 하나를 만드는 것으로 서버 엔드포인트를 완성한다. 민감한 로직을 브라우저에서 격리할 때 특히 유용하다.**

## 나중에 더 깊게 들어가면

- App Router 방식의 Route Handlers (`app/api/route.ts`)와 Pages Router API 라우트의 차이
- API 라우트에서 미들웨어와 인증(JWT, 세션) 처리하기
- Vercel 같은 플랫폼에서 서버리스 함수가 배포되는 방식

---

**원본:** [Provide an example of when you would use an API route in Next.js — memoryhub.tistory.com/276](https://memoryhub.tistory.com/276)
