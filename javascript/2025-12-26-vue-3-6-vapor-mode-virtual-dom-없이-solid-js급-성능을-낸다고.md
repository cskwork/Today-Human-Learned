# Vue 3.6 Vapor Mode — Virtual DOM 없이 Solid.js급 성능 내기

> **TL;DR**
> Vue 3.6 Vapor Mode는 컴파일 시점에 직접 DOM 조작 코드를 생성해 Virtual DOM을 제거하고, 기존 Vue 문법을 유지하면서 Solid.js 수준의 렌더링 성능을 달성한다.

---

## 이 주제를 왜 쓰는지 감 잡기

Vue는 오랫동안 "편하지만 Solid.js나 Svelte보다 느리다"는 평가를 받았다. 핵심 원인은 Virtual DOM이다. 상태가 바뀔 때마다 새로운 가상 DOM 트리를 만들고 이전 트리와 비교(diffing)한 뒤 차이점만 실제 DOM에 반영하는 이 과정이, 컴포넌트가 수백 개 이상으로 늘어나면 눈에 띄는 오버헤드가 된다.

Vue 3.6은 Vapor Mode로 이 구조를 바꿨다. 컴파일 시점에 "이 상태가 바뀌면 정확히 이 DOM 요소를 업데이트하라"는 코드를 미리 생성한다. 런타임에 비교 연산 자체가 사라진다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 컴파일 시점 분석 → 직접 DOM 업데이트 코드 생성 → 런타임 비교 연산 없음`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Virtual DOM | 실제 DOM의 가상 복사본을 메모리에 두고 변경 전후를 비교하는 방식. Vue 2/3 기본 동작 |
| Vapor Mode | Virtual DOM 없이 컴파일 타임에 직접 DOM 조작 코드를 생성하는 Vue 3.6의 컴파일 전략 |
| Alien Signals | Vue 3.6에 통합된 반응성 시스템 라이브러리. 의존성 추적 오버헤드를 줄여 메모리와 속도를 개선한다 |
| Signal | 값이 바뀌면 구독자에게 자동으로 알림을 보내는 반응형 데이터 단위. Vue의 `ref`가 이 패턴을 따른다 |
| `vaporInteropPlugin` | 기존 Virtual DOM 앱에 Vapor 컴포넌트를 섞어 쓸 수 있게 해주는 Vue 플러그인 |

## 예를 들어 설명하면

기존 컴포넌트에서 `<script setup>`에 `vapor` 속성 하나만 추가하면 된다:

```vue
<!-- Vapor Mode 적용 전 -->
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<!-- Vapor Mode 적용 후 — vapor 속성만 추가 -->
<script setup vapor>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <button @click="count++">Count: {{ count }}</button>
</template>
```

코드 변경 없이 속성 하나로 해당 컴포넌트는 Virtual DOM 없이 컴파일된다.

기존 프로젝트에 점진적으로 도입하려면:

```js
import { createApp, vaporInteropPlugin } from 'vue'
import App from './App.vue'

createApp(App)
  .use(vaporInteropPlugin)
  .mount('#app')
```

이후 성능이 중요한 컴포넌트에만 `vapor` 속성을 추가한다. 나머지는 기존 방식으로 동작한다.

## 이 단계에서 중요한 판단 기준

Options API를 주로 쓰는 레거시 코드나 Vuetify 같은 VDOM 기반 UI 라이브러리와 혼용하는 경우에는 아직 Vapor Mode 전체 적용을 피하고, 성능이 민감한 컴포넌트 단위로만 부분 적용하는 것이 안전하다.

## 한 줄 요약 — 이것만 기억하면 된다

**Vapor Mode는 `<script setup vapor>` 속성 하나로 활성화되며, Virtual DOM 비교 연산을 컴파일 타임 직접 DOM 업데이트로 대체해 번들 크기 10KB 미만, 10만 컴포넌트 100ms 마운트 성능을 달성한다.**

## 나중에 더 깊게 들어가면

- Vapor Mode가 지원하지 않는 기능(Options API, Render Functions, $attrs 런타임 접근) 대안 패턴
- Alien Signals와 TC39 Signals 표준 제안의 관계 및 크로스 프레임워크 호환 전망
- Vapor Mode 안정 버전 출시 후 Vuetify, Element Plus 등 서드파티 라이브러리 지원 일정

---

**원본:** [Vue 3.6 Vapor Mode, Virtual DOM 없이 Solid.js급 성능을 낸다고?](https://memoryhub.tistory.com/946)
