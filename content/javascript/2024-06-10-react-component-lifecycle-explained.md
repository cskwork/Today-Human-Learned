+++
title = "React 컴포넌트 생명주기 — 태어나서 사라질 때까지"
date = "2024-06-10"
description = "React 컴포넌트는 마운트(생성) → 업데이트 → 언마운트(제거) 세 단계를 거치며, 각 단계에서 특정 작업을 실행할 수 있는 훅(hook)이 제공된다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> React 컴포넌트는 마운트(생성) → 업데이트 → 언마운트(제거) 세 단계를 거치며, 각 단계에서 특정 작업을 실행할 수 있는 훅(hook)이 제공된다.

---

## React 생명주기를 왜 쓰는지 감 잡기

컴포넌트가 화면에 나타날 때 API를 호출하고, 데이터가 바뀔 때 추가 처리를 하고, 화면에서 사라질 때 타이머를 정리해야 하는 상황이 자주 있다. 이런 작업을 "언제" 실행할지 알려면 컴포넌트의 생명주기를 이해해야 한다. 생명주기를 무시하면 메모리 누수나 API 중복 호출 같은 문제가 생긴다.

초보자는 처음에 이렇게 이해하면 된다.

`컴포넌트 생성(Mounting) → 데이터 변경(Updating) → 컴포넌트 제거(Unmounting)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Mounting | 컴포넌트가 처음 만들어져 화면(DOM)에 삽입되는 시점. |
| Unmounting | 컴포넌트가 화면에서 제거되는 시점. 정리(cleanup) 작업이 여기서 일어난다. |
| componentDidMount | 마운트 직후 한 번 실행되는 메서드. API 호출 시작점으로 쓴다. |
| componentDidUpdate | 상태나 props가 바뀐 후 실행된다. 이전 값과 현재 값을 비교할 수 있다. |
| componentWillUnmount | 컴포넌트가 사라지기 직전에 실행된다. 타이머 해제, 구독 취소에 쓴다. |

## 예를 들어 설명하면

매초 경과 시간을 표시하는 타이머 컴포넌트를 만든다. 마운트되면 타이머를 시작하고, 사라지면 타이머를 정리해야 한다.

```jsx
class Timer extends React.Component {
  constructor(props) {
    super(props);
    this.state = { seconds: 0 };
  }

  componentDidMount() {
    // 마운트 후 타이머 시작
    this.interval = setInterval(() => {
      this.setState((s) => ({ seconds: s.seconds + 1 }));
    }, 1000);
  }

  componentWillUnmount() {
    // 제거 전 타이머 정리 — 이 줄이 없으면 메모리 누수
    clearInterval(this.interval);
  }

  render() {
    return <h1>{this.state.seconds}초 경과</h1>;
  }
}
```

`componentWillUnmount`에서 `clearInterval`을 빠뜨리면, 컴포넌트가 화면에서 사라져도 타이머는 계속 돌아가면서 메모리를 소비한다.

## 이 단계에서 중요한 판단 기준

`componentDidMount`에서 시작한 모든 비동기 작업(타이머, 구독, 이벤트 리스너)은 반드시 `componentWillUnmount`에서 정리한다.

## 한 줄 요약 — 이것만 기억하면 된다

**시작은 componentDidMount에서, 정리는 componentWillUnmount에서 — 이 두 짝을 항상 함께 생각한다.**

## 나중에 더 깊게 들어가면

- 함수형 컴포넌트에서 `useEffect`로 생명주기 대체하기
- `shouldComponentUpdate`와 `React.memo`를 이용한 불필요한 재렌더링 방지
- `getSnapshotBeforeUpdate`가 필요한 상황 (스크롤 위치 복원 등)

---

**원본:** [React Component Lifecycle Explained — memoryhub.tistory.com/253](https://memoryhub.tistory.com/253)
