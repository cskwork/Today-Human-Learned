+++
title = "App Router 중첩 레이아웃과 데이터 패칭 — 실전 예제"
date = "2024-06-10"
description = "`layout.js`를 폴더마다 두면 레이아웃이 자동으로 중첩되고, `page.js`를 async 함수로 만들면 컴포넌트 안에서 바로 데이터를 가져올 수 있다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> `layout.js`를 폴더마다 두면 레이아웃이 자동으로 중첩되고, `page.js`를 async 함수로 만들면 컴포넌트 안에서 바로 데이터를 가져올 수 있다.

---

## 이 예제가 필요한 이유

App Router의 폴더 구조, 레이아웃 중첩, 서버 컴포넌트 데이터 패칭이 실제로 어떻게 맞물리는지를 코드로 한 번에 보여주는 예제다. 개념만 읽으면 추상적으로 느껴지는 것들이 파일을 나란히 놓으면 명확해진다.

구조는 세 단계로 나뉜다.

`핵심 흐름: 루트 레이아웃 → 섹션 레이아웃 → 페이지 콘텐츠`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| RootLayout | `app/layout.js`. html, body 태그를 여기에만 쓴다. 앱 전체를 감싼다. |
| 섹션 레이아웃 | `app/about/layout.js` 같이 특정 경로 아래만 적용되는 레이아웃. |
| async 컴포넌트 | `export default async function` 형태. 함수 안에서 await로 데이터를 가져올 수 있다. |
| 중첩 라우트 | `app/about/info/page.js`는 URL `/about/info`에 대응. 폴더를 중첩하면 URL도 중첩된다. |
| children prop | 레이아웃이 감싸는 페이지 콘텐츠가 들어오는 자리. 반드시 렌더링해야 한다. |

## 예를 들어 설명하면

프로젝트 구조와 각 파일의 역할을 함께 보면 가장 빠르게 이해된다.

```
app/
  layout.js        ← 루트 레이아웃 (html/body/nav)
  page.js          ← /  홈 페이지
  about/
    layout.js      ← /about/* 공통 레이아웃
    page.js        ← /about 페이지
    info/
      page.js      ← /about/info 중첩 페이지
```

```js
// app/layout.js
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <nav>
          <a href="/">Home</a> | <a href="/about">About</a>
        </nav>
        {children}
      </body>
    </html>
  );
}
```

```js
// app/page.js — 홈 페이지, 서버에서 데이터 패칭
export default async function HomePage() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts/1');
  const data = await res.json();
  return (
    <div>
      <h1>Home</h1>
      <p>{data.title}</p>
    </div>
  );
}
```

```js
// app/about/layout.js — /about 전용 레이아웃
export default function AboutLayout({ children }) {
  return (
    <div>
      <header>About 섹션</header>
      {children}
    </div>
  );
}
```

```js
// app/about/page.js
export default async function AboutPage() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts/2');
  const data = await res.json();
  return (
    <div>
      <h1>About</h1>
      <p>{data.title}</p>
      <a href="/about/info">더 보기</a>
    </div>
  );
}
```

```js
// app/about/info/page.js — 중첩 라우트
export default async function InfoPage() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts/3');
  const data = await res.json();
  return <h1>{data.title}</h1>;
}
```

`/about/info`에 접근하면 RootLayout → AboutLayout → InfoPage 순으로 중첩 렌더링된다.

## 이 단계에서 중요한 판단 기준

레이아웃을 어느 폴더에 둘지 결정할 때는 "이 레이아웃이 어느 URL 범위에 적용되어야 하는가"를 먼저 생각하라.

## 한 줄 요약 — 이것만 기억하면 된다

**폴더 위치가 URL이고, layout.js가 레이아웃 범위를 정하며, async page.js 안에서 데이터를 패칭한다.**

## 나중에 더 깊게 들어가면

- 동적 라우트 (`app/posts/[id]/page.js`) 구현
- `loading.js`로 스트리밍 로딩 상태 처리
- 서버 컴포넌트에서 클라이언트 컴포넌트로 데이터를 props로 내려보내는 패턴

---

**원본:** [Simple example using the App Router with a nested layout and data fetching logic — https://memoryhub.tistory.com/258](https://memoryhub.tistory.com/258)
