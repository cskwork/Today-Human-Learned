+++
title = "Claude Code Router — 모델 라우팅으로 AI 비용 줄이기"
date = "2025-07-18"
description = "Claude Code Router는 작업 종류에 따라 AI 요청을 다른 모델로 자동 분배해, 같은 품질의 결과를 훨씬 낮은 비용으로 얻게 해준다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Claude Code Router는 작업 종류에 따라 AI 요청을 다른 모델로 자동 분배해, 같은 품질의 결과를 훨씬 낮은 비용으로 얻게 해준다.

---

## Claude Code Router를 왜 쓰는지 감 잡기

AI 코딩 도구를 매일 쓰다 보면 비용이 빠르게 누적된다. 문제는 모든 요청이 같은 수준의 모델을 필요로 하지 않는다는 점이다. 단순한 자동완성에도 고가의 프리미엄 모델을 쓰는 것은 낭비다.

Claude Code Router는 이 비효율을 해결한다. 요청을 분석해 작업 유형에 맞는 모델로 자동 전달한다. 일반 코딩은 DeepSeek, 복잡한 추론은 DeepSeek Reasoner, 긴 문서 처리는 Gemini Pro로 보내는 식이다. 개발자는 Claude Code를 평소처럼 쓰면 되고, 라우터가 뒤에서 모델 선택을 담당한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 사용자 요청 → 라우터가 작업 유형 판단 → 적합한 모델로 전달 → 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 라우팅 | 요청을 어느 모델로 보낼지 결정하는 과정 — 우체부가 편지를 알맞은 집에 배달하는 것과 같다 |
| 프로바이더 | AI 모델을 제공하는 회사 — OpenAI, DeepSeek, Google 등 |
| 토큰 | AI가 텍스트를 세는 단위. 비용은 토큰 수에 비례한다 |
| 컨텍스트 | AI가 한 번에 처리할 수 있는 텍스트의 양. 모델마다 다르다 |
| config.json | 어떤 작업을 어떤 모델로 보낼지 정의하는 설정 파일 |

## 예를 들어 설명하면

설치 후 `~/.claude-code-router/config.json`을 아래처럼 작성하면 끝이다.

```json
{
  "Providers": [
    {
      "name": "deepseek",
      "api_base_url": "https://api.deepseek.com/chat/completions",
      "api_key": "sk-xxx",
      "models": ["deepseek-chat", "deepseek-reasoner"]
    },
    {
      "name": "openrouter",
      "api_base_url": "https://openrouter.ai/api/v1/chat/completions",
      "api_key": "sk-xxx",
      "models": ["google/gemini-2.5-pro"]
    }
  ],
  "Router": {
    "default": "deepseek,deepseek-chat",
    "think": "deepseek,deepseek-reasoner",
    "longContext": "openrouter,google/gemini-2.5-pro"
  }
}
```

이후 `ccr code`로 라우터를 실행하면 Claude Code가 자동으로 이 라우터를 통해 모델을 선택한다.

작업별 권장 모델은 아래와 같다.

| 작업 유형 | 추천 모델 | 이유 |
|---|---|---|
| 일반 코딩 | DeepSeek Chat | 비용 대비 코딩 성능 우수 |
| 복잡한 추론 | DeepSeek Reasoner | 추론 특화 모델 |
| 긴 컨텍스트 | Gemini 2.5 Pro | 대용량 컨텍스트 처리 가능 |

## 이 단계에서 중요한 판단 기준

모든 요청을 비싼 모델로 보내기 전에, 이 작업이 정말 깊은 추론이 필요한지 먼저 확인한다.

## 한 줄 요약 — 이것만 기억하면 된다

**작업 유형에 따라 모델을 자동 분배하면, 비용을 줄이면서도 필요한 품질을 유지할 수 있다.**

## 나중에 더 깊게 들어가면

- Ollama를 통한 로컬 모델 연동으로 API 비용 완전 제거
- `/model` 명령어로 Claude Code 실행 중 실시간 모델 전환
- 팀 전체 설정을 공유해 비용 효율 표준화하는 방법

---

**원본:** [Claude Code Router로 AI 비용 90% 절약 — https://memoryhub.tistory.com/729](https://memoryhub.tistory.com/729)
