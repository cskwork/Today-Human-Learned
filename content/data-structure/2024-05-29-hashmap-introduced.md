+++
title = "HashMap — 키를 인덱스로 바꿔 즉시 찾아가는 구조"
date = "2024-05-29"
description = "HashMap은 해시 함수로 키를 배열 인덱스로 변환해 저장하고, 키만 알면 값을 O(1)에 꺼낼 수 있다."
tags = ["data-structure"]
categories = ["data-structure"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> HashMap은 해시 함수로 키를 배열 인덱스로 변환해 저장하고, 키만 알면 값을 O(1)에 꺼낼 수 있다.

---

## HashMap을 왜 쓰는지 감 잡기

배열에서 특정 값을 찾으려면 처음부터 하나씩 비교해야 한다(최악 N단계). 반면 HashMap은 키를 고정된 함수에 넣어 "몇 번 칸"인지 바로 계산한다. 도서관 사서에게 책 제목을 말하면 바로 위치를 알려주는 것과 같다. 제목이 곧 "키"이고, 위치가 곧 "값"이다.

HashMap은 검색 API의 자동완성, 캐싱, 중복 감지 등 "키로 값을 빠르게 꺼내야 하는" 거의 모든 곳에 쓰인다. 대부분의 언어가 기본 라이브러리로 제공한다(Python `dict`, Java `HashMap`, JavaScript `Map`).

`핵심 흐름: 키 입력 → 해시 함수로 인덱스 계산 → 해당 버킷에 접근 → 값 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 키(Key) | 값을 꺼낼 때 사용하는 고유 식별자. 이름표와 같다. |
| 값(Value) | 키에 연결된 실제 데이터. |
| 해시 함수(Hash Function) | 키를 받아 배열 인덱스(정수)를 돌려주는 함수. 같은 키는 항상 같은 인덱스를 반환한다. |
| 버킷(Bucket) | 해시 함수가 계산한 인덱스에 해당하는 배열 칸. 실제 데이터가 담긴다. |
| 충돌(Collision) | 서로 다른 두 키가 같은 인덱스로 계산될 때 발생. 체이닝(같은 버킷에 리스트 유지)으로 해결한다. |

## 예를 들어 설명하면

아래는 Python으로 간단한 HashMap을 직접 구현한 예제다. 실무에서는 `dict`를 쓰지만, 내부 원리를 이해하기 위해 직접 만들어 본다.

```python
bucket_size = 10
hashmap = [[] for _ in range(bucket_size)]

def simple_hash(key):
    return hash(key) % bucket_size

def insert(key, value):
    idx = simple_hash(key)
    for pair in hashmap[idx]:
        if pair[0] == key:       # 이미 키가 있으면 값만 업데이트
            pair[1] = value
            return
    hashmap[idx].append([key, value])

def get(key):
    idx = simple_hash(key)
    for pair in hashmap[idx]:
        if pair[0] == key:
            return pair[1]
    return None

insert("alice", 30)
insert("bob", 25)
print(get("alice"))  # 30
```

충돌이 나면 같은 버킷에 리스트로 쌓인다. 버킷 하나에 데이터가 몰리면 검색이 느려지므로, 실제 구현에서는 부하율(load factor)을 보고 버킷을 늘리는 리해시(rehash)를 수행한다.

## 이 단계에서 중요한 판단 기준

"키로 값을 O(1)에 꺼내야 하고 순서가 중요하지 않다면 HashMap이 첫 번째 선택이다."

## 한 줄 요약 — 이것만 기억하면 된다

**HashMap은 해시 함수로 키를 인덱스로 바꿔 값을 즉시 찾으므로, 평균 조회 비용이 O(1)이다.**

## 나중에 더 깊게 들어가면

- 충돌 해결 전략: 체이닝(Chaining) vs 오픈 어드레싱(Open Addressing)
- 부하율(Load Factor)과 리해시(Rehash)가 성능에 미치는 영향
- 순서를 보장하는 LinkedHashMap / 정렬된 키가 필요한 TreeMap과의 비교

---

**원본:** HashMap Introduced — https://memoryhub.tistory.com/162
