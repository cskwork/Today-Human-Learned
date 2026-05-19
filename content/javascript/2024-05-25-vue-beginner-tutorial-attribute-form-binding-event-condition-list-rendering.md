+++
title = "Vue 기초 — 속성 바인딩, 폼, 이벤트, 조건, 목록 렌더링"
date = "2024-05-25"
description = "Vue는 데이터가 바뀌면 화면이 자동으로 따라 바뀌는 선언형 렌더링을 중심으로 동작하며, `v-bind`, `v-on`, `v-model`, `v-if`, `v-for` 다섯 디렉티브가 그 핵심이다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Vue는 데이터가 바뀌면 화면이 자동으로 따라 바뀌는 선언형 렌더링을 중심으로 동작하며, `v-bind`, `v-on`, `v-model`, `v-if`, `v-for` 다섯 디렉티브가 그 핵심이다.

---

## Vue 템플릿 시스템을 왜 쓰는지 감 잡기

HTML만으로 동적인 화면을 만들려면 JavaScript로 DOM을 직접 조작해야 한다. 데이터가 바뀔 때마다 `document.getElementById`를 불러 값을 바꿔줘야 하는 번거로움이 생긴다.

Vue는 이 문제를 뒤집는다. 데이터(JavaScript 객체)를 선언하고 HTML에 연결만 해두면, 데이터가 바뀌는 순간 화면이 알아서 업데이트된다. 개발자는 DOM 조작 대신 데이터 관리에만 집중하면 된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: data() 선언 → 디렉티브로 HTML에 연결 → 데이터 변경 → 화면 자동 갱신`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 반응형 상태 (Reactive State) | 변경되면 화면 업데이트를 자동으로 유발하는 데이터. `data()`에서 반환한 객체가 여기에 해당한다. |
| 디렉티브 (Directive) | `v-`로 시작하는 HTML 특수 속성. Vue가 이 속성을 보고 화면을 제어한다. |
| v-bind | JavaScript 값을 HTML 속성에 연결한다. `:class="변수명"` 형태로 줄여 쓴다. |
| v-model | 입력 필드와 데이터를 양방향으로 동기화한다. 사용자가 입력하면 데이터가, 데이터가 바뀌면 입력 필드가 함께 바뀐다. |
| computed | 다른 데이터를 기반으로 자동 계산되는 읽기 전용 속성. 의존 데이터가 바뀔 때만 재계산되어 성능을 아낀다. |

## 예를 들어 설명하면

할일 목록 앱을 만드는 경우, 다섯 디렉티브를 모두 활용하게 된다.

```vue
<script>
let id = 0
export default {
  data() {
    return {
      newTodo: '',
      todos: [
        { id: id++, text: 'HTML 배우기', done: true },
        { id: id++, text: 'Vue 배우기', done: false }
      ]
    }
  },
  computed: {
    // 완료된 항목을 숨길 때 사용
    activeTodos() {
      return this.todos.filter(t => !t.done)
    }
  },
  methods: {
    addTodo() {
      this.todos.push({ id: id++, text: this.newTodo, done: false })
      this.newTodo = ''
    }
  }
}
</script>

<template>
  <!-- v-model: 입력값과 newTodo 동기화 -->
  <input v-model="newTodo" placeholder="새 할일">
  <button @click="addTodo">추가</button>

  <!-- v-for: 배열을 순회해 항목 렌더링 -->
  <li v-for="todo in todos" :key="todo.id">
    <!-- v-if: done 여부에 따라 조건부 표시 -->
    <span v-if="!todo.done">{{ todo.text }}</span>
  </li>
</template>
```

`v-model`은 `:value + @input`을 하나로 합친 단축형이다. `v-for`에는 반드시 `:key`를 붙여야 Vue가 목록 변경을 효율적으로 추적한다.

## 이 단계에서 중요한 판단 기준

입력 필드에는 `v-model`, 읽기 전용 속성 연결에는 `v-bind(:)`, 이벤트 처리에는 `v-on(@)`을 쓴다는 역할 구분을 먼저 익히면 나머지 디렉티브는 자연스럽게 따라온다.

## 한 줄 요약 — 이것만 기억하면 된다

**Vue 디렉티브는 데이터와 HTML을 연결하는 다리이며, 데이터만 바꾸면 화면은 알아서 따라온다.**

## 나중에 더 깊게 들어가면

- `v-bind`의 객체 문법과 배열 문법 (`:class="{ active: isActive }"`)
- 이벤트 수식어 (`.prevent`, `.stop`, `.once`)
- 리스트 갱신 시 push/splice 같은 변이 메서드와 새 배열 교체의 차이

---

**원본:** [Vue Beginner Tutorial — https://memoryhub.tistory.com/76](https://memoryhub.tistory.com/76)
