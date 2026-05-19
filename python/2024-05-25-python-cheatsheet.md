# Python CheatSheet — 핵심 문법 한눈에 보기

> **TL;DR**
> Python의 자료형, 키워드, 자료구조, 제어 흐름, 함수를 빠르게 찾아볼 수 있는 참조 정리다.

---

## Python CheatSheet를 왜 쓰는지 감 잡기

Python은 인터프리터 언어로, 코드를 한 줄씩 즉시 실행한다. 문법이 간결해서 처음 배우기 쉽지만, 자료형과 자료구조 종류가 많아 한눈에 정리해 두면 실무에서 빠르게 찾아볼 수 있다. 웹 개발, 데이터 분석, 자동화 스크립트 등 어디서나 쓰이는 언어다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 데이터 담기(변수/자료형) → 조작하기(연산자/자료구조) → 흐름 제어(조건/반복) → 재사용(함수)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 인터프리터 | 코드를 한 줄씩 바로 실행하는 방식. 컴파일 없이 즉시 결과를 본다. |
| 들여쓰기 | Python에서 코드 블록을 나누는 방법. 중괄호 대신 공백/탭으로 구분한다. |
| 자료형 | 변수에 담긴 값의 종류. int, float, str, bool 등이 있다. |
| 자료구조 | 여러 값을 묶어 관리하는 방법. List, Tuple, Set, Dictionary가 대표적이다. |
| 키워드 | Python이 예약한 단어들. def, if, for, import 등 변수 이름으로 쓸 수 없다. |

## 예를 들어 설명하면

쇼핑몰 주문 시스템을 만든다고 하면, 각 자료구조는 이렇게 쓰인다.

```python
# List: 순서가 있고, 수정 가능한 주문 목록
orders = ["사과", "바나나", "체리"]

# Tuple: 바뀌면 안 되는 좌표나 설정값
point = (37.5, 127.0)

# Set: 중복 없는 방문자 ID 집합
visitors = {"user1", "user2", "user1"}  # {"user1", "user2"}로 저장됨

# Dictionary: 상품 코드와 가격을 키-값으로 관리
prices = {"apple": 1200, "banana": 800}
```

제어 흐름과 함수를 합치면 재사용 가능한 로직이 된다.

```python
def apply_discount(price: float, rate: float) -> float:
    if rate > 0:
        return price * (1 - rate)
    return price
```

## 이 단계에서 중요한 판단 기준

값이 바뀌면 안 되면 Tuple, 중복을 없애야 하면 Set, 순서와 수정이 필요하면 List, 이름으로 찾아야 하면 Dictionary를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Python의 네 가지 자료구조(List, Tuple, Set, Dict)의 특성을 구분하고, 상황에 맞게 선택하는 것이 Python 기초의 핵심이다.**

## 나중에 더 깊게 들어가면

- 리스트 컴프리헨션(`[x*2 for x in range(10)]`)으로 코드 단축하기
- 제너레이터(`yield`)와 이터레이터의 차이
- 예외 처리(`try / except / finally`)로 안정적인 프로그램 만들기

---

**원본:** [Python CheatSheet — https://memoryhub.tistory.com/56](https://memoryhub.tistory.com/56)
