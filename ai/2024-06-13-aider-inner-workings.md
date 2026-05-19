# Aider 내부 동작 원리

> **TL;DR**
> Aider는 파일을 읽어 LLM 프롬프트를 구성하고, 응답에서 코드 변경사항을 파싱해 파일에 직접 쓴 뒤 git commit을 자동으로 생성하는 Python 애플리케이션이다.

---

## Aider가 내부에서 무엇을 하는지 감 잡기

Aider를 실행하면 터미널에 채팅창이 뜨지만, 뒤에서는 Python 스크립트가 돌아가고 있다. 사용자가 "이 함수 수정해줘"라고 입력하면 Aider는 파일 내용을 프롬프트에 담아 OpenAI/Anthropic API에 전송하고, 돌아온 응답에서 코드 변경 부분을 추출해 실제 파일에 쓴다. 그리고 git commit까지 자동으로 만든다.

이 과정이 단순해 보이지만, 큰 코드베이스를 다룰 때는 `ctags`로 레포지토리 심볼 맵을 생성해 GPT가 코드 구조를 파악하도록 돕는 과정이 추가된다.

핵심 흐름: `설정 로드 → 파일 읽기 → 프롬프트 구성 → API 요청 → 응답 파싱 → 파일 수정 → git commit`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| argparse | Python 표준 라이브러리. CLI 옵션(--api-key, 파일명 등)을 파싱한다 |
| 프롬프트 구성 | 파일 내용과 사용자 요청을 하나의 텍스트로 조합해 LLM에게 보내는 작업 |
| ctags | 코드에서 함수·클래스 위치를 인덱싱하는 도구. 대형 레포에서 컨텍스트를 줄이는 데 쓰임 |
| 응답 파싱 | LLM 응답 텍스트에서 실제 코드 변경 블록만 추출하는 과정 |
| 자동 커밋 | 파일 수정 후 Aider가 자동으로 생성하는 git commit. /undo로 롤백 가능 |

## 예를 들어 설명하면

아래 의사코드는 Aider 핵심 루프를 단순화한 것이다.

```python
def main():
    api_key = load_config()["OPENAI_API_KEY"]
    files = parse_command_line_arguments()

    # 파일 내용 읽기
    file_contents = {f: open(f).read() for f in files}

    # 프롬프트 구성: 파일 내용 + 사용자 요청
    prompt = construct_prompt(file_contents, user_request)

    # LLM API 호출
    response = send_api_request(api_key, prompt)

    # 응답에서 변경사항 추출 후 파일에 적용
    changes = parse_response(response)
    for filepath, new_content in changes.items():
        open(filepath, 'w').write(new_content)

    # 변경사항을 git에 자동 커밋
    commit_changes_to_git(files)
```

실제 구현은 에러 처리, 재시도 로직, 스트리밍 응답 처리 등으로 더 복잡하지만 구조는 이와 같다.

## 이 단계에서 중요한 판단 기준

Aider는 파일 전체를 프롬프트에 포함시키기 때문에 파일이 클수록 토큰 비용이 급증한다. `/add`로 꼭 필요한 파일만 컨텍스트에 넣는 것이 비용 관리의 핵심이다.

## 한 줄 요약 — 이것만 기억하면 된다

**Aider는 파일 읽기 → LLM 호출 → 코드 파싱 → 파일 쓰기 → git commit을 자동화한 루프다.**

## 나중에 더 깊게 들어가면

- ctags 기반 레포지토리 맵이 대형 코드베이스에서 어떻게 컨텍스트 오염을 방지하는지
- Aider의 edit format(SEARCH/REPLACE 블록 vs whole-file 방식) 차이와 선택 기준
- 스트리밍 API 응답을 실시간으로 파일에 적용하는 방식

---

**원본:** [Aider Inner Workings — https://memoryhub.tistory.com/298](https://memoryhub.tistory.com/298)
