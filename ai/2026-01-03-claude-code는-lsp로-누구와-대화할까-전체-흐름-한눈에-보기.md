# Claude Code는 LSP로 누구와 대화할까 — 전체 흐름 한눈에 보기

> **TL;DR**
> Claude Code는 자연어 처리는 Anthropic 클라우드에, 코드 구조 분석은 내 컴퓨터의 Language Server에 나눠서 물어본다. LSP는 그 둘 사이의 대화 규칙이다.

---

## LSP를 왜 쓰는지 감 잡기

Claude Code에 "이 함수 고쳐줘"라고 말하면 뒤에서 여러 구성요소가 대화를 주고받는다. 그런데 이게 어디서 어디로 가는 건지 처음에는 헷갈린다.

핵심부터 말하면, Claude Code는 코드를 이해할 때 내 컴퓨터에서 돌아가는 Language Server라는 프로그램에 물어본다. Anthropic 서버가 아니라 로컬 프로그램이다. 자연어("이 함수 고쳐줘")는 클라우드의 Claude AI가 처리하고, 코드 구조("이 함수가 어디 정의됐지?")는 Language Server가 처리한다.

이 분리가 중요한 이유는 두 가지다. 코드 분석이 빠르고 정확해진다. 그리고 코드 자체는 외부로 나가지 않는다.

`핵심 흐름: 사용자 질문 → Claude Code → [Claude AI(클라우드): 자연어 이해] + [Language Server(로컬): 코드 구조 분석] → 결과 종합 → 사용자`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Language Server | 특정 프로그래밍 언어를 분석하는 로컬 프로그램. Python이면 pyright, Go면 gopls |
| LSP (Language Server Protocol) | Claude Code와 Language Server가 대화하는 표준 규칙. Microsoft가 2016년 만들었다 |
| textDocument/definition | "이 심볼의 정의가 어디야?"라고 물어보는 LSP 요청 |
| textDocument/references | "이 심볼이 어디서 사용돼?"라고 물어보는 LSP 요청 |
| 시맨틱 코드 분석 | 텍스트 검색이 아니라 코드의 구조와 의미를 이해해서 분석하는 방식 |

## 예를 들어 설명하면

"calculateTotal 함수 찾아서 버그 고쳐줘"라고 입력하면 다음 순서로 진행된다.

```
1. 사용자 → Claude Code: "calculateTotal 함수 찾아서 버그 고쳐줘"

2. Claude Code → Claude AI (클라우드):
   사용자 요청 전달 → AI: "함수 위치를 먼저 파악해야겠군"

3. Claude Code → Language Server (로컬, LSP 요청):
   "calculateTotal 정의가 어디야?" (textDocument/definition)
   Language Server: "src/billing.py 42번째 줄"

4. Claude Code → Language Server (추가 LSP 요청):
   "이 함수 어디서 호출돼?" (textDocument/references)
   Language Server: "3군데 - main.py:15, api.py:88, test.py:23"
   "타입 에러 있어?" (textDocument/diagnostics)
   Language Server: "billing.py:45에서 str을 int로 더하려고 함"

5. Claude AI가 정보 종합 → 수정 코드 생성 → 사용자에게 전달
```

LSP 통신은 내 컴퓨터 안에서 끝난다. 코드가 Anthropic 서버로 올라가지 않는다.

## 이 단계에서 중요한 판단 기준

LSP 연결이 안 되어 있으면 Claude Code는 "눈 감고" 일한다. 함수 찾기는 grep 텍스트 검색으로 대체되고, 타입 확인은 코드를 읽고 추측하는 수준이 된다. 정확도와 속도 모두 크게 떨어진다.

| 언어 | Language Server | 설치 |
|---|---|---|
| Python | pyright | `npm install -g pyright` |
| TypeScript/JS | vtsls | `npm install -g @vtsls/language-server` |
| Go | gopls | `go install golang.org/x/tools/gopls@latest` |
| Rust | rust-analyzer | `rustup component add rust-analyzer` |

## 한 줄 요약 — 이것만 기억하면 된다

**Claude Code는 두 곳과 대화한다 — 자연어는 클라우드 Claude AI에, 코드 구조는 로컬 Language Server에. LSP는 그 두 번째 대화의 규칙이다.**

## 나중에 더 깊게 들어가면

- Claude Code에서 LSP를 활성화하는 구체적인 설정 방법 (ENABLE_LSP_TOOL 환경변수)
- 언어별 Language Server 플러그인 설치와 트러블슈팅
- LSP 프로토콜 메시지 구조와 JSON-RPC 기반 통신 방식

---

**원본:** Claude Code는 LSP로 누구와 대화할까? 전체 흐름 한눈에 보기 — [https://memoryhub.tistory.com/960](https://memoryhub.tistory.com/960)
