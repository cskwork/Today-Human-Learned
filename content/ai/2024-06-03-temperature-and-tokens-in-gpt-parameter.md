+++
title = "GPT의 Temperature와 Token — 출력을 결정하는 두 가지 파라미터"
date = "2024-06-03"
description = "Temperature는 GPT 응답의 창의성(무작위성)을 조절하는 다이얼이고, Token은 GPT가 텍스트를 처리하는 최소 단위다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Temperature는 GPT 응답의 창의성(무작위성)을 조절하는 다이얼이고, Token은 GPT가 텍스트를 처리하는 최소 단위다.

---

## Temperature와 Token을 왜 알아야 하는지 감 잡기

GPT API를 처음 쓰면 결과가 너무 뻔하거나 반대로 너무 엉뚱하다는 느낌을 받는다. 그 차이를 만드는 핵심 설정이 Temperature다. 또한 API 비용이 Token 수에 따라 과금되기 때문에, Token 개념을 모르면 예상치 못한 청구서를 받을 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 입력 텍스트 → 토크나이저가 Token으로 분해 → 모델이 다음 Token 예측(Temperature 적용) → 응답 조립`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Temperature | 0에 가까울수록 가장 확률 높은 단어만 선택(예측 가능), 1에 가까울수록 낮은 확률 단어도 선택(창의적). |
| Token | GPT가 텍스트를 쪼개는 조각. 영어 단어 하나가 보통 1~2 token, 한국어는 단어 하나가 2~4 token인 경우가 많다. |
| 토크나이저 | 입력 문자열을 Token 목록으로 변환하는 전처리기. GPT 계열은 BPE(Byte Pair Encoding) 방식을 쓴다. |
| 컨텍스트 윈도우 | 한 번의 요청에서 처리할 수 있는 최대 Token 수. 이 한계를 넘으면 앞부분이 잘린다. |
| 확률 분포 | 다음에 올 단어 후보 각각에 점수를 매긴 목록. Temperature는 이 분포를 날카롭게(낮음) 또는 평탄하게(높음) 만든다. |

## 예를 들어 설명하면

같은 프롬프트 "용이 등장하는 짧은 이야기를 써줘"에 대해 Temperature 값에 따라 결과가 달라진다.

| Temperature | 출력 예시 |
|---|---|
| 0.2 (낮음) | "용이 마을 위를 날아 언덕에 내려앉았다." — 무난하고 예측 가능. |
| 0.8 (높음) | "용은 용암빛 비늘을 번쩍이며 시장 한복판에 급강하했다." — 구체적이고 독창적. |

Token 분해 예시: `"OpenAI의 GPT-4"` → `["Open", "AI", "의", " GPT", "-", "4"]` — 6 token.
이 문장을 1,000번 반복하면 6,000 token이 소비된다.

## 이 단계에서 중요한 판단 기준

용도에 따라 Temperature를 고정하라. 기술 문서·코드 생성은 0.2~0.4, 창작·아이디어 발산은 0.7~1.0이 일반적인 출발점이다.

## 한 줄 요약 — 이것만 기억하면 된다

**Temperature로 창의성을 조절하고, Token 수로 비용과 컨텍스트 한계를 관리한다.**

## 나중에 더 깊게 들어가면

- Top-p(nucleus sampling): Temperature와 함께 쓰이는 또 다른 샘플링 방법
- 한국어 Token 효율: 한국어는 영어보다 Token을 더 많이 소모하는 이유
- 컨텍스트 윈도우 한계 대응법: 긴 문서를 어떻게 쪼개 GPT에 넘길 것인가(chunking, RAG)

---

**원본:** [Temperature and Tokens in GPT parameter — memoryhub.tistory.com/182](https://memoryhub.tistory.com/182)
