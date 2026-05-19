# Python 문자열 순회 — 글자 하나씩 처리하기

> **TL;DR**
> 문자열을 `for` 루프에 넣으면 글자 하나씩 꺼낼 수 있다. 인덱스도 함께 필요하면 `enumerate`를 쓴다.

---

## 문자열 순회를 왜 쓰는지 감 잡기

문자열은 글자들의 순서 있는 집합이다. 비밀번호의 특수문자 수를 세거나, 로그 메시지에서 특정 문자를 찾거나, 텍스트를 암호화할 때 글자 하나씩 처리해야 한다. Python은 문자열 자체를 반복 가능한(iterable) 객체로 취급하기 때문에 `for char in "Python"` 만으로 순회가 가능하다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 문자열 → 루프 → 글자 하나씩 꺼냄 → 처리`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| `for` 루프 | 컬렉션의 원소를 하나씩 꺼내 반복하는 구문 |
| `while` 루프 | 조건이 참인 동안 반복. 인덱스를 직접 관리해야 한다 |
| `enumerate(iterable)` | 원소와 함께 인덱스(번호)도 함께 반환하는 내장 함수 |
| `len(s)` | 문자열의 전체 글자 수를 반환 |
| 인덱스(index) | 문자열에서 글자 위치를 나타내는 0부터 시작하는 번호 |

## 예를 들어 설명하면

문자열에서 모음(a, e, i, o, u)이 몇 개인지 세는 실용적인 예시다.

```python
def count_vowels(text):
    vowels = "aeiouAEIOU"
    count = 0
    for char in text:
        if char in vowels:
            count += 1
    return count

print(count_vowels("Python Programming"))  # 4
```

인덱스도 필요한 경우 `enumerate`를 쓴다.

```python
word = "Python"
for index, char in enumerate(word):
    print(f"{index}: {char}")
# 0: P
# 1: y
# ...
```

`while` 루프로도 같은 작업이 가능하지만, 인덱스를 직접 증가시켜야 하므로 코드가 길어진다. 특별한 이유가 없으면 `for` 루프를 쓴다.

## 이 단계에서 중요한 판단 기준

인덱스가 필요하면 `enumerate`, 글자만 필요하면 `for char in string`으로 충분하다.

## 한 줄 요약 — 이것만 기억하면 된다

**`for char in string`이 가장 단순하고, 위치 번호도 필요하면 `enumerate`를 덧붙인다.**

## 나중에 더 깊게 들어가면

- 리스트 컴프리헨션으로 문자 필터링 `[c for c in s if c.isalpha()]`
- `str.join()`으로 처리된 글자들을 다시 합치기
- 유니코드와 멀티바이트 문자 처리 (`str` vs `bytes`)

---

**원본:** [Python Loop String](https://memoryhub.tistory.com/174)
