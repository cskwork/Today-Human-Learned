+++
title = "Vue 3 컴포넌트 구조 — script 먼저 vs template 먼저"
date = "2025-04-22"
description = "Vue 컴파일러는 블록 순서를 신경 쓰지 않지만, Vue 3 공식 스타일 가이드는 `<script setup>` → `<template>` → `<style>` 순서를 강력히 권장한다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Vue 컴파일러는 블록 순서를 신경 쓰지 않지만, Vue 3 공식 스타일 가이드는 `<script setup>` → `<template>` → `<style>` 순서를 강력히 권장한다.

---

## 이 주제를 왜 쓰는지 감 잡기

`.vue` 파일은 `<template>`, `<script>`, `<style>` 세 블록으로 이루어진다. 어떤 순서로 배치하든 Vue 컴파일러는 컴포넌트를 동일하게 해석한다. 기능 차이는 없다.

그런데 순서가 중요해진 이유는 Vue 3에서 `<script setup>`이 주류가 됐기 때문이다. Composition API를 사용하면 변수와 함수가 `<script>` 안에 선언되고, 바로 아래 `<template>`이 그것을 참조한다. 이때 로직을 먼저 보고 그 결과가 어떻게 쓰이는지를 위에서 아래로 읽는 것이 자연스럽다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: script(로직 정의) → template(로직 사용) → style(외형 꾸미기)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| SFC (Single File Component) | 하나의 `.vue` 파일 안에 template, script, style을 모두 담은 컴포넌트 |
| `<script setup>` | Composition API 코드를 간결하게 쓸 수 있는 블록으로, 변수를 export 없이 template에서 바로 쓸 수 있다 |
| Composition API | 로직을 함수 단위로 조합하는 Vue 3의 코드 작성 방식 |
| Options API | `data()`, `methods`, `computed` 등 객체 키로 구분하는 Vue 2 시대의 코드 방식 |
| 블록 순서 컨벤션 | 팀이나 프로젝트 전체에서 블록 배치를 통일하는 규칙 |

## 예를 들어 설명하면

Vue 3 공식 권장 순서로 작성한 예시:

```vue
<!-- 권장: script → template → style -->
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <button @click="count++">클릭 수: {{ count }}</button>
</template>

<style scoped>
button { font-size: 1rem; }
</style>
```

`count`가 위에서 정의되고, 바로 아래 template에서 쓰인다. 코드를 위에서 아래로 읽으면 로직 → 사용 → 외형 순서로 따라갈 수 있다.

반대로 `<template>`을 맨 위에 두면, template에서 참조하는 변수가 어디서 왔는지 확인하려고 아래로 스크롤해야 한다. 로직이 많을수록 오가는 횟수가 늘어난다.

## 이 단계에서 중요한 판단 기준

`<script setup>`을 쓴다면 script를 먼저 두어야 위에서 아래로 읽히는 코드 흐름이 만들어진다.

## 한 줄 요약 — 이것만 기억하면 된다

**Vue 3에서는 `<script setup>` → `<template>` → `<style>` 순서가 공식 권장이며, ESLint `vue/block-order` 규칙으로 팀 전체에 강제할 수 있다.**

## 나중에 더 깊게 들어가면

- ESLint `eslint-plugin-vue`의 `vue/block-order` 규칙 설정 방법
- Options API와 Composition API를 혼용할 때 블록 구성 전략
- Volar(Vue 언어 지원 도구)가 블록 순서에 따라 제공하는 자동완성 차이

---

**원본:** [Vue 3 컴포넌트 구조 - script 먼저 vs template 먼저](https://memoryhub.tistory.com/557)
