# LinkedHashMap을 구분자로 이어 붙인 문자열로 변환하기

> **TL;DR**
> `LinkedHashMap`의 키-값 쌍을 삽입 순서 그대로 유지하면서 원하는 구분자로 연결된 문자열로 만들려면 `StringBuilder`로 직접 순회하거나 `String.join` 계열 API를 쓴다.

---

## 이 변환을 왜 하는지 감 잡기

캐시 키 생성, 쿼리 파라미터 직렬화, 로그 메시지 구성 등 실무에서는 여러 키-값 쌍을 하나의 문자열로 합쳐야 할 때가 자주 있다. 단순 `HashMap`을 쓰면 순서가 보장되지 않아 결과가 실행마다 달라질 수 있다. `LinkedHashMap`은 삽입 순서를 보장하므로, 키-값 순서가 중요한 문자열 직렬화에 적합하다.

`핵심 흐름: LinkedHashMap 생성 → entrySet 순회 → StringBuilder로 조립 → toString`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| LinkedHashMap | 삽입 순서를 보장하는 HashMap. 꺼낼 때마다 넣은 순서대로 나온다 |
| entrySet() | 맵의 키-값 쌍 전체를 Set으로 반환. 순회할 때 사용 |
| Map.Entry | 키 하나와 값 하나를 묶은 단위. `getKey()`, `getValue()`로 접근 |
| StringBuilder | 문자열을 반복해서 이어 붙일 때 쓰는 가변 버퍼. `+` 연산보다 훨씬 빠름 |
| toString() | StringBuilder가 쌓은 내용을 불변 String으로 변환하는 마지막 단계 |

## 예를 들어 설명하면

키와 값을 모두 언더스코어로 이어 붙이는 예시다.
맵이 `{triggerIdx=IDX, keywords=스톤헨지, category=돌}`이면 결과는 `triggerIdx_IDX_keywords_스톤헨지_category_돌`이다.

```java
LinkedHashMap<String, String> map = new LinkedHashMap<>();
map.put("triggerIdx", "IDX");
map.put("keywords", "스톤헨지");
map.put("category", "돌");

StringBuilder result = new StringBuilder();
for (Map.Entry<String, String> entry : map.entrySet()) {
    if (result.length() > 0) {
        result.append("_");
    }
    result.append(entry.getKey()).append("_").append(entry.getValue());
}

System.out.println(result.toString());
// 출력: triggerIdx_IDX_keywords_스톤헨지_category_돌
```

`result.length() > 0` 조건은 첫 번째 항목 앞에 불필요한 구분자가 붙지 않도록 막는다.

## 이 단계에서 중요한 판단 기준

키와 값의 구분자가 같을 때(예: 모두 `_`) 나중에 다시 파싱하기 어려워진다. 직렬화 결과를 역직렬화할 가능성이 있다면 키-값 사이 구분자와 항목 간 구분자를 다르게 잡는 것이 안전하다.

## 한 줄 요약 — 이것만 기억하면 된다

**삽입 순서가 중요한 키-값 직렬화에는 `LinkedHashMap` + `StringBuilder` 조합으로 entrySet을 순회하면 충분하다.**

## 나중에 더 깊게 들어가면

- `Collectors.joining()`: Stream API로 같은 작업을 더 짧게 표현하는 방법
- `String.join()` vs `StringJoiner`: 단순 값 목록 연결에 적합한 표준 API
- 역직렬화(파싱): 구분자가 포함된 값이 있을 때 안전하게 다시 쪼개는 방법

---

**원본:** [Convert LinkedHashMap to String with Separator — https://memoryhub.tistory.com/189](https://memoryhub.tistory.com/189)
