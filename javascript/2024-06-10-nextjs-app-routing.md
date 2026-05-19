# Next.js App Router 동작 원리

> **TL;DR**
> `app/` 폴더 구조가 곧 라우트이고, 각 파일은 역할(page, layout, loading...)에 따라 이름이 정해져 있다.

---

## App Router를 왜 쓰는지 감 잡기

Pages Router에서는 레이아웃을 공유하려면 `_app.js`에 모든 것을 밀어 넣거나 각 페이지에서 래퍼 컴포넌트를 반복해야 했다. 예를 들어 `/dashboard` 아래의 모든 페이지에 사이드바를 넣고 싶을 때, 페이지마다 사이드바 컴포넌트를 임포트해야 했다.

App Router는 이 문제를 폴더 기반 레이아웃으로 해결한다. 폴더 안에 `layout.js`를 두면 그 폴더 아래 모든 페이지에 자동으로 레이아웃이 적용된다. 컴포넌트는 기본적으로 서버에서 실행되므로, 데이터 패칭 코드를 별도 함수 없이 컴포넌트 안에 바로 쓸 수 있다.

`핵심 흐름: app/ 폴더 → 파일 역할 결정(page/layout/loading) → 서버 컴포넌트로 렌더링 → 필요한 부분만 클라이언트로 전환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| page.js | 해당 경로의 실제 페이지 콘텐츠를 담는 파일. |
| layout.js | 같은 폴더와 하위 폴더 페이지를 감싸는 껍데기. 네비게이션, 헤더 등을 여기에 둔다. |
| React Server Component | 서버에서만 실행. DB 조회, API 호출을 컴포넌트 안에서 직접 할 수 있다. |
| 'use client' | 이 지시어를 파일 최상단에 쓰면 해당 컴포넌트는 브라우저에서 실행된다. onClick 같은 이벤트가 필요할 때 사용. |
| 중첩 레이아웃 | 폴더를 중첩하면 레이아웃도 자동으로 중첩된다. 루트 layout → 섹션 layout → page 순으로 감싼다. |

## 예를 들어 설명하면

아래 폴더 구조는 루트 레이아웃 아래에 `/about`이 별도 레이아웃을 갖는 구조다.

```
app/
  layout.js        ← 전체 앱을 감쌈 (html, body, nav)
  page.js          ← / 경로
  about/
    layout.js      ← /about/* 경로만 감쌈
    page.js        ← /about 경로
```

```js
// app/layout.js — 루트 레이아웃
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <nav>공통 네비게이션</nav>
        {children}
      </body>
    </html>
  );
}

// app/about/page.js — 서버 컴포넌트에서 직접 데이터 패칭
export default async function AboutPage() {
  const res = await fetch('https://api.example.com/about');
  const data = await res.json();
  return <h1>{data.title}</h1>;
}
```

클라이언트 상태나 이벤트가 필요하면 해당 컴포넌트 파일 최상단에 `'use client'`를 선언한다.

## 이 단계에서 중요한 판단 기준

컴포넌트에 `onClick`, `useState`, `useEffect`가 필요한지 먼저 확인하라. 없다면 기본 서버 컴포넌트로 두는 것이 성능상 유리하다.

## 한 줄 요약 — 이것만 기억하면 된다

**App Router에서는 폴더 구조가 라우트이고, layout.js가 중첩 레이아웃을 자동 처리하며, 컴포넌트는 기본이 서버다.**

## 나중에 더 깊게 들어가면

- loading.js, error.js, not-found.js 등 특수 파일의 역할
- fetch 캐싱 옵션 (`cache: 'no-store'` vs `next: { revalidate: 60 }`)
- 서버 컴포넌트와 클라이언트 컴포넌트 사이에서 데이터를 props로 전달하는 패턴

---

**원본:** [Nextjs App routing — https://memoryhub.tistory.com/257](https://memoryhub.tistory.com/257)
