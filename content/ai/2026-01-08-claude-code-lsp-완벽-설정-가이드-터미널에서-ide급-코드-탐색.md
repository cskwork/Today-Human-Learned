+++
title = "Claude Code LSP 완벽 설정 가이드 — 터미널에서 IDE급 코드 탐색"
date = "2026-01-08"
description = "`ENABLE_LSP_TOOL=1` 환경변수를 켜고 언어별 플러그인을 설치하면, Claude Code가 텍스트 grep 대신 시맨틱 코드 분석으로 함수 정의와 참조를 즉시 파악한다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> `ENABLE_LSP_TOOL=1` 환경변수를 켜고 언어별 플러그인을 설치하면, Claude Code가 텍스트 grep 대신 시맨틱 코드 분석으로 함수 정의와 참조를 즉시 파악한다.

---

## Claude Code LSP를 왜 설정해야 하는지 감 잡기

VS Code에서 함수 위에 마우스를 올리면 타입 정보가 뜨고, "정의로 이동"을 누르면 정확한 파일과 라인으로 점프한다. 이 기능의 정체가 Language Server Protocol(LSP)이다.

Claude Code 2.0.74부터 이 기능이 터미널에도 탑재됐다. LSP 없이는 "processRequest 함수가 어디 정의되어 있지?"라고 물으면 AI가 grep처럼 파일을 뒤져야 했다. 같은 이름이 주석에 있든 문자열 안에 있든 구분하지 못하고, 대규모 코드베이스에서는 45초씩 걸리기도 했다.

LSP가 켜지면 이 문제를 근본적으로 해결한다. 코드를 텍스트가 아닌 구조로 이해하기 때문이다. 응답 시간이 45초에서 50ms 수준으로 줄어든다.

`핵심 흐름: ENABLE_LSP_TOOL 활성화 → 마켓플레이스 등록 → 언어별 플러그인 설치 → LSP 서버 바이너리 PATH 확인 → 동작 검증`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| LSP | Language Server Protocol — 에디터와 언어 서버 간 표준 통신 규약. Microsoft가 2016년 만들었다 |
| Language Server | 코드의 시맨틱 구조를 분석하는 프로그램. Python은 pyright, Go는 gopls, Rust는 rust-analyzer |
| 시맨틱 분석 | 텍스트 패턴 매칭이 아니라 코드의 의미와 구조를 이해하는 분석 방식 |
| ENABLE_LSP_TOOL | Claude Code에서 LSP 기능을 켜는 환경변수. 기본값은 비활성화 |
| 플러그인 마켓플레이스 | Claude Code에서 LSP 서버를 설치·관리하는 커뮤니티 저장소 |

## 예를 들어 설명하면

설정 완료 후 Claude Code에서 "main 함수의 정의 위치를 찾아줘"라고 물으면 응답이 달라진다.

```
# LSP 정상 동작 시:
"main 함수는 src/app/main.py 파일의 42번 라인에 정의되어 있습니다."

# LSP 미동작 시:
"main이라는 이름이 포함된 파일들을 검색하고 있습니다..."
```

정확한 파일명과 라인 번호가 포함되면 LSP가 동작하는 것이다.

## 이 단계에서 중요한 판단 기준

설정은 5단계다. 순서대로 따라가되, 3단계(바이너리 설치)에서 `which pyright` 같은 명령으로 PATH를 반드시 확인한다.

**1단계: LSP 활성화**

```bash
# 영구 활성화 (~/.zshrc 또는 ~/.bashrc에 추가)
export ENABLE_LSP_TOOL=1
```

또는 `~/.claude/settings.json`으로 설정:

```json
{
  "env": {
    "ENABLE_LSP_TOOL": "1"
  }
}
```

**2단계: 플러그인 마켓플레이스 등록** (Claude Code 내에서 한 번만 실행)

```
/plugin marketplace add boostvolt/claude-code-lsps
```

**3단계: 언어별 플러그인 설치**

```
/plugin install pyright@claude-code-lsps       # Python
/plugin install vtsls@claude-code-lsps         # TypeScript/JS
/plugin install gopls@claude-code-lsps         # Go
/plugin install rust-analyzer@claude-code-lsps # Rust
```

**4단계: 바이너리 수동 설치 (자동 설치 실패 시)**

```bash
pip install pyright                              # Python
npm install -g @vtsls/language-server typescript # TypeScript
go install golang.org/x/tools/gopls@latest       # Go
rustup component add rust-analyzer               # Rust
brew install jdtls                               # Java (Java 21+ 필요)
```

**5단계: 트러블슈팅**

| 문제 | 원인 | 해결 |
|---|---|---|
| "No LSP server available for file type" | 플러그인 미설치 또는 미인식 | `/plugin` 탭 확인 후 재시작 |
| "Executable not found in $PATH" | 바이너리 경로 문제 | `which [서버명]` 확인 후 PATH 추가 |
| 설치 후 동작 안 함 | 세션 초기화 필요 | Claude Code 종료 후 재실행 |
| Windows에서 미작동 | 플랫폼 호환성 이슈 | WSL 사용 권장 |

## 한 줄 요약 — 이것만 기억하면 된다

**`export ENABLE_LSP_TOOL=1`을 셸 설정에 추가하고 자주 쓰는 언어의 플러그인 하나만 설치하면, Claude Code가 코드를 텍스트가 아닌 구조로 이해하기 시작한다.**

## 나중에 더 깊게 들어가면

- 팀 전체에 LSP 설정 배포하기 (`.claude/settings.json` 버전 관리)
- Java(jdtls) + Vue(`@vue/language-server`) 풀스택 환경 설정
- LSP 5가지 핵심 기능(goToDefinition, findReferences, documentSymbol, hover, getDiagnostics) 활용법

---

**원본:** Claude Code LSP 완벽 설정 가이드: 터미널에서 IDE급 코드 탐색 — [https://memoryhub.tistory.com/961](https://memoryhub.tistory.com/961)
