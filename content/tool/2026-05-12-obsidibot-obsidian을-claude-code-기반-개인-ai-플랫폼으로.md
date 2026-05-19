+++
title = "ObsidiBot — Obsidian을 Claude Code 기반 개인 AI 플랫폼으로 바꾸는 플러그인"
date = "2026-05-12"
description = "Claude Code CLI를 Obsidian vault에 직접 붙여 노트 읽기·쓰기·정리, Skill 기반 자동화, 권한 모드 분리를 지원하는 베타 플러그인. 채팅 윈도우가 아니라 vault를 작업 공간으로 만든다."
tags = ["obsidian", "claude-code", "tool"]
categories = ["tool"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> ObsidiBot은 Claude Code CLI를 Obsidian 안에 통합하는 베타 플러그인이다. 노트 읽기·쓰기·생성·정리와 Skill 기반 자동화를 지원하고, Read-only/Standard/Full Access 권한 모드로 위험을 분리한다.

---

## 왜 쓰는지

Obsidian은 메모를 잘 쌓아두는 도구지만, 쌓인 노트를 **자동으로 정리·요약·재구성**하는 것은 여전히 손으로 해야 한다. ObsidiBot은 채팅 윈도우를 하나 더 띄우는 방식이 아니라, **Claude가 vault 자체를 직접 다루도록** 만든다.

## 핵심 흐름

```
사용자 ─ Obsidian
        └─ ObsidiBot 패널
              └─ Claude Code CLI
                    ├─ vault 노트 read/write/create/move
                    ├─ Obsidian 명령 실행 (Daily Note, Dataview…)
                    └─ Skill (마크다운 정의된 작업)
```

- **Claude Code CLI 기반**: Claude Pro/Max 구독으로 동작. 별도 API 키·과금 없음.
- **Skills**: YAML frontmatter + 마크다운으로 작업 정의. `/` 메뉴, Command Palette에서 실행.
- **권한 모드**: Read-only / Standard / Full Access를 세션별로 선택.

## 핵심 용어 5개

| 용어 | 의미 |
|------|------|
| **Vault** | Obsidian이 노트를 보관하는 폴더. ObsidiBot이 다루는 작업 단위. |
| **Skill** | 마크다운으로 정의한 반복 작업 (주간 리뷰·요약·블로그 초안 등). |
| **BRAT** | 베타 플러그인을 설치하는 커뮤니티 플러그인. ObsidiBot 설치 경로. |
| **Permission Mode** | 읽기 전용 / 제한적 쓰기 / 전체 접근의 3단계. |
| **Claude Code CLI** | 터미널에서 Claude를 호출하는 공식 CLI. ObsidiBot의 백엔드. |

## 설치 예시

### Claude Code 설치

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### BRAT으로 베타 추가

BRAT 플러그인에서 `ScottKirvan/ObsidiBot` 추가, 또는 릴리즈 zip을 `.obsidian/plugins/obsidibot/`에 수동 설치.

### 활성화

1. Claude Code 로그인
2. BRAT에서 ObsidiBot 추가
3. Community Plugins에서 활성화
4. **"ObsidiBot: Open agent panel"** 실행
5. Read-only 모드로 시작

## 추천 사용 조합

| 대상 | 조합 |
|------|------|
| **초보자** | 테스트 vault + Read-only + 간단한 요약 Skill |
| **Git 사용자** | Obsidian Git + ObsidiBot Skills + 주간 리뷰 |
| **글쓰기 중심** | "초안 생성", "블로그 구조화", "주제별 묶기" Skill |
| **개발자** | GitHub 이슈 작성, 프로젝트 문서 정리, 코드베이스 설계 노트 |

## 판단 기준

- ✅ Obsidian을 지식관리 OS처럼 쓰고 있다
- ✅ Claude Code를 이미 사용 중이다
- ✅ 터미널/에이전트 기반 AI 사용에 익숙하다
- ❌ 모바일 중심으로 Obsidian을 쓴다 (데스크탑만 지원)
- ❌ vault에 민감 자료가 들어 있고 AI 수정이 불안하다
- ❌ Claude Code 설치·인증이 부담스럽다

## 한계와 주의점

- **베타 단계.** Public beta, Community Plugin 등록 검토 중. 실무 메인 vault보다 테스트 vault 권장.
- **Claude Code CLI 의존성.** 별도 설치·인증 필요. 비사용자에게는 진입장벽.
- **모바일 미지원.** Obsidian desktop만 동작.
- **권한 관리 리스크.** 예상치 못한 변경 가능. 백업·Git 연동 필수.
- **빠른 개발 속도.** 사용법·구조가 자주 바뀐다. Changelog 모니터링 필요.

## 도입 전 체크리스트

1. **vault 백업** — Git/Obsidian Sync/수동 백업 중 최소 하나
2. **권한은 보수적으로** — Read-only로 시작해 점진적 확대
3. **Claude Code CLI 정상 동작 확인** — 인증·경로·OS 환경
4. **테스트 vault에서 먼저 검증** — 실 vault 사본에서 시험
5. **Changelog 모니터링** — 빠른 업데이트 추적

## 마치며

ObsidiBot의 메시지는 명확하다. **"AI를 Obsidian 위에 띄우지 말고, vault 안에서 일하게 만들어라."** 베타 단계이므로 Read-only 권한 + 테스트 vault + Git 백업 3종 세트로 시작하는 것이 안전하다.

---

## 핵심 요약

- **강력한 통합**: Claude Code가 Obsidian vault를 직접 다루며 노트 자동화와 Skill 기반 워크플로 실현
- **베타 주의**: 테스트 vault + Read-only부터 시작해 권한을 점진적으로 확대
- **타깃 사용자**: 지식관리 자동화를 추구하고 Claude Code에 익숙한 Obsidian 고급 사용자

## 참고

- 원문: [ObsidiBot, Obsidian을 Claude Code 기반 개인 AI 플랫폼으로 바꾸는 플러그인](https://memoryhub.tistory.com/entry/%F0%9F%A7%A0-ObsidiBot-Obsidian%EC%9D%84-Claude-Code-%EA%B8%B0%EB%B0%98-%EA%B0%9C%EC%9D%B8-AI-%ED%94%8C%EB%9E%AB%ED%8F%BC%EC%9C%BC%EB%A1%9C-%EB%B0%94%EA%BE%B8%EB%8A%94-%ED%94%8C%EB%9F%AC%EA%B7%B8%EC%9D%B8)
- [ScottKirvan/ObsidiBot](https://github.com/ScottKirvan/ObsidiBot)
- [공식 문서](https://www.scottkirvan.com/ObsidiBot/)
