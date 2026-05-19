+++
title = "Hermes Agent, 왜 다들 주목할까? 학습하는 에이전트 핵심 정리"
date = "2026-04-07"
description = "Hermes Agent는 단순 챗봇 래퍼가 아니라, 장기 기억·스킬 시스템·멀티채널 게이트웨이·실행 격리를 한 덩어리로 묶은 오픈소스 에이전트 런타임이다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Hermes Agent는 단순 챗봇 래퍼가 아니라, 장기 기억·스킬 시스템·멀티채널 게이트웨이·실행 격리를 한 덩어리로 묶은 오픈소스 에이전트 런타임이다.

---

## Hermes Agent가 무엇인지 감 잡기

오픈소스 AI 에이전트 저장소는 많지만 대부분 "툴 호출 된다", "채팅 된다" 수준에서 그친다. 실제로 오래 쓰려면 세 가지가 더 필요하다. 첫째, 대화 기록이 세션을 넘어 남아야 한다. 둘째, 채널을 바꿔도 같은 에이전트가 이어져야 한다. 셋째, 외부 명령 실행이 격리되어야 안전하다.

Hermes Agent는 이 세 가지를 중심 설계로 삼은 프로젝트다. NousResearch가 공개했으며 2026년 4월 기준 약 27.9k stars를 기록하고 있다. MIT 라이선스, Python 3.11 이상이 필요하다. v0.5~v0.7이 2026년 3월 말에서 4월 초 사이에 연달아 출시되었을 만큼 업데이트 속도도 빠르다.

핵심 흐름: `대화 입력 → AIAgent 중심 → 도구 실행 + 메모리 기록 + 스킬 적용 → CLI/게이트웨이 출력`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 스킬 시스템 | 반복 작업을 패턴으로 저장해 다음에 재사용하는 학습 메커니즘 |
| 메모리 | 대화·선호도·컨텍스트를 로컬에 지속 저장해 세션 간 맥락을 유지하는 기능 |
| 게이트웨이 | Telegram, Discord, Slack 등 메시징 채널로 같은 에이전트를 연결하는 어댑터 |
| 격리형 백엔드 | Docker, SSH, Modal처럼 명령 실행을 호스트와 분리하는 환경 |
| 승인 모드 | manual / smart / off 세 단계로 위험 명령의 자동 실행 여부를 제어하는 보안 설정 |

## 예를 들어 설명하면

로컬에서 시작해 운영 환경으로 확장하는 표준 경로는 이렇다.

```bash
# 설치
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
source ~/.bashrc

# 모델과 도구 설정
hermes model
hermes tools

# CLI로 첫 대화
hermes

# 운영 전환: 격리형 백엔드로 변경
hermes config set terminal.backend docker

# 메시징 채널 연결
hermes gateway setup
hermes gateway start
```

모든 대화·메모리·스킬은 기본적으로 로컬 `~/.hermes/` 아래에 저장되고 텔레메트리를 수집하지 않는다.

## 이 단계에서 중요한 판단 기준

로컬 백엔드는 호스트 권한 위에서 실행되므로 실험 범위를 작게 잡을 때만 적합하다. 자동화나 실제 운영을 시작하는 순간 Docker 같은 격리형 백엔드로 전환하는 것이 기본 원칙이다.

## 한 줄 요약 — 이것만 기억하면 된다

**체험은 CLI로 시작하고, 운영은 격리형 백엔드와 프로필 분리부터 잡는다.**

## 나중에 더 깊게 들어가면

- 스킬 시스템에서 경험이 어떻게 재사용 가능한 패턴으로 변환되는지
- 멀티 프로필 설정으로 개인용과 팀용 에이전트를 분리 운영하는 방법
- MCP server mode를 통해 외부 도구와 연동하는 아키텍처

---

**원본:** [Hermes Agent, 왜 다들 주목할까? 학습하는 에이전트 핵심 정리](https://memoryhub.tistory.com/1051)
