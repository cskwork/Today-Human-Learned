# React 컴포넌트란 무엇인가 — 일반 함수와 뭐가 다른가

> **TL;DR**
> React 컴포넌트는 UI 조각을 반환하도록 설계된 특수 함수다. 일반 JS 함수와 달리 JSX를 반환하고, 상태(state)를 가지며, 생명주기를 갖는다.

---

## React 컴포넌트를 왜 쓰는지 감 잡기

웹 페이지를 만들 때 버튼, 헤더, 카드 같은 요소가 반복된다. HTML을 매번 복붙하면 수정할 때 모든 곳을 고쳐야 한다. React 컴포넌트는 이 반복 조각을 재사용 가능한 단위로 캡슐화한다. 컴포넌트 하나를 수정하면 그것이 쓰인 모든 곳이 바뀐다.

일반 JavaScript 함수는 계산이나 데이터 처리를 위한 도구다. React 컴포넌트는 거기서 한 발 더 나아가 "화면의 이 부분이 어떻게 생겨야 하는가"를 선언한다.

`핵심 흐름: props 입력 → 컴포넌트 함수 실행 → JSX 반환 → DOM 렌더링`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| JSX | HTML처럼 생긴 JavaScript 문법. 컴포넌트가 반환하는 UI 설명서다. |
| Props | 부모 컴포넌트가 자식에게 넘겨주는 읽기 전용 데이터. 함수의 인자와 같다. |
| State | 컴포넌트 내부에서 관리하는 데이터. 바뀌면 화면이 다시 그려진다. |
| Hook | 함수형 컴포넌트에서 state나 생명주기 기능을 쓸 수 있게 해주는 함수. `useState`, `useEffect` 등. |
| 렌더링 | 컴포넌트가 반환한 JSX를 실제 DOM으로 변환하는 과정. |

## 예를 들어 설명하면

```jsx
// React 컴포넌트 — JSX를 반환하고 state를 갖는다
function Counter() {
    const [count, setCount] = React.useState(0);
    return (
        <div>
            <p>클릭 수: {count}</p>
            <button onClick={() => setCount(count + 1)}>클릭</button>
        </div>
    );
}

// 일반 JS 함수 — 숫자를 반환한다, UI와 무관하다
function add(a, b) {
    return a + b;
}
```

`Counter`는 버튼을 클릭할 때마다 `count`가 바뀌고, React가 알아서 화면을 다시 그린다. 일반 함수 `add`는 그런 연결이 없다. 두 가지 모두 JavaScript 함수지만, React 컴포넌트는 React 런타임과 연결되어 있어 동작 방식이 다르다.

## 이 단계에서 중요한 판단 기준

컴포넌트 이름은 반드시 대문자로 시작해야 한다. 소문자로 시작하면 React가 HTML 태그로 인식해 예상대로 동작하지 않는다.

## 한 줄 요약 — 이것만 기억하면 된다

**React 컴포넌트는 JSX를 반환하고 state로 UI를 제어하는 함수다. 일반 함수는 데이터를 처리하고 값을 반환할 뿐이다.**

## 나중에 더 깊게 들어가면

- `useEffect`로 컴포넌트 생명주기 다루기
- 컴포넌트 트리와 단방향 데이터 흐름
- React.memo와 불필요한 리렌더링 방지

---

**원본:** [What is a React component, and how does it differ from a regular JavaScript function? — https://memoryhub.tistory.com/252](https://memoryhub.tistory.com/252)
