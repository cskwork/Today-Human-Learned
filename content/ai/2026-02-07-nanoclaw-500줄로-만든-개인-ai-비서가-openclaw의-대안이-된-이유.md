+++
title = "NanoClaw — 500줄로 만든 개인 AI 비서가 OpenClaw의 대안이 된 이유"
date = "2026-02-07"
description = "NanoClaw는 TypeScript 500줄짜리 경량 AI 비서로, 코드 전체를 8분 안에 읽을 수 있는 단순함과 Apple Container 기반 OS 수준 격리를 결합해 OpenClaw의 보안 문제를 우회한다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> NanoClaw는 TypeScript 500줄짜리 경량 AI 비서로, 코드 전체를 8분 안에 읽을 수 있는 단순함과 Apple Container 기반 OS 수준 격리를 결합해 OpenClaw의 보안 문제를 우회한다.

---

## NanoClaw를 왜 쓰는지 감 잡기

OpenClaw가 GitHub 스타 14만 개를 넘기며 "개인 AI 비서" 열풍을 일으켰지만, 한 개발자가 이렇게 말했다. "내 삶에 접근하는 소프트웨어를 이해하지 못한 채 잠들 수 없다." OpenClaw는 52개 모듈, 45개 이상의 의존성으로 이루어져 있다. 보안 취약점이 발생해도 어디서 왜 발생했는지 파악하기 어렵다.

NanoClaw는 이 전제에서 출발했다. 같은 핵심 기능을 제공하되, 코드베이스 전체를 8분 안에 읽고 이해할 수 있는 크기로 만든다. 소스 파일은 단 네 개다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: WhatsApp 메시지 → 트리거 확인 → 컨테이너 내 에이전트 실행 → 응답 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Apple Container | 2025년 WWDC에서 발표된 Apple의 오픈소스 컨테이너 런타임. 각 컨테이너를 독립된 경량 VM으로 실행해 Docker보다 강한 격리를 제공한다 |
| OS 수준 격리 | 코드 로직이 아니라 운영체제가 직접 프로세스를 분리해서 보안을 강제하는 방식. 소프트웨어 버그로 우회하기 어렵다 |
| 컨테이너별 독립 VM | Docker가 여러 컨테이너가 커널을 공유하는 방식이라면, Apple Container는 컨테이너마다 별도 VM을 만든다. 한 컨테이너가 침해되어도 다른 컨테이너에 영향이 없다 |
| Skills over Features | 기능을 코드에 직접 추가하는 대신 Claude Code 스킬 파일로 기여하는 NanoClaw의 확장 정책. 핵심 코드베이스가 작고 감사 가능한 상태를 유지한다 |
| 최소 마운트 | 컨테이너가 볼 수 있는 파일 시스템을 명시적으로 허가된 디렉토리로만 제한하는 보안 설계 |

## 예를 들어 설명하면

NanoClaw의 전체 아키텍처는 한 줄로 설명된다.

```
WhatsApp (baileys) → SQLite → Polling loop → Container (Claude Agent SDK) → Response
```

보안 경계를 시각화하면 이렇다.

```
[비신뢰 영역: WhatsApp 메시지]
         ↓ 트리거 확인, 입력 이스케이핑
[호스트 프로세스: 메시지 라우팅, 마운트 검증, 컨테이너 생명주기]
         ↓ 명시적 마운트만 허용
[컨테이너(격리됨): 에이전트 실행, bash, 파일 조작]
```

일반 AI 비서에서 bash 명령어를 실행하면 호스트 시스템 전체가 노출될 위험이 있다. NanoClaw에서는 bash 명령어가 컨테이너 내부에서만 실행되므로 호스트 Mac에 영향을 주지 않는다. `.ssh`, `.aws`, `.env` 등 민감한 경로는 기본적으로 차단 목록에 포함되어 있다.

OpenClaw와의 선택 기준을 정리하면 다음과 같다.

| 비교 항목 | OpenClaw | NanoClaw |
|---|---|---|
| 코드 규모 | 52+ 모듈, 45+ 의존성 | 소스 파일 4개, ~500줄 |
| 지원 채널 | 다수 (텔레그램, 왓츠앱, 디스코드 등) | WhatsApp 기본, 스킬로 확장 |
| 보안 모델 | 애플리케이션 수준 허용 목록 | OS 수준 컨테이너 격리 |
| 적합한 사용자 | 다양한 플랫폼과 LLM을 원하는 사용자 | 코드를 직접 이해하고 통제하려는 개발자 |

## 이 단계에서 중요한 판단 기준

코드 전체를 직접 읽고 이해할 수 있는 소프트웨어에만 컴퓨터 접근 권한을 줘야 한다면, NanoClaw가 현재 유일한 실용적 선택지다.

## 한 줄 요약 — 이것만 기억하면 된다

**NanoClaw는 "이해할 수 없는 소프트웨어에 내 삶을 맡기지 않는다"는 원칙을 코드 500줄로 구현한 도구다. macOS Tahoe와 Apple Silicon이 필요하다는 플랫폼 제약이 가장 큰 진입 장벽이다.**

## 나중에 더 깊게 들어가면

- Apple Container와 Docker의 격리 방식 차이 및 보안 수준 비교
- Skills over Features 기여 모델: Telegram/Slack 스킬을 직접 만드는 방법
- 알려진 한계: Anthropic 인증 정보가 에이전트 컨테이너에 마운트되는 구조의 위험성

---

**원본:** NanoClaw, 500줄로 만든 개인 AI 비서가 OpenClaw의 대안이 된 이유 — https://memoryhub.tistory.com/1011
