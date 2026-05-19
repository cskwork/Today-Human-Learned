# Next.js App Router — 파일 구조가 곧 URL 구조

> **TL;DR**
> Next.js App Router는 `app/` 폴더 안의 파일 위치가 그대로 URL 경로가 되며, 서버 컴포넌트와 클라이언트 컴포넌트를 명시적으로 나눠 성능을 제어한다.

---

## App Router를 왜 쓰는지 감 잡기

Next.js 13 이전에는 `pages/` 폴더 안에 파일을 두면 자동으로 라우트가 만들어졌다. App Router는 그 방식을 `app/` 폴더 기반으로 재설계했다. 단순히 폴더 이름만 바뀐 게 아니라, 서버에서 렌더링하는 컴포넌트와 브라우저에서 동작하는 컴포넌트를 명확히 구분하는 개념이 함께 들어왔다.

서버 컴포넌트는 DB 조회나 API 호출을 서버에서 처리한 뒤 HTML만 클라이언트로 보낸다. 클라이언트 컴포넌트는 버튼 클릭이나 상태 변경처럼 브라우저에서 동작해야 하는 코드다. 이 둘을 섞어 쓰면 성능과 인터랙션을 동시에 챙길 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: app/ 폴더 구조 → 자동 라우팅 → 서버/클라이언트 컴포넌트 분리 → 데이터 페칭`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Server Component | 서버에서만 실행되는 컴포넌트. DB 접근 가능, `useState` 불가 |
| Client Component | `'use client'` 선언 후 브라우저에서 실행. 상태와 이벤트 처리 가능 |
| layout.js | 같은 폴더 하위 모든 페이지에 공통 UI를 감싸는 파일 |
| Route Handler | `app/api/.../route.js`에 두는 API 엔드포인트 |
| Route Group | 폴더명을 `(이름)` 형태로 만들면 URL에 영향 없이 파일을 논리적으로 묶을 수 있다 |

## 예를 들어 설명하면

대시보드 페이지에서 서버 컴포넌트와 클라이언트 컴포넌트를 함께 쓰는 구조다.

```tsx
// app/(dashboard)/page.tsx — 서버 컴포넌트 (기본값)
import { Suspense } from 'react';
import DashboardMetrics from '@/components/DashboardMetrics';
import RecentActivity from '@/components/RecentActivity';

export default function DashboardPage() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Suspense fallback={<p>Loading...</p>}>
        <DashboardMetrics />   {/* 서버에서 DB 조회 */}
      </Suspense>
      <Suspense fallback={<p>Loading...</p>}>
        <RecentActivity />     {/* 클라이언트에서 인터랙션 */}
      </Suspense>
    </div>
  );
}

// components/DashboardMetrics.tsx — 서버 컴포넌트
export default async function DashboardMetrics() {
  const metrics = await fetchFromDB(); // 서버에서 직접 실행
  return <ul>{metrics.map(m => <li key={m.id}>{m.name}</li>)}</ul>;
}

// components/RecentActivity.tsx — 클라이언트 컴포넌트
'use client';
import { useState, useEffect } from 'react';

export default function RecentActivity() {
  const [items, setItems] = useState([]);
  useEffect(() => { fetchActivity().then(setItems); }, []);
  return <ul>{items.map(i => <li key={i.id}>{i.description}</li>)}</ul>;
}
```

`app/` 안의 폴더 구조는 URL 그대로다. `app/dashboard/page.tsx`는 `/dashboard` 경로에 응답한다.

## 이 단계에서 중요한 판단 기준

컴포넌트가 `useState`, `useEffect`, 또는 클릭 이벤트를 필요로 한다면 `'use client'`를 선언하고, 그렇지 않으면 기본 서버 컴포넌트로 둔다.

## 한 줄 요약 — 이것만 기억하면 된다

**App Router에서 핵심은 파일 위치가 곧 라우트이며, 서버/클라이언트 컴포넌트 구분이 성능을 결정한다.**

## 나중에 더 깊게 들어가면

- `revalidate` 옵션으로 페이지별 캐시 전략 제어하기
- `generateStaticParams`로 동적 경로를 빌드 타임에 정적 생성하기
- 미들웨어(`middleware.ts`)로 인증 처리 및 리다이렉트 구현하기

---

**원본:** [NextJS App Router Web app Example](https://memoryhub.tistory.com/307)
