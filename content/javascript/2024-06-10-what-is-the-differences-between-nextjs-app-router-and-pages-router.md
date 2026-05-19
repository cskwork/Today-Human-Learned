+++
title = "Next.js App Router vs Pages Router — 무엇이 다른가"
date = "2024-06-10"
description = "Pages Router는 파일 하나 = 라우트 하나인 전통 방식이고, App Router는 React Server Components 기반의 유연한 신형 방식이다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Pages Router는 파일 하나 = 라우트 하나인 전통 방식이고, App Router는 React Server Components 기반의 유연한 신형 방식이다.

---

## 라우터를 왜 두 가지나 만들었는지 감 잡기

웹 앱은 URL마다 다른 페이지를 보여줘야 한다. Next.js는 처음에 `pages/` 폴더 안에 파일을 만들면 자동으로 라우트가 생기는 단순한 방식을 택했다. 이게 Pages Router다.

그런데 앱이 복잡해지면서 문제가 생겼다. 데이터를 페이지 단위로만 가져올 수 있어서 중첩 레이아웃을 공유하거나 컴포넌트 수준에서 서버 데이터를 쓰기가 어려웠다. Next.js 13부터 이 문제를 해결한 App Router가 도입됐다.

둘 다 지금도 공식 지원된다. 신규 프로젝트에는 App Router가 권장된다.

`핵심 흐름: 파일 위치 → 라우트 결정 → 데이터 패칭 → 렌더링`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Pages Router | `pages/` 폴더 기반. 파일 하나가 URL 하나. |
| App Router | `app/` 폴더 기반. 폴더 구조가 라우트이고 레이아웃을 중첩할 수 있다. |
| React Server Component (RSC) | 서버에서만 실행되는 React 컴포넌트. JS를 클라이언트로 전송하지 않는다. |
| layout.js | 해당 폴더 아래 모든 페이지에 공통 적용되는 껍데기 컴포넌트. |
| getStaticProps / getServerSideProps | Pages Router에서만 쓰는 데이터 패칭 함수. App Router에서는 async 컴포넌트로 대체된다. |

## 예를 들어 설명하면

같은 `/about` 페이지를 두 방식으로 구현하면 차이가 명확하다.

**Pages Router**
```js
// pages/about.js
export async function getServerSideProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  return { props: { data } };
}

export default function AboutPage({ data }) {
  return <h1>{data.title}</h1>;
}
```

**App Router**
```js
// app/about/page.js
export default async function AboutPage() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  return <h1>{data.title}</h1>;
}
```

App Router는 컴포넌트 자체가 async 함수다. 별도의 데이터 패칭 헬퍼가 필요 없다.

## 이 단계에서 중요한 판단 기준

신규 프로젝트라면 App Router를 선택하고, 기존 Pages Router 코드를 굳이 마이그레이션할 긴급한 이유는 없다.

## 한 줄 요약 — 이것만 기억하면 된다

**App Router는 컴포넌트 수준 서버 렌더링과 중첩 레이아웃을 가능하게 한 Next.js의 진화형 라우팅 시스템이다.**

## 나중에 더 깊게 들어가면

- `'use client'` 지시어와 서버/클라이언트 컴포넌트 경계 설계
- App Router의 캐싱 전략 (fetch 옵션, revalidate)
- 기존 Pages Router 프로젝트를 App Router로 점진적으로 마이그레이션하는 방법

---

**원본:** [What is the differences between Nextjs App router and Pages router? — https://memoryhub.tistory.com/256](https://memoryhub.tistory.com/256)
