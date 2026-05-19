+++
title = "symphony-multi-agent — Codex·Claude Code·Gemini·Pi를 한 보드에서 굴리는 오케스트레이터"
date = "2026-05-10"
description = "OpenAI Symphony를 멀티 백엔드로 확장한 실험 도구. 파일/Linear 기반 Kanban + 터미널 TUI로 여러 AI 코딩 CLI를 병렬 실행한다. v0.1.0 초기 단계라 로컬 실험에 적합."
tags = ["ai", "claude-code", "codex", "gemini", "agent"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> symphony-multi-agent는 OpenAI Symphony를 확장해 Codex·Claude Code·Gemini·Pi를 하나의 터미널 Kanban에서 관리하는 실험 도구다. v0.1.0(2026-05-09) 초기 단계라 프로덕션보다 로컬 실험용으로 적합하다.

---

## 왜 쓰는지

AI 코딩이 "한 번 묻고 한 번 답받는" 방식에서 "작업을 맡기고 결과를 검토하는" 방식으로 옮겨가면서, **여러 에이전트를 동시에 돌릴 때 누가 무엇을 하는지 추적하는 일**이 새 병목이 됐다. OpenAI 내부 팀이 Symphony 도입 후 3주 만에 landed PR이 500% 증가했다는 보고는 이 방향의 가능성을 보여준다.

symphony-multi-agent는 같은 아이디어를 **하나의 CLI가 아니라 여러 CLI에 적용**한다. "이슈 트래커를 에이전트 실행의 컨트롤 플레인으로 쓴다"는 발상이다.

## 핵심 흐름

```
WORKFLOW.md (config)
   ├─ tracker.kind: file | linear
   ├─ agent.kind: codex | claude | gemini | pi
   └─ hooks: after_create / before_run / after_run

티켓 생성 → 오케스트레이터 감지 → 워크스페이스 생성 → CLI 에이전트 실행
                                                       ↓
                                            Kanban TUI에 상태 반영
                                            (Todo / In Progress / Review / Done)
```

`AgentBackend` 계층이 CLI별 차이(`codex app-server`, `claude -p --output-format stream-json`, `gemini -p ""`, `pi --mode json -p ""`)를 정규화한다.

## 핵심 용어 5개

| 용어 | 의미 |
|------|------|
| **AgentBackend** | 각 CLI의 세션·이벤트 차이를 정규화하는 추상 계층 |
| **WORKFLOW.md** | tracker·agent·hooks를 정의하는 설정 파일 |
| **파일 Kanban** | Linear 없이 마크다운으로 보드를 시작할 수 있는 모드 |
| **TUI** | `symphony tui ./WORKFLOW.md`로 띄우는 터미널 Kanban 뷰 |
| **doctor** | 포트·CLI 경로·권한·placeholder URL 등 초기 실패 원인을 점검하는 명령 |

## 실습

### 설치

```bash
git clone https://github.com/cskwork/symphony-multi-agent.git
cd symphony-multi-agent
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

### 파일 기반 보드 시작

```bash
symphony board init ./kanban
symphony board new TASK-1 "Fix flaky pagination test" --priority 2
symphony doctor ./WORKFLOW.md
symphony tui ./WORKFLOW.md
```

## 장점과 한계

| 항목 | 내용 |
|------|------|
| **에이전트 비교 실험** | 동일 구조로 Codex·Claude·Gemini·Pi 비교 가능 |
| **티켓 중심** | 채팅이 아니라 보드 기반 작업 흐름 — 버그 수정·테스트·문서화에 적합 |
| **로컬 파일 기반** | Linear 없이 마크다운으로 시작 — 초기 진입 장벽 낮음 |
| **경량 관찰성** | TUI + JSON API(`/api/v1/state`, `/api/v1/<id>`) |

### 한계

- **v0.1.0 초기 단계** (37 commits). 팀 프로덕션보다 실험물에 가깝다.
- **TUI 읽기 전용** — 드래그앤드롭·드릴다운·로그 tailing 미지원.
- **백엔드별 연속성 차이** — Codex는 멀티턴이 안정적, Gemini는 안정된 세션 모델이 없어 turn이 독립적이고 토큰 보고도 불안정.
- **운영 안전성** — OpenAI Symphony 자체가 "trusted environments" 전제. 실제 저장소 적용 전 권한·브랜치·테스트·롤백·비용 제한 설계 필수.

## 판단 기준

| 대상 | 조합 |
|------|------|
| **개인 개발자** | 파일 Kanban + Claude Code/Pi + 로컬 테스트 저장소 |
| **팀 단위** | Linear/파일 보드 + Codex/Claude Code + CI 테스트 + PR 리뷰 규칙 |
| **대형 코드베이스** | 조사·리팩터링·테스트 누락 탐색·마이그레이션 계획부터 시작 |

### 도입 전 체크포인트

1. **권한 분리** — 작업 디렉터리·브랜치·환경 변수·API 키·배포 권한
2. **티켓 명확성** — 작업 단위 세분화, 완료 기준 정의
3. **자동 검증** — pytest·npm test·lint·type check·smoke test
4. **백엔드 차이 인정** — CLI별 세션 모델·이벤트 포맷·토큰 보고가 다름
5. **비용 관리** — 초기에는 `max_concurrent_agents`를 낮게

## 마치며

symphony-multi-agent의 가치는 "AI 에이전트 하나를 잘 쓰는 법"이 아니라 **"여러 작업을 티켓 단위로 나누고 에이전트가 병렬 처리하는 운영 방식"**에 있다. Mock → 테스트 저장소(Claude Code/Codex) → 명확한 티켓 + 자동 검증 순으로 단계적으로 도입하면 위험이 작다.

---

## 핵심 요약

- **아키텍처**: Codex·Claude Code·Gemini·Pi를 `AgentBackend`로 정규화해 파일/Linear Kanban에서 통합 관리
- **강점/약점**: 로컬 TUI·파일 보드·다중 에이전트 실험은 강점, v0.1.0 + 읽기 전용 TUI + 백엔드 안정성 편차는 약점
- **실습 경로**: Mock → 테스트 저장소(Claude Code/Codex) → 명확한 티켓 + CI 검증 → 팀 운영(권한·비용 관리 필수)

## 참고

- 원문: [symphony-multi-agent AI 코딩 오케스트레이터](https://memoryhub.tistory.com/entry/%F0%9F%8E%BC-symphony-multi-agent-Codex%C2%B7Claude-Code%C2%B7Gemini%C2%B7Pi-AI-%EC%BD%94%EB%94%A9-%EC%98%A4%EC%BC%80%EC%8A%A4%ED%8A%B8%EB%A0%88%EC%9D%B4%ED%84%B0)
- [cskwork/symphony-multi-agent](https://github.com/cskwork/symphony-multi-agent)
