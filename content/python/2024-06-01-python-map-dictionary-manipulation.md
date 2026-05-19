+++
title = "Python 딕셔너리 — 키-값 쌍으로 데이터 다루기"
date = "2024-06-01"
description = "딕셔너리는 이름(키)으로 값을 꺼내는 데이터 구조다. 리스트처럼 순서가 아니라 의미 있는 이름으로 접근한다."
tags = ["python"]
categories = ["python"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 딕셔너리는 이름(키)으로 값을 꺼내는 데이터 구조다. 리스트처럼 순서가 아니라 의미 있는 이름으로 접근한다.

---

## 딕셔너리를 왜 쓰는지 감 잡기

리스트는 순서(인덱스)로 값을 찾는다. 반면 딕셔너리는 이름(키)으로 값을 찾는다. 연락처 앱을 떠올리면 쉽다. "이름"으로 "전화번호"를 꺼내는 구조가 딕셔너리다. 숫자 인덱스 대신 의미 있는 단어를 쓰므로 코드 가독성이 높아진다. API 응답, 설정 파일, 데이터베이스 레코드 등 현실의 데이터 대부분이 이 구조를 따른다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 선언(키-값 쌍) → 키로 접근 → 키로 수정/삭제`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 키(key) | 값을 꺼낼 때 쓰는 이름. 중복 불가 |
| 값(value) | 키에 연결된 실제 데이터. 어떤 타입도 가능 |
| 중괄호 `{}` | 딕셔너리를 만드는 기호 |
| `del` | 키-값 쌍을 아예 삭제하는 키워드 |
| `.pop(key)` | 키-값 쌍을 삭제하면서 삭제된 값을 반환하는 메서드 |

## 예를 들어 설명하면

과일 가격표를 딕셔너리로 관리하는 상황이다.

```python
fruit_prices = {"apple": 1.2, "banana": 0.5, "cherry": 2.5}

# 조회
print(fruit_prices["banana"])   # 0.5

# 수정
fruit_prices["cherry"] = 2.8

# 추가
fruit_prices["elderberry"] = 1.5

# 삭제 (반환값 필요 없을 때)
del fruit_prices["apple"]

# 삭제 (반환값이 필요할 때)
removed = fruit_prices.pop("banana")
print(removed)   # 0.5
```

없는 키로 접근하면 `KeyError`가 발생한다. 안전하게 꺼내려면 `fruit_prices.get("apple", 0)` 처럼 기본값을 지정한다.

## 이 단계에서 중요한 판단 기준

데이터를 순서가 아니라 이름으로 꺼내야 한다면 딕셔너리를 선택하라.

## 한 줄 요약 — 이것만 기억하면 된다

**키로 값을 만들고, 키로 값을 바꾸고, 키로 값을 지운다.**

## 나중에 더 깊게 들어가면

- 딕셔너리 컴프리헨션 `{k: v for k, v in ...}`
- `.keys()`, `.values()`, `.items()` 메서드로 순회하기
- `defaultdict`, `Counter` 등 `collections` 모듈의 확장 딕셔너리

---

**원본:** [Python Map(Dictionary) Manipulation](https://memoryhub.tistory.com/173)
