+++
title = "Vue.js 핵심 개념 — 계산된 속성, 디렉티브, 라우터, 상태 관리"
date = "2024-05-26"
description = "Vue.js의 computed, directive, Vue Router, Vuex는 각각 파생 값 계산, 템플릿 동작 제어, 페이지 전환, 전역 상태 관리를 담당한다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Vue.js의 computed, directive, Vue Router, Vuex는 각각 파생 값 계산, 템플릿 동작 제어, 페이지 전환, 전역 상태 관리를 담당한다.

---

## 이 주제를 왜 쓰는지 감 잡기

Vue 인스턴스와 기본 반응형 데이터를 이해했다면, 다음 단계는 "화면을 더 효율적으로 제어하는 방법"이다. 단순히 데이터를 표시하는 것을 넘어, 계산된 값을 캐싱하고, 여러 페이지를 관리하고, 컴포넌트 간 공유 상태를 다뤄야 하는 순간이 온다. 이 네 가지 개념이 그 순간에 필요한 도구다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 데이터 → computed(파생 값 계산) → template(디렉티브로 렌더링) → Router(페이지 분기) → Vuex(전역 공유 상태)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| computed | 다른 데이터에서 계산되는 값. 의존 데이터가 바뀔 때만 재계산되고 그 외엔 캐시를 반환한다 |
| 디렉티브(directive) | 템플릿에서 `v-`로 시작하는 특수 속성. `v-if`, `v-for`, `v-bind`, `v-model` 등이 있다 |
| props | 부모 컴포넌트가 자식 컴포넌트에 데이터를 넘겨줄 때 사용하는 읽기 전용 속성 |
| Vue Router | SPA(단일 페이지 앱)에서 URL 경로에 따라 다른 컴포넌트를 보여주는 공식 라우팅 라이브러리 |
| Vuex | 여러 컴포넌트가 공유하는 상태를 중앙에서 관리하는 라이브러리. 상태(state), 변이(mutation)로 구성된다 |

## 예를 들어 설명하면

`computed`와 `methods`의 차이가 헷갈리는 경우가 많다.

```js
computed: {
  // 의존 데이터(message)가 바뀌지 않으면 다시 계산하지 않는다
  reversedMessage() {
    return this.message.split('').reverse().join('');
  },
  todoCount() {
    return this.todos.length;
  }
}
```

같은 로직을 `methods`에 넣으면 렌더링마다 매번 호출된다. 계산 비용이 있는 파생 값이라면 `computed`를 쓰는 것이 성능상 유리하다.

Vue Router로 페이지를 나누는 최소 예시다.

```js
const routes = [
  { path: '/home', component: Home },
  { path: '/about', component: About }
];
const router = new VueRouter({ routes });
new Vue({ router, render: h => h(App) }).$mount('#app');
```

Vuex에서 상태를 읽고 변경하는 패턴이다.

```js
// 스토어 정의
const store = new Vuex.Store({
  state: { count: 0 },
  mutations: {
    increment(state) { state.count++; }
  }
});

// 컴포넌트에서 사용
computed: { count() { return this.$store.state.count; } },
methods: { increment() { this.$store.commit('increment'); } }
```

## 이 단계에서 중요한 판단 기준

파생 값은 `computed`, 사용자 행동에 반응하는 로직은 `methods`, 두 컴포넌트 이상이 같은 데이터를 공유해야 하면 `Vuex`를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**computed는 캐싱되는 계산식, Router는 URL 분기, Vuex는 전역 공유 상태 — 세 가지 역할이 분명히 다르다.**

## 나중에 더 깊게 들어가면

- Vuex actions: 비동기 작업(API 호출)을 처리하는 방법
- 커스텀 디렉티브 작성
- Vue 3의 Pinia — Vuex를 대체하는 공식 상태 관리 라이브러리

---

**원본:** [VueJS Core Concept](https://memoryhub.tistory.com/93)
