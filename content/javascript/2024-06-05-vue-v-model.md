+++
title = "Vue v-model — 양방향 데이터 바인딩의 동작 원리"
date = "2024-06-05"
description = "`v-model`은 입력 요소의 값 바인딩(`v-bind:value`)과 이벤트 리스닝(`@input`)을 하나로 묶은 단축 문법이다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> `v-model`은 입력 요소의 값 바인딩(`v-bind:value`)과 이벤트 리스닝(`@input`)을 하나로 묶은 단축 문법이다.

---

## 이 주제를 왜 쓰는지 감 잡기

폼 입력값을 Vue 데이터와 연동하려면 원래 두 가지를 따로 써야 한다. 값을 데이터에서 읽어오는 바인딩, 그리고 사용자가 입력할 때 데이터를 갱신하는 이벤트 리스너다. `v-model`은 이 두 가지를 한 줄로 처리해준다. 로그인 폼, 검색창, 필터 UI처럼 입력값과 앱 상태를 실시간으로 연결해야 하는 곳에 쓰인다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 사용자 입력 → v-model이 data 갱신 → 화면이 새 값으로 자동 업데이트`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 양방향 바인딩 | 입력이 데이터를 바꾸고, 데이터가 입력 표시를 바꾼다. 두 방향 모두 자동으로 동기화된다 |
| syntactic sugar | 편의를 위해 긴 코드를 짧게 줄인 문법. `v-model`은 `:value` + `@input`의 syntactic sugar다 |
| `.lazy` | 입력 도중이 아니라 포커스를 잃을 때(`change` 이벤트)만 데이터를 갱신하는 수식어 |
| `.number` | 입력값을 자동으로 숫자형으로 변환하는 수식어. 텍스트로 들어오는 숫자 입력에 유용하다 |
| `.trim` | 입력값 앞뒤 공백을 자동으로 제거하는 수식어 |

## 예를 들어 설명하면

`v-model`의 실제 동작을 풀어 쓰면 아래와 같다.

```html
<!-- 이 두 줄은 동일하다 -->
<input v-model="message">
<input :value="message" @input="message = $event.target.value">
```

수식어를 적용한 폼 예시다.

```html
<form @submit.prevent="submitForm">
  <!-- 앞뒤 공백 자동 제거 -->
  <input v-model.trim="name">

  <!-- 숫자형으로 자동 변환 -->
  <input v-model.number="age">

  <!-- 포커스를 잃을 때만 업데이트 -->
  <textarea v-model.lazy="comments"></textarea>

  <button type="submit">제출</button>
</form>
```

`.number`가 없으면 숫자를 입력해도 `age`는 문자열 `"25"`가 된다. 이후 연산에서 의도치 않은 결과가 나올 수 있어서 숫자 입력 필드에는 `.number`를 붙이는 것이 좋다.

## 이 단계에서 중요한 판단 기준

입력값과 Vue 데이터를 실시간으로 연결해야 한다면 `v-model`을 쓴다. 단순히 초기값만 표시하고 Vue가 추적할 필요 없다면 `:value`만 써도 충분하다.

## 한 줄 요약 — 이것만 기억하면 된다

**`v-model`은 `:value`와 `@input`을 합친 단축어이고, `.lazy`, `.number`, `.trim` 수식어로 입력 처리 방식을 세밀하게 조정할 수 있다.**

## 나중에 더 깊게 들어가면

- 커스텀 컴포넌트에서 `v-model` 구현하기 (`modelValue` prop + `update:modelValue` emit)
- Vue 3의 `v-model` 다중 바인딩
- 복잡한 폼 유효성 검사 라이브러리(VeeValidate, Vuelidate)

---

**원본:** [Vue (v-model)](https://memoryhub.tistory.com/191)
