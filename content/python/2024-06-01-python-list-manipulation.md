+++
title = "Python 리스트 조작 — 순서 있는 데이터 다루기"
date = "2024-06-01"
description = "Python 리스트는 순서가 있고 수정 가능한 컬렉션으로, 추가·삭제·접근 메서드 네댓 개만 알면 대부분의 작업을 처리할 수 있다."
tags = ["python"]
categories = ["python"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Python 리스트는 순서가 있고 수정 가능한 컬렉션으로, 추가·삭제·접근 메서드 네댓 개만 알면 대부분의 작업을 처리할 수 있다.

---

## 리스트를 왜 쓰는지 감 잡기

데이터가 하나가 아니라 여러 개일 때 각각 변수를 만드는 것은 비효율적이다. 리스트는 여러 값을 하나의 이름 아래 순서대로 묶어 관리하는 구조다. 장바구니 목록, 게시글 목록, 처리할 작업 목록처럼 "순서가 있는 것들의 모음"이라면 리스트가 적합하다.

Python 리스트는 서로 다른 자료형을 섞어 담을 수도 있고, 크기가 동적으로 늘고 준다. 한 번 정해지면 바꿀 수 없는 Tuple과 달리 추가·수정·삭제가 자유롭다.

`핵심 흐름: 리스트 선언 → 인덱스로 접근 → 메서드로 추가/삭제 → 반복문으로 순회`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 인덱스 | 리스트에서 각 요소의 위치 번호. 0부터 시작한다. `fruits[0]`은 첫 번째 요소다. |
| `append` | 리스트 맨 끝에 새 요소를 추가하는 메서드. |
| `insert` | 지정한 위치에 요소를 끼워 넣는 메서드. 기존 요소는 뒤로 밀린다. |
| `remove` | 값을 기준으로 첫 번째로 일치하는 요소를 삭제한다. |
| `pop` | 인덱스를 기준으로 요소를 꺼내면서 삭제한다. 기본값은 마지막 요소다. |

## 예를 들어 설명하면

도서 목록을 관리하는 예시다. 추가·삽입·삭제를 차례로 적용한다.

```python
books = ["1984", "The Great Gatsby", "Moby Dick"]

# 맨 끝에 추가
books.append("Pride and Prejudice")
# ["1984", "The Great Gatsby", "Moby Dick", "Pride and Prejudice"]

# 두 번째 자리에 삽입
books.insert(1, "War and Peace")
# ["1984", "War and Peace", "The Great Gatsby", "Moby Dick", "Pride and Prejudice"]

# 값으로 삭제
books.remove("Moby Dick")

# 인덱스로 꺼내기 (반환값으로 받을 수 있음)
removed = books.pop(2)
print(removed)   # The Great Gatsby
print(books)     # ["1984", "War and Peace", "Pride and Prejudice"]
```

## 이 단계에서 중요한 판단 기준

`remove`는 값이 없으면 `ValueError`를 발생시킨다. 삭제 전에 값이 있는지 확인하거나(`if x in list`), 예외 처리를 함께 쓰는 습관을 들인다.

## 한 줄 요약 — 이것만 기억하면 된다

**리스트는 순서 있는 가변 컬렉션이며, append/insert/remove/pop 네 가지 메서드로 대부분의 조작을 해결할 수 있다.**

## 나중에 더 깊게 들어가면

- 슬라이싱(`books[1:3]`)으로 부분 리스트 추출하기
- 리스트 컴프리헨션(`[x.upper() for x in books]`)으로 변환 간결하게 표현하기
- `sort()` / `sorted()`로 정렬하기와 두 함수의 차이 이해하기

---

**원본:** [Python List Manipulation — https://memoryhub.tistory.com/172](https://memoryhub.tistory.com/172)
