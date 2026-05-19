+++
title = "React Props — 부모가 자식에게 데이터를 전달하는 방법"
date = "2024-06-12"
description = "Props는 컴포넌트 외부에서 전달하는 읽기 전용 데이터다. 함수의 인자와 같은 개념이다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Props는 컴포넌트 외부에서 전달하는 읽기 전용 데이터다. 함수의 인자와 같은 개념이다.

---

## Props를 왜 쓰는지 감 잡기

React에서 UI는 컴포넌트 조각들로 구성된다. 같은 모양이지만 내용이 다른 카드, 배지, 메시지 박스 같은 것들이다. Props는 이 조각들을 재사용 가능하게 만드는 핵심 수단이다. 컴포넌트를 찍어내는 틀이라고 생각하면, Props는 틀에 채워 넣는 재료다.

컴포넌트를 함수로 보면 Props는 그 함수의 인자(parameter)와 정확히 같은 역할을 한다.

`핵심 흐름: 부모 컴포넌트 → props 전달 → 자식 컴포넌트가 받아서 화면에 출력`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 컴포넌트 | 화면의 한 조각을 담당하는 함수. 재사용 가능한 UI 단위 |
| Props | 부모 컴포넌트가 자식에게 넘기는 데이터 묶음. 읽기 전용 |
| JSX | JS 파일 안에서 HTML처럼 쓰는 문법. React가 이걸 실제 DOM으로 변환한다 |
| export default | 다른 파일에서 이 컴포넌트를 불러다 쓸 수 있게 내보내는 구문 |
| import | 다른 파일에서 컴포넌트나 라이브러리를 가져오는 구문 |

## 예를 들어 설명하면

`message`라는 prop을 받아 화면에 출력하는 컴포넌트를 만든다.

```jsx
// MessageComponent.jsx
const MessageComponent = (props) => {
  return <div>{props.message}</div>;
};

export default MessageComponent;
```

부모 컴포넌트에서 이 컴포넌트를 사용할 때 `message` 값을 속성처럼 넘긴다.

```jsx
// App.jsx
import MessageComponent from './MessageComponent';

function App() {
  return (
    <div>
      <MessageComponent message="Hello, World!" />
      <MessageComponent message="두 번째 메시지" />
    </div>
  );
}
```

같은 컴포넌트를 두 번 사용하지만 내용은 다르다. Props가 없다면 내용마다 별도 컴포넌트를 만들어야 한다.

## 이 단계에서 중요한 판단 기준

Props는 부모에서 자식으로만 흐르고, 자식이 직접 수정할 수 없다. 값을 바꿔야 한다면 Props가 아닌 State를 써야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Props는 컴포넌트를 재사용 가능하게 만드는 외부 입력값이며, 함수의 인자와 같다.**

## 나중에 더 깊게 들어가면

- Props와 State의 차이 — 언제 State를 써야 하는가
- Props 타입 검증 (PropTypes, TypeScript)
- 여러 단계를 거쳐 전달하는 Props Drilling 문제와 Context API

---

**원본:** [Create a simple React component that displays a message passed as a prop — memoryhub.tistory.com/278](https://memoryhub.tistory.com/278)
