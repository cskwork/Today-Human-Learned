+++
title = "JSX — JavaScript 안에 HTML을 쓰는 이유"
date = "2024-06-10"
description = "JSX는 JavaScript 파일 안에서 HTML처럼 생긴 문법을 쓸 수 있게 해주는 확장 문법으로, React가 UI를 선언적으로 표현하는 방식이다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> JSX는 JavaScript 파일 안에서 HTML처럼 생긴 문법을 쓸 수 있게 해주는 확장 문법으로, React가 UI를 선언적으로 표현하는 방식이다.

---

## JSX를 왜 쓰는지 감 잡기

React 이전에는 JavaScript로 화면 요소를 만들 때 `document.createElement('h1')` 같은 코드를 직접 썼다. 구조가 복잡해질수록 코드가 길어지고, 어떤 화면이 만들어지는지 한눈에 파악하기 어려웠다. JSX는 이 문제를 해결하려고 나왔다. HTML 구조를 JavaScript 안에서 그대로 쓸 수 있어서, 코드만 봐도 결과 화면이 떠오른다.

JSX 자체는 브라우저가 이해하지 못한다. Babel 같은 도구가 JSX를 일반 JavaScript로 변환한 뒤 브라우저에서 실행된다.

초보자는 처음에 이렇게 이해하면 된다.

`JSX 작성 → Babel 변환 → 일반 JS → 브라우저 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| JSX | JavaScript 안에서 HTML처럼 보이는 문법. `.js` 파일 안에 `<div>` 같은 태그를 쓸 수 있다. |
| Babel | JSX를 브라우저가 이해할 수 있는 일반 JavaScript로 변환해주는 컴파일러. |
| Component | UI의 조각. 함수 하나가 특정 화면 부분을 반환하도록 만든 것. |
| Props | 부모 컴포넌트가 자식 컴포넌트에게 전달하는 데이터. HTML 속성처럼 생겼다. |
| 표현식 (Expression) | `{}` 안에 넣는 JavaScript 값이나 계산식. JSX 안에서 동적 데이터를 출력할 때 쓴다. |

## 예를 들어 설명하면

쇼핑몰 상품 목록 화면을 만든다고 하자. 상품 이름 배열을 받아 목록으로 보여주는 컴포넌트를 JSX로 표현하면 이렇다.

```jsx
function ItemList(props) {
  return (
    <ul>
      {props.items.map((item) => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
}

const items = ['사과', '바나나', '체리'];
// 사용: <ItemList items={items} />
```

`{}` 안에 `.map()`을 넣어 배열을 JSX 요소로 바꿨다. `key`는 React가 목록 변경을 추적하는 데 필수다.

## 이 단계에서 중요한 판단 기준

JSX는 문법 편의 도구일 뿐이다. JSX를 쓴다고 React가 더 빨라지지 않는다. 중요한 것은 컴포넌트를 어떻게 쪼개느냐다.

## 한 줄 요약 — 이것만 기억하면 된다

**JSX는 JavaScript 안에서 UI 구조를 HTML처럼 표현하는 문법이며, 실행 전에 Babel이 일반 JS로 변환한다.**

## 나중에 더 깊게 들어가면

- JSX가 내부적으로 변환되는 `React.createElement()` 구조 이해하기
- Conditional rendering 패턴 (`&&`, 삼항 연산자)
- `key` prop이 왜 필수이고, 어떤 값을 써야 하는지

---

**원본:** [JSX Introduced — memoryhub.tistory.com/251](https://memoryhub.tistory.com/251)
