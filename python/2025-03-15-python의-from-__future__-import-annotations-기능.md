# Python의 `from __future__ import annotations` — 타입 힌트를 나중에 평가하기

> **TL;DR**
> 이 한 줄을 파일 맨 위에 쓰면 타입 힌트를 즉시 실행하지 않고 문자열로 저장해 둔다. 자기 자신을 참조하거나 두 모듈이 서로를 import할 때 발생하는 오류를 막는다.

---

## 이 기능을 왜 쓰는지 감 잡기

Python은 코드를 위에서 아래로 해석한다. 클래스 안에서 자기 자신을 타입 힌트로 쓰면 아직 정의되지 않았다는 `NameError`가 발생한다. 두 모듈이 서로를 import하는 순환 참조에서도 같은 문제가 생긴다. `from __future__ import annotations`는 타입 힌트를 "나중에 필요할 때 평가하겠다"고 Python에 알린다. 힌트를 실제 타입 객체 대신 문자열로 저장하기 때문에 오류가 사라진다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 타입 힌트 작성 → (즉시 평가 대신) 문자열로 저장 → 실제 검사 시점에만 평가`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 타입 힌트(type hint) | 변수나 함수의 인자·반환값에 붙이는 타입 표시. 실행에 영향 없음 |
| 지연 평가(lazy evaluation) | 값이 필요한 시점까지 계산을 미루는 방식 |
| 순환 참조(circular import) | A가 B를 import하고, B도 A를 import해 서로 의존하는 상황 |
| `__annotations__` | 클래스·함수에 달린 타입 힌트를 담은 딕셔너리 |
| PEP 563 | 이 기능을 정의한 Python 개선 제안서. Python 3.7부터 `__future__`로 사용 가능 |

## 예를 들어 설명하면

클래스가 메서드 반환 타입으로 자기 자신을 쓰는 경우다.

```python
# 이 import 없으면 NameError: name 'Person' is not defined
from __future__ import annotations

class Person:
    def clone(self) -> Person:   # 정상 동작
        return Person()
```

두 모듈 간 순환 참조도 해결된다.

```python
# module_a.py
from __future__ import annotations
from module_b import B

class A:
    def process(self, obj: B) -> A:
        return self
```

```python
# module_b.py
from __future__ import annotations
from module_a import A

class B:
    def process(self, obj: A) -> B:
        return self
```

두 파일 모두에 `from __future__ import annotations`를 추가하면 import 시점에 타입 힌트를 평가하지 않아 오류가 없다.

## 이 단계에서 중요한 판단 기준

Python 3.7~3.10을 쓰고 클래스 내 자기 참조나 순환 import가 있으면 이 한 줄을 파일 맨 위에 추가하라. Python 3.11 이상은 기본 동작이므로 불필요하다.

## 한 줄 요약 — 이것만 기억하면 된다

**`from __future__ import annotations`는 타입 힌트를 문자열로 저장해 "아직 정의 안 된 타입" 오류를 막는다.**

## 나중에 더 깊게 들어가면

- `typing.get_type_hints()`로 지연된 타입 힌트를 실제 타입 객체로 해석하기
- Python 3.11 이후 기본 동작 변경 내용(PEP 649 vs PEP 563 논쟁)
- `TYPE_CHECKING` 상수로 런타임에만 import를 제외하는 패턴

---

**원본:** [Python의 from __future__ import annotations 기능](https://memoryhub.tistory.com/474)
