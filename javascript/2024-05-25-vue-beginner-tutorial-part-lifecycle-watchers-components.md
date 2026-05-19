# Vue 기초 2편 — 라이프사이클, 워처, 컴포넌트 통신

> **TL;DR**
> Vue 컴포넌트는 생성-마운트-업데이트-소멸의 생애 주기를 가지며, 워처는 데이터 변화에 반응해 부수 효과를 처리하고, 컴포넌트 간 통신은 props(아래로)와 emits(위로)로 이루어진다.

---

## 이 개념들을 왜 알아야 하는지 감 잡기

Vue가 데이터 변화를 화면에 자동 반영해주는 것은 강력하지만, 세 가지 상황에서는 그것만으로 부족하다. 첫째, 컴포넌트가 화면에 나타난 직후에 특정 코드를 실행해야 할 때(예: API 호출). 둘째, 특정 데이터가 바뀔 때 화면 외의 작업(예: 로그 전송, 다른 API 호출)을 해야 할 때. 셋째, 화면을 재사용 가능한 조각으로 쪼개고 조각들 사이에 데이터를 주고받아야 할 때.

라이프사이클 훅, 워처, 컴포넌트 시스템이 각각 이 세 가지를 해결한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 컴포넌트 생성 → mounted 훅 실행 → 데이터 변화 → watch 반응 → 자식 컴포넌트에 props 전달 → 자식이 emits로 응답`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 라이프사이클 훅 | 컴포넌트의 생애 특정 시점에 자동으로 호출되는 함수. `mounted`는 화면에 붙은 직후, `updated`는 데이터 변경 후 DOM이 갱신된 직후에 실행된다. |
| mounted | 컴포넌트가 실제 DOM에 삽입된 직후 실행되는 훅. 초기 API 호출 위치로 가장 많이 쓴다. |
| watch | 특정 데이터를 감시하다가 값이 바뀌면 지정한 함수를 실행한다. computed와 달리 부수 효과(API 호출, 로깅 등)를 처리할 때 쓴다. |
| props | 부모 컴포넌트가 자식 컴포넌트에 전달하는 읽기 전용 데이터. 자식은 props를 직접 수정하면 안 된다. |
| emits | 자식 컴포넌트가 부모에게 보내는 이벤트. `this.$emit('이벤트명', 데이터)` 형태로 호출한다. |

## 예를 들어 설명하면

ID가 바뀔 때마다 자동으로 해당 ID의 데이터를 불러오는 패턴은 `mounted` + `watch`의 조합으로 구현한다.

```vue
<script>
export default {
  data() {
    return { todoId: 1, todoData: null }
  },
  methods: {
    async fetchData() {
      this.todoData = null
      const res = await fetch(`https://jsonplaceholder.typicode.com/todos/${this.todoId}`)
      this.todoData = await res.json()
    }
  },
  mounted() {
    // 컴포넌트가 화면에 나타나면 즉시 첫 데이터 로드
    this.fetchData()
  },
  watch: {
    // todoId가 바뀔 때마다 새 데이터 로드
    todoId() {
      this.fetchData()
    }
  }
}
</script>
```

컴포넌트 간 통신은 부모 → 자식 방향은 `:msg="greeting"`(props), 자식 → 부모 방향은 `@response="handler"`(emits)로 처리한다. 슬롯(slot)은 부모가 자식 컴포넌트의 내부 레이아웃 일부를 채워 넣는 방식으로, 재사용 가능한 래퍼 컴포넌트를 만들 때 유용하다.

## 이 단계에서 중요한 판단 기준

데이터 변화에 반응해 계산된 값이 필요하면 `computed`, 계산 이외의 부수 작업(API 호출, 외부 라이브러리 제어)이 필요하면 `watch`를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**라이프사이클로 시점을 제어하고, watch로 변화에 반응하고, props/emits로 컴포넌트끼리 대화한다.**

## 나중에 더 깊게 들어가면

- `watch`의 `immediate: true`와 `deep: true` 옵션
- Composition API에서의 `onMounted`, `watchEffect` 사용법
- 전역 상태 관리가 필요해지는 시점과 Pinia 도입 기준

---

**원본:** [Vue Beginner Tutorial Part 2 — https://memoryhub.tistory.com/77](https://memoryhub.tistory.com/77)
