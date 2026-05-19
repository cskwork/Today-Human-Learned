+++
title = "Java Optional.flatMap() — map()이랑 뭐가 다른 걸까"
date = "2025-04-14"
description = "map()은 변환 결과를 Optional로 한 번 더 감싸고, flatMap()은 변환 함수가 이미 Optional을 반환할 때 중첩 없이 그대로 꺼낸다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> map()은 변환 결과를 Optional로 한 번 더 감싸고, flatMap()은 변환 함수가 이미 Optional을 반환할 때 중첩 없이 그대로 꺼낸다.

---

## 이 주제를 왜 쓰는지 감 잡기

null 체크를 연달아 쓰면 코드가 if문 중첩으로 망가진다. Java 8의 Optional은 null일 수 있는 값을 감싸서, 체인 형태로 변환과 null 처리를 한 줄로 이어 쓸 수 있게 해 준다.

그런데 Optional 안의 값을 변환할 때 두 가지 메서드가 있다. 변환 함수가 평범한 값(String, int 등)을 반환하면 map()을 쓰면 된다. 문제는 변환 함수가 Optional을 반환할 때다. 이때 map()을 쓰면 Optional 안에 Optional이 들어가는 이중 포장이 생긴다. flatMap()은 이 이중 포장을 막아 준다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Optional<T> → (변환 함수) → map: Optional<U> / flatMap: Optional<U> (중첩 제거)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Optional | null일 수도 있는 값을 담는 컨테이너 — 비어 있거나 값이 하나 있다 |
| map() | Optional 안의 값에 함수를 적용하고, 결과를 Optional로 다시 감싼다 |
| flatMap() | 함수가 Optional을 반환할 때 사용 — 결과를 그대로 반환해 중첩을 없앤다 |
| 평탄화(flatten) | Optional 안의 Optional을 하나의 Optional로 합치는 것 |
| orElse() | Optional이 비어 있을 때 대신 반환할 기본값을 지정한다 |

## 예를 들어 설명하면

`Product` → `Person` → `Job` 순서로 탐색해서 직업 이름을 꺼내는 상황이다. 각 참조는 Optional로 감싸져 있다.

```java
// map()만 쓰면 컴파일 에러 — 첫 번째 map 결과가 Optional<Optional<Person>>이 됨
Optional<String> jobName = optProduct
    .map(Product::getPerson)  // Optional<Optional<Person>> 발생
    .map(Person::getJob)      // 에러: Optional에는 getJob()이 없다
    .map(Job::getJobName);

// flatMap()으로 해결 — 각 단계의 Optional이 평탄화됨
Optional<String> jobName = optProduct
    .flatMap(Product::getPerson)  // Optional<Person>
    .flatMap(Person::getJob)      // Optional<Job>
    .map(Job::getJobName);        // Optional<String>

System.out.println(jobName.orElse("직업 정보 없음"));
```

마지막 단계에서 `getJobName()`은 String을 반환하므로 map()을 쓰는 것이 맞다. 그 앞 단계들은 Optional을 반환하므로 flatMap()을 쓴다.

## 이 단계에서 중요한 판단 기준

변환 함수의 반환 타입을 보면 즉시 결정할 수 있다 — Optional을 반환하면 flatMap(), 일반 값을 반환하면 map().

## 한 줄 요약 — 이것만 기억하면 된다

**연결 고리가 Optional을 반환하면 flatMap(), 일반 값을 반환하면 map() — 반환 타입으로 판단한다.**

## 나중에 더 깊게 들어가면

- Stream의 flatMap()과 비교 — 컬렉션 평탄화에 쓰이는 방식
- Optional을 남용하면 생기는 문제 — 필드 타입으로 Optional을 쓰면 안 되는 이유
- Optional.ifPresent(), Optional.filter() 등 나머지 API 활용법

---

**원본:** [Java Optional.flatMap(), 도대체 map()이랑 뭐가 다른 걸까?](https://memoryhub.tistory.com/551)
