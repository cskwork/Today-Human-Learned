# Aider 저장소 구조 — 내부가 어떻게 생겼는지 감 잡기

> **TL;DR**
> Aider의 핵심은 `main.py`(진입점), `commands.py`(명령 처리), `coders/`(코드 편집 전략) 세 축으로 이루어져 있다.

---

## Aider 저장소 구조를 왜 파악해야 하는지 감 잡기

Aider는 LLM과 대화하며 로컬 git 저장소의 코드를 직접 편집하는 도구다. 기여하거나 동작을 커스터마이즈하려면 어느 파일이 무슨 일을 하는지 알아야 한다. 구조를 먼저 파악하면 버그를 찾거나 기능을 추가할 때 어디서 시작할지 바로 알 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`사용자 입력 → main.py → commands.py → coders/ → git 저장소 파일 수정`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| `main.py` | 프로그램 시작점. 인자 파싱, 모델 로딩, 실행 흐름 제어를 담당한다 |
| `commands.py` | `/add`, `/commit`, `/diff` 같은 채팅 명령어 로직이 모여 있는 파일 |
| `coders/` | LLM 응답을 실제 파일 변경으로 바꾸는 전략 클래스들의 디렉토리 |
| `base_coder.py` | 모든 코더 클래스가 상속하는 기반 클래스. 파일 읽기/쓰기 공통 로직 포함 |
| `editblock_coder.py` | 파일 전체가 아닌 변경된 블록만 정밀하게 수정하는 코더 구현체 |

## 예를 들어 설명하면

저장소 최상위 구조를 도식화하면 다음과 같다.

```
aider/
├── main.py              # 진입점
├── commands.py          # 채팅 명령 처리
├── coders/
│   ├── base_coder.py    # 공통 기반 클래스
│   ├── editblock_coder.py  # 블록 단위 편집
│   └── wholefile_coder.py  # 파일 전체 교체
├── tests/
│   ├── test_main.py
│   └── test_commands.py
benchmark/               # 성능 측정 스크립트
website/                 # 문서 사이트 소스
```

`coders/`에 편집 전략이 분리된 이유는, LLM마다 응답 형식이 다르기 때문이다. GPT-4는 블록 편집(`editblock_coder`)을, 일부 모델은 파일 전체 재생성(`wholefile_coder`)을 더 잘 처리한다. Aider는 모델에 맞는 코더를 자동으로 선택한다.

## 이 단계에서 중요한 판단 기준

새로운 LLM 응답 형식을 지원하고 싶다면 `base_coder.py`를 상속하는 새 클래스를 `coders/`에 추가하는 것이 올바른 진입점이다. `main.py`를 직접 수정하는 건 범위가 너무 넓다.

## 한 줄 요약 — 이것만 기억하면 된다

**Aider의 핵심 흐름은 `main.py` → `commands.py` → `coders/`이며, 편집 전략은 `coders/` 안에 전략 패턴으로 분리되어 있다.**

## 나중에 더 깊게 들어가면

- `wholefile_coder.py`와 `editblock_coder.py`의 응답 파싱 방식 차이
- Aider가 모델별로 코더를 자동 선택하는 로직 (`main.py` 내부)
- `benchmark/` 디렉토리의 SWE-Bench 벤치마크 실행 방법

---

**원본:** [Aider Repository Structure — https://memoryhub.tistory.com/302](https://memoryhub.tistory.com/302)
