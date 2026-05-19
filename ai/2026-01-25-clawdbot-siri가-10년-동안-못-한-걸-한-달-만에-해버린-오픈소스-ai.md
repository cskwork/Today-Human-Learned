# Clawdbot: 대화만 하는 AI가 아니라 실제로 일하는 AI

> **TL;DR**
> Clawdbot은 내 컴퓨터에 상주하며 WhatsApp, Telegram 등으로 지시를 받아 이메일, 캘린더, 파일 관리를 실제로 수행하는 오픈소스 AI 에이전트다.

---

## Clawdbot을 왜 쓰는지 감 잡기

ChatGPT나 Claude에게 "이메일 보내줘"라고 하면 "이렇게 보내면 됩니다"라는 설명만 돌아온다. Siri는 2011년부터 있었지만 2026년인 지금도 "오늘 회의 자료 정리해서 팀에 공유해줘"는 처리하지 못한다.

Clawdbot은 다르다. 내 컴퓨터에서 직접 실행되며 실제로 파일을 열고, 이메일을 보내고, 캘린더를 수정한다. iOS 개발 커뮤니티에서 PSPDFKit으로 알려진 Peter Steinberger가 만든 이 오픈소스 프로젝트는 출시 한 달 만에 GitHub 스타 8,000개를 돌파했다.

핵심 차이는 실행 위치다. 클라우드 서버가 아니라 내 컴퓨터에서 돌아가기 때문에 내 파일, 앱, 터미널에 직접 접근할 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 메시징 앱으로 지시 → Gateway 수신 → Agent가 LLM 판단 → Skills가 실제 작업 실행 → 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Gateway | Telegram, WhatsApp 등 메시징 앱과 연결되는 제어 허브. 24시간 데몬으로 실행됨 |
| Agent | 사용자 지시를 받아 LLM(Claude, GPT 등)으로 판단하고 실행하는 핵심 컴포넌트 |
| Skills | 기능 확장 모듈. 커뮤니티가 만든 것을 설치하거나 직접 만들 수 있음 |
| Memory | 대화 기록과 사용자 선호도를 로컬 마크다운 파일로 저장하는 구조 |
| DM 정책 (pairing 모드) | 허가된 사용자만 Clawdbot에 접근할 수 있도록 제한하는 보안 설정 |

## 예를 들어 설명하면

매일 아침 자동 브리핑을 받고 싶다면: Google 캘린더의 오늘 일정, Notion 할 일 목록, Todoist 우선순위 작업을 Clawdbot이 cron job으로 수집해 텔레그램 메시지로 보내준다. 텍스트와 TTS 음성 버전 모두 가능하다.

기본 설치는 Node.js 22 이상 환경에서 다음으로 시작한다:

```bash
npm install -g clawdbot
clawdbot onboard --install-daemon
```

온보딩 위자드가 LLM 제공자(Anthropic, OpenAI, Google 등), 메시징 채널, 기본 스킬 설정을 안내한다. 텔레그램 연결은 BotFather에서 봇 토큰을 받아 입력하면 된다.

## 이 단계에서 중요한 판단 기준

Clawdbot은 컴퓨터에 광범위한 접근 권한을 갖는다. 신뢰할 수 있는 네트워크에서만 사용하고 DM 정책을 반드시 pairing 모드로 설정한다. API 비용도 주의해야 한다. 경량 모델(GPT-4o-mini, Claude Haiku)을 기본값으로 쓰거나 Ollama 같은 로컬 모델을 활용하면 비용을 크게 줄일 수 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**Clawdbot은 AI가 대화 상자 밖으로 나와 내 컴퓨터에서 실제 작업을 수행하는 첫 번째 실용적 오픈소스 개인 에이전트다.**

## 나중에 더 깊게 들어가면

- 커스텀 Skills 작성 방법과 ClawdHub 커뮤니티 활용
- 로컬 LLM(Ollama)을 백엔드로 연결해 API 비용 없이 운영하기
- 여러 컴퓨터에 Clawdbot을 분산 배포하는 멀티 노드 구성

---

**원본:** Clawdbot, Siri가 10년 동안 못 한 걸 한 달 만에 해버린 오픈소스 AI — [https://memoryhub.tistory.com/990](https://memoryhub.tistory.com/990)
