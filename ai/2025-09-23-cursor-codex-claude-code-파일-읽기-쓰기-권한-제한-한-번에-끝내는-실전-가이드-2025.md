# Cursor, Codex, Claude Code: AI 에이전트 파일 권한 제한 실전 가이드

> **TL;DR**
> AI 코딩 에이전트가 어떤 파일을 읽고 쓸 수 있는지 명시적으로 제한하지 않으면, 실수 하나가 운영 환경 파일을 덮어쓰거나 비밀 키를 노출시킬 수 있다.

---

## 파일 권한 제한을 왜 쓰는지 감 잡기

AI 에이전트는 이제 에디터, 터미널, 파일 시스템을 자유롭게 건드린다. 편하지만 위험하다. `.env`에 담긴 API 키를 읽거나, 배포 스크립트를 수정하거나, `git push`를 무단으로 실행할 수 있다.

세 도구는 서로 다른 방식으로 이 문제를 다룬다.

`에이전트 실행 요청 → 권한 규칙 평가 → 허용/거부/확인 요청 → 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| .cursorignore | Cursor가 특정 파일을 아예 못 보게 막는 설정 파일. .gitignore 문법 그대로 사용 |
| 샌드박스 모드 | Codex에서 에이전트가 조작할 수 있는 범위를 단계적으로 제한하는 실행 환경 |
| 승인(approval) 모드 | 에이전트가 민감한 작업을 하기 전에 사람에게 확인을 요청하는 방식 |
| 글롭 패턴 | `./secrets/**`처럼 와일드카드로 경로를 표현하는 문법. 규칙을 묶어서 선언할 때 씀 |
| deny / ask / allow | Claude Code 권한 규칙의 세 단계. 거부 → 확인 → 허용 순서로 평가됨 |

## 예를 들어 설명하면

**Cursor**: 프로젝트 루트에 `.cursorignore`를 두면 AI가 해당 파일/폴더를 인덱싱, 채팅 컨텍스트, 자동완성에서 통째로 못 본다.

```
# .cursorignore
.env
secrets/**
**/*.pem
dist/
*.log
```

단, `.cursorignore`는 에디터 레벨의 차단이다. 터미널에서 `cat .env`를 승인하면 여전히 읽을 수 있다. 비밀 파일은 워크스페이스 밖에 두거나 OS 권한으로 추가 보호해야 한다.

**Codex**: 세션 시작 시 모드를 선택한다.

```bash
# 읽기 전용 세션 (수정·명령 실행 불가)
codex --sandbox read-only --ask-for-approval never

# 기본 모드 (워크스페이스 내 작업은 자연스럽게, 위험 작업은 승인)
codex
```

세밀한 파일 단위 denylist(`.codexignore` 등)는 아직 공식 지원되지 않는다.

**Claude Code**: `.claude/settings.json`에 규칙을 선언한다. 가장 세밀하게 제어할 수 있다.

```json
{
  "permissions": {
    "deny":  ["Read(./.env)", "Read(./secrets/**)"],
    "ask":   ["Write(./production/**)", "Bash(git push:*)"],
    "allow": ["Bash(npm run test:*)"]
  }
}
```

규칙 평가 순서: deny → allow → ask → 기타.

## 이 단계에서 중요한 판단 기준

비밀 파일(키, 인증서, 패스워드)은 어떤 도구를 쓰든 워크스페이스 밖에 두는 것이 첫 번째 방어선이고, 권한 규칙은 두 번째 방어선이다.

## 한 줄 요약 — 이것만 기억하면 된다

**읽기는 ignore 파일로 차단하고, 쓰기와 명령 실행은 승인/규칙으로 제어한다.**

## 나중에 더 깊게 들어가면

- Claude Code hooks(PreToolUse/PostToolUse)로 실행 전후 로깅·차단 로직 추가
- Codex의 파일 단위 denylist 기능 (요청 이슈 진행 중)
- OS 수준 ACL과 에이전트 권한 제한의 조합 설계

---

**원본:** Cursor, Codex, Claude Code: 파일 읽기/쓰기 권한 제한, 한 번에 끝내는 실전 가이드 (2025) — https://memoryhub.tistory.com/786
