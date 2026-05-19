# Javascript Cheatsheet

> **TL;DR**
> JavaScript의 핵심 내장 객체 — Object와 Array — 의 주요 메서드를 용도별로 정리한 빠른 참조 카드다.

---

## JavaScript 내장 API를 왜 알아야 하는지 감 잡기

JavaScript로 실무 코드를 작성할 때 가장 많이 쓰는 것이 Object와 Array 메서드다. 직접 반복문을 짜는 대신 `map`, `filter`, `reduce` 같은 내장 메서드를 쓰면 코드가 짧고 의도가 명확해진다.

MDN 공식 문서가 기준이지만, 매번 찾기 번거로울 때 이 카드를 먼저 펼쳐 본다.

`핵심 흐름: 데이터 입력 → Object/Array 메서드로 변환/필터/집계 → 결과 사용`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Object.keys(obj) | 객체의 키 이름들을 배열로 돌려준다. `["name", "age"]` 형태. |
| Object.entries(obj) | 객체를 `[키, 값]` 쌍의 배열로 변환한다. for...of 순회에 유용하다. |
| arr.map(fn) | 배열의 모든 요소에 함수를 적용해 새 배열을 만든다. 원본을 바꾸지 않는다. |
| arr.filter(fn) | 함수가 true를 반환하는 요소만 남긴 새 배열을 만든다. |
| arr.reduce(fn, init) | 배열 요소를 누적 계산해 값 하나로 줄인다. 합계, 집계에 쓴다. |

## 예를 들어 설명하면

**Object 핵심 메서드**

```js
const user = { name: "Alice", age: 30, role: "admin" };

Object.keys(user);            // ["name", "age", "role"]
Object.values(user);          // ["Alice", 30, "admin"]
Object.entries(user);         // [["name","Alice"], ["age",30], ["role","admin"]]

// 객체 복사 (얕은 복사)
const copy = Object.assign({}, user);
// 또는
const copy2 = { ...user };

// 객체 동결 — 이후 수정 불가
Object.freeze(user);
```

**Array 핵심 메서드**

```js
const nums = [1, 2, 3, 4, 5];

nums.map(n => n * 2);           // [2, 4, 6, 8, 10]  — 변환
nums.filter(n => n % 2 === 0);  // [2, 4]             — 필터
nums.reduce((acc, n) => acc + n, 0); // 15            — 집계

nums.find(n => n > 3);          // 4  — 조건 첫 번째 요소
nums.some(n => n > 4);          // true
nums.every(n => n > 0);         // true

// 배열 합치기 / 자르기
[1, 2].concat([3, 4]);          // [1, 2, 3, 4]
nums.slice(1, 3);               // [2, 3]  — 원본 불변
nums.splice(1, 2);              // nums에서 인덱스 1부터 2개 제거 (원본 변경)
```

## 이 단계에서 중요한 판단 기준

`map`, `filter`, `reduce`는 원본 배열을 바꾸지 않지만 `splice`, `sort`, `reverse`는 원본을 직접 변경한다. 불변성이 필요한 상황에서는 반드시 전자를 쓴다.

## 한 줄 요약 — 이것만 기억하면 된다

**Object.keys/entries와 Array의 map·filter·reduce를 손에 익히면 JavaScript 데이터 처리의 80%는 해결된다.**

## 나중에 더 깊게 들어가면

- Promise와 async/await: 비동기 처리를 순서대로 읽히게 만드는 방법
- 구조 분해 할당(Destructuring)과 스프레드 연산자(`...`): 데이터 추출·합성 단축 문법
- Set과 Map: 중복 제거, 키-값 순서 보존이 필요할 때 Array와 Object 대신 쓰는 자료구조

---

**원본:** [Javascript Cheatsheet — https://memoryhub.tistory.com/58](https://memoryhub.tistory.com/58)
