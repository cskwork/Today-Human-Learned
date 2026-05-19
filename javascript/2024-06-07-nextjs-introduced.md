# Next.js — React 앱에 서버 렌더링을 더한 프레임워크

> **TL;DR**
> Next.js는 React로 UI를 만들면서 SSR, SSG, CSR을 상황에 따라 선택할 수 있게 해주는 풀스택 프레임워크다.

---

## Next.js를 왜 쓰는지 감 잡기

순수 React(Create React App)는 브라우저에서 JavaScript가 실행된 뒤에야 화면이 그려진다. 이 방식은 검색 엔진이 콘텐츠를 제대로 읽지 못하고, 첫 화면이 뜨는 데 시간이 걸린다는 단점이 있다.

Next.js는 서버가 HTML을 미리 만들어서 브라우저에 내려주는 방식(SSR, SSG)을 React와 결합한다. 덕분에 첫 화면이 빠르고, SEO(검색 엔진 최적화)에도 유리하다. 블로그, 커머스, 대시보드, SaaS 프론트엔드에 널리 쓰인다.

초보자는 처음에 이렇게 이해하면 된다.

`요청 → 서버에서 HTML 생성(SSR) 또는 빌드 시 HTML 생성(SSG) → 브라우저 수신 → React가 이어받아 동작(Hydration)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| SSR (Server-Side Rendering) | 요청이 올 때마다 서버가 HTML을 새로 만들어 보내는 방식 |
| SSG (Static Site Generation) | 빌드 시 HTML을 미리 만들어두고 CDN에서 바로 서빙하는 방식 |
| CSR (Client-Side Rendering) | HTML은 최소한만 내려보내고 브라우저 JavaScript가 화면을 그리는 방식 |
| 파일 기반 라우팅 | `pages/about.js` 파일을 만들면 `/about` URL이 자동으로 생기는 규칙 |
| API Route | `pages/api/` 폴더에 파일을 두면 백엔드 API 엔드포인트가 되는 기능 |

## 예를 들어 설명하면

블로그를 만든다고 가정하자. 홈페이지는 내용이 자주 바뀌지 않으니 SSG로 빌드 때 만들고, 댓글처럼 실시간 데이터가 필요한 곳은 SSR로 처리한다.

```jsx
// SSG — 빌드 시 데이터를 가져와 HTML 생성
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();
  return { props: { posts } };
}

// SSR — 요청마다 서버에서 최신 데이터 조회
export async function getServerSideProps(context) {
  const res = await fetch(`https://api.example.com/comments?postId=${context.params.id}`);
  const comments = await res.json();
  return { props: { comments } };
}
```

`getStaticProps`는 빌드 단계에 한 번 실행되고, `getServerSideProps`는 요청마다 실행된다.

## 이 단계에서 중요한 판단 기준

콘텐츠가 자주 바뀌면 SSR, 거의 안 바뀌면 SSG, 검색 엔진이 읽을 필요 없는 인증된 대시보드라면 CSR을 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Next.js는 React UI에 SSR/SSG/CSR을 조합할 수 있게 해서 빠른 초기 로딩과 SEO를 동시에 잡는 프레임워크다.**

## 나중에 더 깊게 들어가면

- App Router(Next.js 13+)와 Server Components의 차이
- ISR(Incremental Static Regeneration)로 SSG 페이지를 주기적으로 갱신하는 방법
- Middleware를 이용한 인증 처리 패턴

---

**원본:** [NextJS Introduced](https://memoryhub.tistory.com/205)
