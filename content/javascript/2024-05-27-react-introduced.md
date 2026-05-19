+++
title = "React 입문 — 컴포넌트, 상태, Virtual DOM"
date = "2024-05-27"
description = "React는 UI를 컴포넌트 단위로 쪼개고, 상태(state)가 바뀌면 Virtual DOM을 통해 최소한의 범위만 다시 그리는 라이브러리다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> React는 UI를 컴포넌트 단위로 쪼개고, 상태(state)가 바뀌면 Virtual DOM을 통해 최소한의 범위만 다시 그리는 라이브러리다.

---

## 이 주제를 왜 쓰는지 감 잡기

웹 화면이 복잡해질수록 어떤 데이터가 어떤 UI에 영향을 주는지 추적하기 어려워진다. React는 UI를 독립적인 컴포넌트로 분리하고, 상태가 바뀌면 해당 컴포넌트만 다시 렌더링해서 이 문제를 해결한다. 데이터가 자주 바뀌는 단일 페이지 앱(SPA), 대시보드, 실시간 피드에 적합하다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 컴포넌트 정의 → props로 데이터 전달 → state로 내부 상태 관리 → 상태 변경 → Virtual DOM 비교 → 실제 DOM 최소 업데이트`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 컴포넌트(component) | UI의 독립 단위. 함수 혹은 클래스로 작성하고, 재사용할 수 있다 |
| JSX | JavaScript 안에서 HTML처럼 보이는 문법. 빌드 단계에서 `React.createElement()` 호출로 변환된다 |
| state | 컴포넌트 내부에서 관리하는 변경 가능한 데이터. 바뀌면 해당 컴포넌트가 다시 렌더링된다 |
| props | 부모가 자식 컴포넌트에 넘겨주는 읽기 전용 데이터 |
| Virtual DOM | 실제 DOM의 가벼운 복사본. React가 이것을 먼저 갱신한 뒤 실제 DOM과 비교해서 달라진 부분만 반영한다 |

## 예를 들어 설명하면

버튼을 클릭할 때마다 숫자가 오르는 카운터 컴포넌트다.

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>{count}번 클릭했습니다</p>
      <button onClick={() => setCount(count + 1)}>클릭</button>
    </div>
  );
}

export default Counter;
```

`useState(0)`은 초기값 0으로 상태를 만든다. `setCount`를 호출하면 `count`가 바뀌고, React는 이 컴포넌트를 다시 렌더링한다. Virtual DOM은 이전 렌더링과 비교해서 `<p>` 태그 안 숫자만 실제 DOM에 반영한다.

## 이 단계에서 중요한 판단 기준

컴포넌트 안에서만 쓰는 데이터는 `useState`로, 여러 컴포넌트가 공유해야 하는 데이터는 상위 컴포넌트의 state로 올리거나 Context API 혹은 외부 상태 라이브러리를 사용한다.

## 한 줄 요약 — 이것만 기억하면 된다

**React에서 화면은 state의 함수다. state가 바뀌면 React가 알아서 최소 범위만 다시 그린다.**

## 나중에 더 깊게 들어가면

- useEffect: 컴포넌트 생명주기와 사이드 이펙트 처리
- Context API와 전역 상태 관리(Redux, Zustand)
- 서버 컴포넌트(React Server Components)와 Next.js

---

**원본:** [React Introduced](https://memoryhub.tistory.com/138)
