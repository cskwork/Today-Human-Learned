# Gemini Code Assist

> **TL;DR**
> 구글이 2025년 무료로 공개한 AI 코딩 어시스턴트로, 월 18만 회 코드 완성을 제공한다. GitHub Copilot 무료 플랜(월 2천 회)의 90배 수준이다.

---

## Gemini Code Assist를 왜 쓰는지 감 잡기

AI 코딩 도구의 가장 큰 장벽은 비용이었다. GitHub Copilot은 무료 플랜의 월 한도가 2,000회로 실제 개발에 쓰기엔 빠듯하다. 구글은 Gemini Code Assist를 무료로 풀면서 한도를 월 18만 회로 설정했다. 하루 6,000회, 시간당 250회 수준이다.

VS Code와 JetBrains IDE에 확장 프로그램으로 설치한다. 개인 Gmail 계정으로 로그인하면 바로 쓸 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: IDE 설치 → Google 계정 로그인 → 코드 작성 중 자동 완성 + 채팅 활용`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Gemini Code Assist | VS Code·JetBrains에서 쓰는 구글의 AI 코딩 어시스턴트 |
| 코드 완성 | 코드를 입력하는 동안 다음 줄이나 블록을 자동으로 제안하는 기능 |
| Agent 모드 | 단일 프롬프트로 여러 파일을 동시에 수정하는 멀티파일 편집 기능 |
| 컨텍스트 윈도우 | AI가 한 번에 읽을 수 있는 코드·대화의 양; Gemini Code Assist는 최대 128,000 토큰 지원 |
| Smart Action | 코드 블록을 선택한 뒤 오류 수정·설명·리팩토링을 단축키로 요청하는 기능 |

## 예를 들어 설명하면

VS Code에서 `def`만 입력하면 Gemini가 함수 전체를 제안한다.

```python
# Python 파일에서 'def' 입력 후 자동 제안 예시
def create_storage_bucket(bucket_name: str) -> None:
    """Google Cloud Storage 버킷을 생성한다."""
    from google.cloud import storage
    client = storage.Client()
    client.create_bucket(bucket_name)
```

단축키 `Cmd+I` (macOS) 또는 `Ctrl+I` (Windows/Linux)로 인라인 명령 창을 열 수 있다. `/fix`로 버그 수정, `/generate`로 새 코드 생성, `/explain`으로 코드 설명을 요청한다.

GitHub 앱을 연결하면 PR이 열릴 때 자동으로 코드 리뷰 요약이 달린다.

## 이 단계에서 중요한 판단 기준

AI가 생성한 코드는 반드시 검토한다. 무료 한도가 넉넉하다고 검증 없이 병합하면 오히려 기술 부채가 쌓인다.

## 한 줄 요약 — 이것만 기억하면 된다

**Gemini Code Assist는 무료 한도가 실용적인 수준이므로 메인 AI 코딩 도구로 쓰면서, 생성된 코드는 반드시 개발자가 직접 검토해야 한다.**

## 나중에 더 깊게 들어가면

- Agent 모드로 대규모 리팩토링 자동화하는 방법
- GitHub 코드 리뷰 에이전트 설정 및 커스터마이징
- Gemini Code Assist Enterprise(유료) 기능과 무료 버전의 차이

---

**원본:** [Gemini Code Assist](https://memoryhub.tistory.com/723)
