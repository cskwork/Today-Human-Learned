+++
title = "Agent of Empires — 여러 AI 코딩 에이전트를 한 번에 관리하는 대시보드"
date = "2026-05-10"
description = "tmux 세션 + Git worktree + Docker sandbox + 웹·모바일 대시보드를 묶은 AI 코딩 에이전트 운영 도구. Claude Code·Codex·Gemini·OpenCode·Pi 등을 병렬로 띄우고 브랜치별로 분리해서 검증·병합한다."
tags = ["ai", "tmux", "tool", "claude-code"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Agent of Empires(AoE)는 tmux·Git worktree·Docker sandbox·웹 대시보드를 묶어 여러 AI 코딩 CLI를 병렬로 운영하는 터미널 중심 세션 매니저다. AI 모델이 아니라 **여러 에이전트를 분리해 실행·검증·병합하는 운영 도구**다.

---

## 왜 쓰는지

AI 코딩 에이전트 사용이 "단일 질문 → 단일 답변"에서 **"병렬 작업 → 다중 검증"**으로 옮겨가면서, 브랜치·작업 디렉터리·터미널 세션·인증 정보·변경 파일 관리가 곧 작업 시간보다 더 큰 비용이 됐다. AoE는 이 운영 부담을 줄이는 데 초점이 있다.

지원하는 에이전트: Claude Code, OpenCode, Mistral Vibe, Codex CLI, Gemini CLI, Cursor CLI, Copilot CLI, Pi.dev, Factory Droid, Hermes, Kiro CLI.

GitHub: ~2.1k stars, 181 forks · 최신 릴리즈 v1.6.1 (2026-05-09).

## 핵심 흐름

```
사용자 ─ aoe (TUI)
         ├─ tmux session × N  (터미널 닫아도 유지)
         ├─ Git worktree × N  (브랜치별 작업 디렉터리)
         ├─ Docker sandbox    (선택, 컨테이너 격리)
         └─ aoe serve         (웹·모바일 대시보드)
```

| 구성 요소 | 역할 |
|---------|------|
| **tmux** | 각 에이전트를 독립 세션으로 실행. 터미널 닫아도 유지 |
| **Git worktree** | 브랜치별 디렉터리 분리. 여러 에이전트가 동일 저장소를 병렬 작업 |
| **Docker sandbox** | 세션별 컨테이너 격리. 파일·환경 오염 위험 감소 |
| **TUI/Web dashboard** | 터미널·브라우저·모바일 접근 |

## 핵심 용어 5개

| 용어 | 의미 |
|------|------|
| **세션** | tmux 세션 하나 = 에이전트 하나의 작업 공간 |
| **worktree** | 동일 저장소를 다른 디렉터리·다른 브랜치로 여는 git 기능 |
| **sandbox** | `--sandbox`로 컨테이너 안에서 세션을 실행하는 모드 |
| **serve** | 웹 대시보드 실행 명령. Tailscale/Cloudflare로 외부 접속 가능 |
| **repo config** | 저장소 단위 설정·hooks. 팀 표준화 단위 |

## 실습

### 설치

```bash
# 자동 설치 (Linux/macOS)
curl -fsSL \
  https://raw.githubusercontent.com/njbrake/agent-of-empires/main/scripts/install.sh \
  | bash

# Homebrew
brew install aoe

# 소스 빌드
git clone https://github.com/njbrake/agent-of-empires
cd agent-of-empires
cargo build --release

aoe --version
```

### 기본 실행

```bash
aoe                                # TUI
aoe add /path/to/project           # 세션 추가
aoe add --cmd claude               # Claude Code로 실행
# TUI 키: n=새 세션, Enter=attach, Ctrl+b d=tmux 빠져나가기
```

### Worktree + Sandbox

```bash
aoe add . -w feat/my-feature -b    # 새 브랜치 + worktree
aoe add --sandbox .                # Docker sandbox
aoe add --sandbox-image myregistry/custom:v1 .
```

### 웹 대시보드

```bash
aoe serve                          # 로컬
aoe serve --host 0.0.0.0           # 다른 기기에서 접근 (VPN 권장)
aoe serve --remote                 # Tailscale Funnel / Cloudflare quick tunnel
```

## 추천 조합

| 사용자 | 조합 | 이유 |
|--------|------|------|
| **개인 개발자** | TUI + Git worktree | 가장 단순 |
| **AI 코딩 실험자** | 여러 CLI + worktree | Claude/Codex/Gemini 비교 용이 |
| **팀 환경** | repo config + hooks | 공통 설정·반복 작업 표준화 |
| **보안 민감** | Docker sandbox + read-only dashboard | 환경 오염·원격 입력 위험 감소 |

## 판단 기준

### 적합 ✅

- Claude Code/Codex/Gemini/OpenCode를 이미 사용 중
- AI 에이전트를 동시에 여러 개 굴리고 싶음
- Git worktree 기반 병렬 개발에 익숙
- 터미널 중심 워크플로 선호
- 모바일에서 장시간 실행 중인 에이전트 확인이 필요

### 부적합 ⚠️

- GUI 중심 완성형 IDE 기대
- tmux·Git branch·worktree 개념이 낯섦
- Windows 네이티브만 사용 (WSL2 필요)
- AI 결과를 자동 신뢰하고 바로 병합하는 팀
- 원격 웹 대시보드 보안 정책을 관리하기 어려운 환경

## 한계와 주의점

- **초보자용 아님.** tmux·Git worktree·CLI 에이전트 경험 필수.
- **웹 대시보드 보안 위험.** 인증된 사용자는 터미널 키 입력이 가능하다.  
  위험한 설정: `0.0.0.0` 공개 Wi-Fi 노출, VPN 없이 실행, 인증 비활성화.
- **AI 품질은 사용자 책임.** AoE는 세션·디렉터리 관리만. 코드 안전성·테스트·요구사항은 별도 검증 필요.

## 도입 전 체크포인트

| 항목 | 확인 질문 |
|------|----------|
| 작업 단위 | 세션 하나가 담당할 작업 범위는? |
| 브랜치 전략 | worktree 브랜치 이름 규칙은? |
| 검증 방식 | 테스트·lint·type-check 실행 시점은? |
| 보안 | 웹 대시보드 접속 권한·위치는? |
| 인증 정보 | sandbox에 어떤 credential 공유? |
| 삭제 정책 | 세션·worktree 정리 시점은? |
| 팀 표준 | repo config·hooks를 버전 관리에 포함? |

**팀 도입 시 핵심**: "무엇을 해도 되는지"보다 **"무엇을 하면 안 되는지"를 먼저 정의**한다.

## 마치며

> "AI 에이전트 시대의 핵심은 많이 실행하는 것이 아니라, 분리해서 실행하고 검증하고 안전하게 병합하는 것."

도입 순서는 TUI → worktree → Diff View → sandbox → web dashboard. 작게 시작해야 운영 비용이 폭발하지 않는다.

---

## 핵심 요약

- **AoE = tmux 기반 세션 매니저**: 여러 AI 에이전트를 Git worktree로 브랜치 분리해 병렬 관리
- **장점**: 병렬 작업·세션 지속성·웹/모바일 대시보드·Docker 격리 / **단점**: 초보자 부적합, Windows 제한, 웹 대시보드 보안 주의
- **최적 워크플로**: 작은 작업 단위 → worktree 분리 → Diff View + 테스트로 검증 → 안전하게 병합

## 참고

- 원문: [Agent of Empires, 여러 AI 코딩 에이전트를 관리하는 대시보드](https://memoryhub.tistory.com/entry/%F0%9F%A4%96-Agent-of-Empires-%EC%97%AC%EB%9F%AC-AI-%EC%BD%94%EB%94%A9-%EC%97%90%EC%9D%B4%EC%A0%84%ED%8A%B8%EB%A5%BC-%ED%95%9C-%EB%B2%88%EC%97%90-%EA%B4%80%EB%A6%AC%ED%95%98%EB%8A%94-%EB%8F%84%EA%B5%AC%EC%9D%BC%EA%B9%8C)
- [njbrake/agent-of-empires](https://github.com/njbrake/agent-of-empires)
