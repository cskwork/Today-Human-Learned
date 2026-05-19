# 가장 긴 공통 접두사 찾기

> **TL;DR**
> 가장 짧은 문자열을 기준으로 한 글자씩 모든 문자열과 비교하고, 불일치가 나오는 순간 그 이전까지를 반환하면 된다.

---

## 공통 접두사를 왜 찾는지 감 잡기

접두사(prefix)란 문자열 앞부분에서 시작하는 부분 문자열이다. 예를 들어 파일 경로 `/home/user/docs`와 `/home/user/music`의 공통 접두사는 `/home/user`다. 자동 완성, 파일 탐색기, 검색 인덱스 등에서 여러 문자열의 공통 시작 부분을 빠르게 찾아야 할 때 이 알고리즘이 쓰인다.

핵심 제약은 공통 접두사의 길이가 가장 짧은 문자열을 넘을 수 없다는 것이다. 그래서 먼저 가장 짧은 문자열을 찾고, 그 길이 범위 안에서만 비교한다.

`핵심 흐름: 최단 문자열 선택 → 위치별 문자 비교 → 불일치 발견 시 즉시 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 접두사(prefix) | 문자열의 맨 앞에서 시작하는 부분. "flower"의 접두사는 "f", "fl", "flo" 등이다. |
| 조기 종료(early termination) | 결과가 확정되는 순간 반복을 바로 멈추는 것. 불필요한 비교를 줄인다. |
| `min(strs, key=len)` | 리스트에서 길이가 가장 짧은 문자열을 찾는 Python 표현 |
| 인덱스(index) | 문자열에서 문자의 위치 번호. 0부터 시작한다. |
| 슬라이싱(slicing) | `s[:i]`처럼 문자열의 일부를 잘라내는 Python 문법 |

## 예를 들어 설명하면

`["flower", "flow", "flight"]`에서 공통 접두사를 찾는 과정이다.

```python
def longestCommonPrefix(strs):
    if not strs:
        return ""
    shortest_str = min(strs, key=len)   # "flow"
    for i in range(len(shortest_str)):
        current_char = shortest_str[i]
        for string in strs:
            if string[i] != current_char:
                return shortest_str[:i]  # 불일치 직전까지 반환
    return shortest_str
```

비교 과정:
- 인덱스 0: f, f, f — 일치
- 인덱스 1: l, l, l — 일치
- 인덱스 2: o, o, i — "flight"에서 'i'가 다름, 즉시 `"fl"` 반환

`["dog", "racecar", "car"]`은 인덱스 0에서 d, r, c가 모두 다르므로 빈 문자열을 반환한다.

## 이 단계에서 중요한 판단 기준

가장 짧은 문자열 길이를 넘어서는 비교는 의미가 없으므로, 항상 최단 문자열을 탐색 범위의 상한으로 삼는다.

## 한 줄 요약 — 이것만 기억하면 된다

**가장 짧은 문자열 기준으로 한 글자씩 전체 비교, 처음 불일치에서 멈추면 공통 접두사가 나온다.**

## 나중에 더 깊게 들어가면

- 이진 탐색으로 접두사 길이를 줄여가는 O(S log n) 방법
- 트라이(Trie) 자료구조를 사용한 접두사 검색
- 유니코드 문자열에서 다중 바이트 문자를 처리할 때 주의할 점

---

**원본:** LeetCode - 14. Longest Common Prefix — https://memoryhub.tistory.com/176
