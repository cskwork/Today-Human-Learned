+++
title = "Vue vs React 생명주기 — 같은 개념, 다른 이름"
date = "2024-06-10"
description = "Vue와 React는 생성·업데이트·소멸이라는 동일한 단계를 거치지만, 훅 이름과 실행 방식이 달라서 대응 관계를 먼저 파악해야 한다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Vue와 React는 생성·업데이트·소멸이라는 동일한 단계를 거치지만, 훅 이름과 실행 방식이 달라서 대응 관계를 먼저 파악해야 한다.

---

## Vue·React 생명주기를 왜 비교하는지 감 잡기

둘 다 컴포넌트 기반 프레임워크이므로 "화면에 나타날 때", "데이터가 바뀔 때", "사라질 때" 특정 코드를 실행하는 메커니즘이 필요하다. 목표는 같지만 용어와 타이밍이 달라서, 한 프레임워크에만 익숙한 개발자가 다른 쪽으로 옮길 때 혼란이 생긴다.

초보자는 처음에 이렇게 이해하면 된다.

`생성(init) → 마운트(DOM 삽입) → 업데이트(리렌더링) → 소멸(DOM 제거)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| componentDidMount (React) | 컴포넌트가 DOM에 삽입된 직후. Vue의 `mounted`와 같은 시점. |
| mounted (Vue) | React의 `componentDidMount`에 대응. DOM 조작과 데이터 fetch 시작점. |
| componentWillUnmount (React) | 컴포넌트 제거 직전. 정리 작업 담당. Vue의 `beforeDestroy`에 대응. |
| useEffect (React 함수형) | 클래스 컴포넌트의 `componentDidMount` + `componentDidUpdate` + `componentWillUnmount`를 하나로 대체하는 훅. |
| Composition API setup (Vue 3) | Vue 3에서 `onMounted`, `onUnmounted` 등 생명주기 훅을 함수 안에서 등록하는 방식. |

## 예를 들어 설명하면

두 프레임워크의 "마운트 후 API 호출 → 소멸 전 정리" 패턴을 나란히 보면 대응 관계가 명확해진다.

**React (함수형 컴포넌트)**
```jsx
function MyComponent() {
  const [count, setCount] = React.useState(0);

  React.useEffect(() => {
    // componentDidMount에 해당
    const id = setInterval(() => setCount((c) => c + 1), 1000);
    return () => clearInterval(id); // componentWillUnmount에 해당
  }, []);

  return <h1>{count}</h1>;
}
```

**Vue (Options API)**
```javascript
new Vue({
  data() { return { count: 0 }; },
  mounted() {
    this.id = setInterval(() => { this.count++; }, 1000);
  },
  beforeDestroy() {
    clearInterval(this.id);
  },
  template: '<h1>{{ count }}</h1>'
});
```

두 코드는 동일한 동작을 한다. `useEffect`의 반환 함수가 Vue의 `beforeDestroy` 역할을 담당한다.

## 대응 관계 한눈에 보기

| 단계 | React (클래스) | React (함수형) | Vue 2 |
|---|---|---|---|
| 초기화 | `constructor` | `useState` 초기값 | `beforeCreate` / `created` |
| DOM 삽입 후 | `componentDidMount` | `useEffect(fn, [])` | `mounted` |
| 업데이트 후 | `componentDidUpdate` | `useEffect(fn, [dep])` | `updated` |
| 제거 전 | `componentWillUnmount` | `useEffect`의 return 함수 | `beforeDestroy` |

## 이 단계에서 중요한 판단 기준

Vue는 훅 이름만 보면 실행 시점이 명확하지만, React의 `useEffect`는 의존성 배열(`[]`)을 어떻게 쓰느냐에 따라 세 가지 훅을 모두 대체한다 — 이 차이를 먼저 파악하면 혼란이 줄어든다.

## 한 줄 요약 — 이것만 기억하면 된다

**생명주기 단계는 동일하다. React는 useEffect 하나로, Vue는 훅 이름별로 분리해서 관리한다.**

## 나중에 더 깊게 들어가면

- Vue 3 `onMounted` / `onUnmounted`와 React `useEffect`의 설계 철학 차이
- React의 `StrictMode`가 `useEffect`를 두 번 실행하는 이유
- 상태 관리 라이브러리(Pinia, Redux)와 생명주기 훅의 연동 패턴

---

**원본:** [Vue vs React Lifecycle — memoryhub.tistory.com/255](https://memoryhub.tistory.com/255)
