# Vue.js 입문 — 왜 쓰고 어떻게 돌아가는가

> **TL;DR**
> Vue.js는 데이터가 바뀌면 화면이 자동으로 따라 바뀌는 구조(반응형)를 컴포넌트 단위로 조립하게 해주는 프레임워크다.

---

## 이 주제를 왜 쓰는지 감 잡기

HTML과 JavaScript를 직접 연결하면, 데이터가 바뀔 때마다 DOM을 수동으로 찾아서 갱신해야 한다. 화면이 복잡해질수록 코드가 뒤엉킨다. Vue.js는 데이터와 화면을 자동으로 동기화하고, UI를 독립적인 컴포넌트로 쪼개서 관리하게 해준다. 단일 페이지 애플리케이션(SPA), 관리자 대시보드, 폼 중심 서비스에 흔히 쓰인다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 데이터(data) → 템플릿(template)이 데이터를 화면에 표시 → 사용자 입력 → 메서드(methods)가 데이터 변경 → 화면 자동 갱신`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Vue 인스턴스 | `new Vue({})`로 만드는 애플리케이션의 시작점. 데이터, 메서드, 템플릿을 한 곳에 묶는다 |
| 템플릿(template) | HTML에 `{{ }}` 문법으로 데이터를 끼워 넣는 뷰 영역. 데이터가 바뀌면 자동으로 다시 그려진다 |
| 반응형 데이터(reactive data) | `data` 객체에 선언된 값. Vue가 이 값의 변화를 감시해서 화면을 자동으로 업데이트한다 |
| 메서드(methods) | 버튼 클릭 같은 사용자 행동에 반응하는 함수. `this.데이터이름`으로 데이터를 읽고 쓴다 |
| 컴포넌트(component) | 재사용 가능한 UI 조각. 자체 데이터와 템플릿을 가지며, 여러 곳에서 불러다 쓸 수 있다 |

## 예를 들어 설명하면

할 일 목록을 추가하는 최소 예시다.

```js
new Vue({
  el: '#app',
  data: {
    todos: [{ text: 'Vue.js 배우기' }],
    newTodo: ''
  },
  methods: {
    addTodo() {
      this.todos.push({ text: this.newTodo });
      this.newTodo = '';
    }
  }
});
```

```html
<div id="app">
  <ul>
    <li v-for="todo in todos">{{ todo.text }}</li>
  </ul>
  <input v-model="newTodo">
  <button @click="addTodo">추가</button>
</div>
```

`v-for`는 배열을 반복 렌더링하고, `v-model`은 입력값과 데이터를 양방향으로 연결하며, `@click`은 클릭 이벤트를 메서드에 연결한다. `addTodo`가 `todos`를 바꾸면 화면은 알아서 갱신된다.

## 이 단계에서 중요한 판단 기준

화면에 표시되는 값이 여러 곳에서 공유되거나 자주 바뀐다면 Vue의 반응형 데이터로 관리하는 것이 맞다. 단순 정적 페이지라면 Vue 없이 순수 HTML이 더 간단하다.

## 한 줄 요약 — 이것만 기억하면 된다

**데이터를 `data`에 두고 `template`으로 화면에 연결하면, 데이터가 바뀔 때 화면은 Vue가 알아서 갱신한다.**

## 나중에 더 깊게 들어가면

- 계산된 속성(computed): 데이터에서 파생된 값을 캐싱하는 방법
- 컴포넌트 간 데이터 전달(props, emit)
- Vue 3의 Composition API와 `<script setup>` 문법

---

**원본:** [VueJS Introduced](https://memoryhub.tistory.com/92)
