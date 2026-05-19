+++
title = "OpenAI GPT-OSS 공개 — 오픈 웨이트 추론 모델의 등장"
date = "2025-08-06"
description = "OpenAI가 Apache 2.0 라이선스로 gpt-oss-120b와 gpt-oss-20b를 공개했다. 개인 GPU에서 실행 가능하고, 추론 성능은 각각 o4-mini, o3-mini에 근접한다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> OpenAI가 Apache 2.0 라이선스로 gpt-oss-120b와 gpt-oss-20b를 공개했다. 개인 GPU에서 실행 가능하고, 추론 성능은 각각 o4-mini, o3-mini에 근접한다.

---

## GPT-OSS를 왜 쓰는지 감 잡기

기업에서 LLM을 쓸 때 가장 큰 걸림돌은 두 가지다. 첫째, 내부 데이터를 외부 API에 보내면 기밀 유출 위험이 생긴다. 둘째, API 비용이 규모에 따라 급격히 늘어난다. GPT-2 이후 OpenAI가 언어 모델 가중치를 공개하지 않다가, 2025년 8월 이 두 문제를 직접 겨냥한 모델을 내놨다.

오픈 웨이트 모델(open-weight model)이란 모델의 내부 숫자값(가중치)을 누구나 내려받아 직접 서버에서 실행할 수 있는 모델이다. 클라우드 API 없이 자체 인프라에서 돌릴 수 있다.

`핵심 흐름: 모델 가중치 다운로드 → 로컬/온프레미스 서버에 올리기 → API 없이 추론 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 오픈 웨이트 | 모델 내부 숫자(가중치)를 공개해 누구나 로컬에서 실행 가능한 모델 형태 |
| MoE | 혼합 전문가(Mixture of Experts) — 모든 파라미터를 쓰지 않고 토큰마다 일부만 활성화해 메모리를 아끼는 구조 |
| Apache 2.0 | 상업 이용, 수정, 재배포를 모두 허용하는 오픈소스 라이선스 |
| 추론 모델 | 답을 바로 내놓는 대신 단계별 사고(Chain-of-Thought)를 거쳐 정확도를 높이는 LLM 유형 |
| reasoning_effort | 추론 깊이를 low / medium / high로 조절하는 파라미터 — 속도와 정확도의 트레이드오프 |

## 예를 들어 설명하면

두 모델의 핵심 스펙을 한눈에 보면 이렇다.

| 항목 | gpt-oss-120b | gpt-oss-20b |
|---|---|---|
| 총 파라미터 | 117B | 21B |
| 토큰당 활성 파라미터 | 5.1B | 3.6B |
| MoE 전문가 수 | 128 중 4 활성 | 32 중 4 활성 |
| 컨텍스트 길이 | 128k 토큰 | 128k 토큰 |
| 최소 권장 VRAM | 80GB (단일 GPU) | 16GB |
| 성능 비교 기준 | o4-mini 수준 | o3-mini 수준 |

120b는 NVIDIA A100 80GB 한 장으로 동작하고, 20b는 고급 소비자용 GPU에서도 실행된다. 두 모델 모두 함수 호출, CoT, 구조화 출력을 기본 지원한다.

## 이 단계에서 중요한 판단 기준

데이터를 외부로 보낼 수 없는 환경이거나 월 API 비용이 부담된다면 gpt-oss-20b로 시작하고, 더 높은 추론 성능이 필요하면 120b를 검토하라.

## 한 줄 요약 — 이것만 기억하면 된다

**gpt-oss는 OpenAI가 6년 만에 내놓은 오픈 웨이트 추론 모델로, 로컬 실행과 상업 사용이 모두 가능하다.**

## 나중에 더 깊게 들어가면

- vLLM, Ollama, llama.cpp 등 서빙 스택별 성능 차이
- gpt-oss를 이용한 에이전트 워크플로 구성 방법
- 악의적 파인튜닝 위험과 Preparedness Framework의 실제 적용 방식

---

**원본:** [OpenAI, GPT-OSS 공개 — memoryhub.tistory.com/742](https://memoryhub.tistory.com/742)
