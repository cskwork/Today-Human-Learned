+++
title = "Javascript Promise — 비동기 처리를 약속으로 묶기"
date = "2024-05-25"
description = "Promise는 \"나중에 결과를 줄게\"라는 약속 객체로, 중첩 콜백 없이 순차 비동기 작업을 선형으로 작성할 수 있다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Promise는 "나중에 결과를 줄게"라는 약속 객체로, 중첩 콜백 없이 순차 비동기 작업을 선형으로 작성할 수 있다.

---

## Promise를 왜 쓰는지 감 잡기

JavaScript는 파일 읽기, 네트워크 요청처럼 "결과가 나중에 오는" 작업을 자주 다룬다. 전통적인 해결책은 콜백 함수였다. 하나의 작업이 끝나면 다음 함수를 인수로 넘기는 방식인데, 단계가 4~5개를 넘으면 중첩이 깊어져 읽기도 고치기도 어려운 구조가 된다. 이를 콜백 지옥(callback hell)이라 부른다.

Promise는 ES6에서 이 문제를 해결하기 위해 도입됐다. 비동기 작업의 결과를 나중에 꺼낼 수 있는 상자처럼 생각하면 된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 작업 시작 → Promise 반환 → (성공) resolve / (실패) reject → .then() / .catch() 처리`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Promise | 비동기 작업의 결과를 담는 객체. 아직 결과가 없을 수도 있다. |
| pending | 아직 결과가 정해지지 않은 대기 상태 |
| fulfilled | 작업이 성공적으로 끝난 상태. resolve()가 호출됐다. |
| rejected | 작업이 실패한 상태. reject()가 호출됐다. |
| .then() / .catch() | fulfilled이면 .then(), rejected이면 .catch()가 실행된다. |

## 예를 들어 설명하면

이미지를 불러와서 크기를 줄이고 필터를 적용한 뒤 저장하는 흐름이 있다고 가정한다.

```javascript
getImage("./image.png")
  .then(image => compressImage(image))
  .then(compressed => applyFilter(compressed))
  .then(filtered => saveImage(filtered))
  .then(() => console.log("저장 완료"))
  .catch(err => { throw new Error(err) });
```

각 `.then()`은 이전 단계가 성공해야 실행된다. 하나라도 실패하면 `.catch()`로 바로 건너뛴다. 중첩 없이 위에서 아래로 읽히는 선형 구조다.

## 이 단계에서 중요한 판단 기준

순서가 있는 비동기 작업이 2단계 이상이면 Promise 체이닝을 쓴다. 독립적인 여러 작업을 동시에 돌리려면 `Promise.all()`을 떠올린다.

## 한 줄 요약 — 이것만 기억하면 된다

**Promise는 "나중에 줄게"라는 약속 객체이고, .then()과 .catch()로 그 결과를 받아 처리한다.**

## 나중에 더 깊게 들어가면

- async/await 문법: Promise 체이닝을 동기 코드처럼 작성하는 방법
- Promise.all(), Promise.race(): 여러 Promise를 묶어 제어하는 방법
- 이벤트 루프와 마이크로태스크 큐: Promise가 언제 실행되는지 내부 원리

---

**원본:** [Javascript Promise — https://memoryhub.tistory.com/64](https://memoryhub.tistory.com/64)
