# Vue 렌더링 최적화 — 왜 신경 써야 하는가

> **TL;DR**
> Vue는 데이터가 바뀔 때마다 화면을 다시 그리는데, 불필요한 재렌더링을 줄이는 것이 성능의 핵심이다.

---

## Vue 렌더링 최적화를 왜 쓰는지 감 잡기

Vue는 데이터가 바뀌면 자동으로 화면을 업데이트한다. 편리하지만, 아무 생각 없이 쓰면 컴포넌트가 필요 이상으로 자주 다시 그려진다. 목록이 1000개짜리라면 스크롤할 때마다 화면이 버벅인다. 최적화는 "언제 다시 그릴지"와 "얼마나 적게 그릴지"를 제어하는 작업이다.

초보자는 처음에 이렇게 이해하면 된다.

`데이터 변경 → Vue가 감지 → 해당 컴포넌트 재렌더링 → DOM 업데이트`

최적화란 이 흐름에서 불필요한 단계를 잘라내는 것이다.

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Computed Property | 다른 데이터를 기반으로 계산되는 값. 의존 데이터가 안 바뀌면 이전 결과를 그대로 돌려준다 (캐싱). |
| v-once | 이 태그는 처음 한 번만 렌더링하고, 이후 데이터가 바뀌어도 다시 그리지 않는다. |
| Functional Component | 자체 상태(state)가 없는 순수 표시용 컴포넌트. 인스턴스를 만들지 않아 더 가볍다. |
| Lazy Loading | 컴포넌트를 처음부터 다 불러오지 않고, 실제로 필요한 순간에 내려받는 방식. |
| Virtual Scrolling | 화면에 보이는 항목만 DOM에 렌더링하고 나머지는 생략한다. 1만 개 목록도 부드럽게 처리한다. |

## 예를 들어 설명하면

검색창에 글자를 입력할 때마다 API를 호출하면 서버에 과부하가 걸린다. `debounce`로 입력이 잠시 멈춘 뒤에만 호출하도록 제한한다.

```javascript
import { debounce } from 'lodash';

export default {
  methods: {
    search: debounce(function () {
      // 입력 멈춘 후 300ms 뒤 한 번만 실행
      this.fetchResults(this.query);
    }, 300)
  }
};
```

목록 렌더링에서는 `:key`를 반드시 붙여야 Vue가 어떤 항목이 바뀌었는지 추적할 수 있다. key가 없으면 전체 목록을 다시 그린다.

## 이 단계에서 중요한 판단 기준

재렌더링이 실제로 느린지 Vue Devtools의 Performance 탭으로 먼저 측정하고, 병목이 확인된 컴포넌트에만 최적화를 적용한다.

## 한 줄 요약 — 이것만 기억하면 된다

**computed로 캐싱하고, v-once로 정적 콘텐츠를 고정하고, 목록엔 반드시 key를 붙인다.**

## 나중에 더 깊게 들어가면

- `defineAsyncComponent`를 이용한 코드 분할(Code Splitting) 전략
- `vue-virtual-scroller`로 대용량 목록 처리하기
- Nuxt.js SSR로 초기 로드 시간 단축하기

---

**원본:** [Optimizing rendering in Vue — memoryhub.tistory.com/209](https://memoryhub.tistory.com/209)
