# Python 타입 힌트 — 코드에 명세서 달기

> **TL;DR**
> 타입 힌트는 함수의 입력과 출력 타입을 명시해 코드 가독성을 높이고, IDE와 정적 분석 도구가 오류를 미리 잡도록 돕는다.

---

## 타입 힌트를 왜 쓰는지 감 잡기

Python은 동적 타입 언어라 변수 타입을 선언하지 않아도 코드가 동작한다. 그런데 프로젝트가 커지면 "이 함수가 문자열을 받는지 정수를 받는지" 를 코드를 열어봐야 알 수 있는 문제가 생긴다. 타입 힌트는 이 정보를 함수 정의에 직접 적어두는 방법이다.

런타임에 타입을 강제하지는 않는다. 즉 힌트를 어겨도 프로그램이 즉시 멈추지 않는다. 대신 `mypy` 같은 정적 분석 도구나 IDE가 경고를 띄워 개발 단계에서 버그를 걸러준다.

`핵심 흐름: 함수 정의 시 타입 표기 → IDE/mypy가 타입 오류 감지 → 런타임 전에 버그 수정`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 타입 힌트 | 매개변수나 반환값 옆에 적는 타입 표시. `name: str`처럼 콜론 뒤에 쓴다. |
| 반환 타입 주석 | `->` 뒤에 적는 함수 반환값의 타입. `-> int`는 정수를 돌려준다는 의미다. |
| `self` | 클래스 안의 메서드가 자기 자신을 참조할 때 쓰는 첫 번째 매개변수. |
| 정적 분석 | 코드를 실행하지 않고 타입 오류를 미리 찾는 과정. mypy가 대표 도구다. |
| 메서드 | 클래스 안에 정의된 함수. `self`를 첫 번째 인자로 받는 것이 특징이다. |

## 예를 들어 설명하면

로마 숫자를 정수로 변환하는 메서드다. 타입 힌트가 어디에 어떻게 붙는지 보면 된다.

```python
class RomanConverter:
    def romanToInt(self, s: str) -> int:
        roman_to_int = {
            'I': 1, 'V': 5, 'X': 10, 'L': 50,
            'C': 100, 'D': 500, 'M': 1000
        }
        total = 0
        prev_value = 0

        for char in reversed(s):
            current_value = roman_to_int[char]
            if current_value < prev_value:
                total -= current_value
            else:
                total += current_value
            prev_value = current_value

        return total

converter = RomanConverter()
print(converter.romanToInt("XIV"))  # 출력: 14
```

`s: str`은 입력이 문자열이어야 함을, `-> int`는 반환값이 정수임을 선언한다. IDE는 이 정보를 바탕으로 `s`에 정수를 넘기면 경고를 표시한다.

## 이 단계에서 중요한 판단 기준

팀 프로젝트나 라이브러리를 작성할 때는 타입 힌트를 빠짐없이 달아야 한다. 개인 스크립트나 빠른 실험 코드에서는 생략해도 무방하다.

## 한 줄 요약 — 이것만 기억하면 된다

**타입 힌트는 Python 코드에 붙이는 명세서로, 런타임을 강제하지 않지만 개발 단계에서 오류를 미리 잡아 유지보수 비용을 낮춘다.**

## 나중에 더 깊게 들어가면

- `Optional[str]`, `Union[int, str]` 등 복합 타입 표기법
- `typing` 모듈의 `List`, `Dict`, `Callable` 활용
- mypy를 CI 파이프라인에 통합해 타입 오류 자동 검출하기

---

**원본:** [Advanced Python Syntax (type hint) — https://memoryhub.tistory.com/171](https://memoryhub.tistory.com/171)
