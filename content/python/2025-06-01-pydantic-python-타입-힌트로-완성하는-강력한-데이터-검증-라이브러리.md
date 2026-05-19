+++
title = "Pydantic — 타입 힌트로 데이터를 자동 검증하기"
date = "2025-06-01"
description = "Pydantic은 클래스에 타입 힌트만 적으면 입력 데이터를 자동으로 검증하고 변환해 주는 라이브러리다. `isinstance()` 검사를 손으로 짤 필요가 없어진다."
tags = ["python"]
categories = ["python"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Pydantic은 클래스에 타입 힌트만 적으면 입력 데이터를 자동으로 검증하고 변환해 주는 라이브러리다. `isinstance()` 검사를 손으로 짤 필요가 없어진다.

---

## Pydantic을 왜 쓰는지 감 잡기

Python은 동적 타입 언어라 API로 받은 JSON이나 사용자 입력이 예상 타입과 다를 때 런타임에 터진다. 기존에는 `if not isinstance(data['id'], int)` 같은 코드를 필드마다 반복해야 했다. Pydantic은 클래스 선언 자체가 스키마가 된다. 데이터를 넣으면 검증과 타입 변환을 자동으로 처리하고, 실패하면 명확한 오류 메시지를 돌려준다. FastAPI, LLM 프레임워크, 설정 관리 등에서 표준처럼 쓰인다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 타입 힌트가 달린 클래스 정의 → 데이터 투입 → 자동 검증 및 변환 → 안전한 객체 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| `BaseModel` | 모든 Pydantic 모델이 상속하는 기본 클래스 |
| `Field` | 필드에 최솟값, 최댓값, 길이 제한 등 세부 규칙을 붙이는 함수 |
| `field_validator` | 특정 필드에 커스텀 검증 로직을 추가하는 데코레이터 |
| `ValidationError` | 검증 실패 시 Pydantic이 발생시키는 예외. 어떤 필드가 왜 실패했는지 담겨 있다 |
| 타입 강제 변환(coercion) | `"123"` 같은 문자열을 `int` 필드에 넣으면 자동으로 `123`으로 바꿔주는 동작 |

## 예를 들어 설명하면

사용자 등록 API 데이터를 검증하는 예시다.

```python
from pydantic import BaseModel, Field, field_validator
from typing import Optional

class User(BaseModel):
    id: int
    name: str = Field(min_length=1, max_length=100)
    email: str
    age: Optional[int] = Field(None, gt=0, le=150)

    @field_validator('email')
    @classmethod
    def validate_email(cls, v: str) -> str:
        if '@' not in v:
            raise ValueError('유효한 이메일 형식이 아닙니다')
        return v.lower()

# '123'(문자열)이 자동으로 123(정수)으로 변환된다
user = User(id='123', name='Kim', email='KIM@EXAMPLE.COM', age='25')
print(user)
# User(id=123, name='Kim', email='kim@example.com', age=25)
```

검증 실패 시 `ValidationError`가 발생하며 어떤 필드가 왜 틀렸는지 명시된다.

## 이 단계에서 중요한 판단 기준

외부에서 들어오는 데이터(API 요청, 설정 파일, CSV 파싱)를 Python 객체로 변환해야 한다면 Pydantic을 먼저 고려하라.

## 한 줄 요약 — 이것만 기억하면 된다

**클래스에 타입 힌트를 달면 Pydantic이 검증과 변환을 대신 해준다.**

## 나중에 더 깊게 들어가면

- 중첩 모델(`class Address(BaseModel)` 안에 `User` 필드)
- `model_config = ConfigDict(strict=True)` — 타입 강제 변환 없이 엄격 검사
- Pydantic V2의 Rust 기반 성능 개선과 V1 마이그레이션 방법

---

**원본:** [Pydantic Python - 타입 힌트로 완성하는 강력한 데이터 검증 라이브러리](https://memoryhub.tistory.com/629)
