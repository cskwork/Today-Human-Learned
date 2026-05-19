# Vue.js 생명주기 — 컴포넌트가 만들어지고 사라지는 과정

> **TL;DR**
> Vue 컴포넌트는 생성 → 마운트 → 업데이트 → 소멸 네 단계를 거치며, 각 단계 전후에 훅 함수를 등록해 원하는 작업을 실행할 수 있다.

---

## Vue 생명주기를 왜 쓰는지 감 잡기

컴포넌트가 화면에 나타나는 순간 서버에서 데이터를 불러오거나, 제거되는 순간 타이머를 멈춰야 하는 상황이 자주 있다. Vue는 이런 "특정 시점에 코드를 실행"하는 기능을 생명주기 훅으로 제공한다. 훅을 모르면 데이터를 너무 일찍 불러오거나(DOM이 아직 없는 상태), 정리를 깜빡해 메모리가 새는 버그를 만든다.

초보자는 처음에 이렇게 이해하면 된다.

`생성(Creation) → 마운트(Mounting) → 업데이트(Updating) → 소멸(Destruction)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| created | 컴포넌트 인스턴스가 만들어진 직후. 데이터는 준비됐지만 DOM은 아직 없다. API 호출 시작점. |
| mounted | 컴포넌트가 DOM에 삽입된 직후. DOM을 직접 건드리거나 외부 라이브러리를 초기화할 때 쓴다. |
| beforeUpdate | 반응형 데이터가 바뀌어 DOM이 다시 그려지기 직전. 현재 DOM 상태를 저장할 때 유용. |
| updated | DOM 업데이트가 완료된 직후. 업데이트 결과에 따른 추가 작업을 여기서 한다. |
| beforeDestroy | 컴포넌트가 제거되기 직전. 타이머·이벤트 리스너·구독 정리는 반드시 여기서 한다. |

## 예를 들어 설명하면

1초마다 시각을 갱신하는 컴포넌트. 마운트되면 인터벌을 시작하고, 소멸 직전에 정리한다.

```javascript
new Vue({
  data() {
    return { message: '로딩 중...' };
  },
  mounted() {
    this.intervalId = setInterval(() => {
      this.message = new Date().toLocaleTimeString();
    }, 1000);
  },
  beforeDestroy() {
    clearInterval(this.intervalId); // 이 줄이 없으면 메모리 누수
  },
  template: '<div>{{ message }}</div>'
}).$mount('#app');
```

`mounted`에서 시작한 인터벌은 반드시 `beforeDestroy`에서 멈춰야 한다. 컴포넌트가 DOM에서 사라져도 인터벌은 계속 실행되기 때문이다.

## 이 단계에서 중요한 판단 기준

DOM이 필요 없는 초기화(데이터 fetch, 상태 세팅)는 `created`에서, DOM이 필요한 초기화(라이브러리 연결, 엘리먼트 크기 측정)는 `mounted`에서 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**데이터 초기화는 created, DOM 접근은 mounted, 정리는 beforeDestroy — 이 세 훅이 가장 자주 쓰인다.**

## 나중에 더 깊게 들어가면

- Vue 3 Composition API의 `onMounted`, `onUnmounted` 훅으로 같은 로직 표현하기
- `activated` / `deactivated` — `<keep-alive>`로 캐시된 컴포넌트에만 적용되는 특수 훅
- Vue 3에서 `beforeDestroy` / `destroyed` 대신 `beforeUnmount` / `unmounted`로 이름이 바뀐 이유

---

**원본:** [Vue.js Lifecycle Explained — memoryhub.tistory.com/254](https://memoryhub.tistory.com/254)
